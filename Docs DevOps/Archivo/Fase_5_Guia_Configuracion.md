# Guía de Automatización y CI/CD v2.0

  * **Proyecto:** Sistema de Monitoreo de Estrés Hídrico en Plantas de Arándano
  * **Fase:** 5 - Automatización (CI/CD)
  * **Fecha:** 2025-06-30
  * **Versión:** 1.0

## 1. Propósito

Este documento detalla la configuración y los procedimientos implementados durante la Fase 5 para establecer un sistema robusto de Integración Continua (CI) y Despliegue Continuo (CD) utilizando GitHub Actions. El objetivo es automatizar por completo el ciclo de vida de la aplicación y de la configuración de la infraestructura, migrando de un modelo de despliegue manual a uno basado en la filosofía GitOps. Esto aumenta la velocidad y fiabilidad de los despliegues, a la vez que reduce drásticamente el riesgo de error humano.

## 2. Gestión de Secretos y Permisos (Configuración Única)

La configuración de los secretos es el paso más crítico para permitir que los flujos de trabajo se comuniquen de forma segura con los recursos externos. A continuación se detallan los comandos para obtener cada valor.

### 2.1 Obtención de la Clave Privada SSH (`SSH_PRIVATE_KEY`)

Esta clave permite al workflow autenticarse en el servidor VPS. Se genera una única vez.

1.  **Comando de Generación:** Ejecuta el siguiente comando en tu terminal local para crear un par de claves dedicadas para GitHub Actions.
    ```bash
    ssh-keygen -t ed25519 -f ~/.ssh/arandano_deploy_key -C "github-actions-arandanoirt"
    ```
2.  **Comando para Obtener el Valor:** El valor para el secreto es el contenido del archivo de la clave **privada**.
    ```bash
    cat ~/.ssh/arandano_deploy_key
    ```
    Copia toda la salida, incluyendo `-----BEGIN OPENSSH PRIVATE KEY-----` y `-----END OPENSSH PRIVATE KEY-----`.

### 2.2 Obtención de la Huella Digital del Servidor (`SSH_FINGERPRINT`)

Esto permite al workflow verificar la identidad del servidor al que se conecta.

1.  **Comando de Obtención:** Ejecuta este comando en tu terminal local, reemplazando `<IP_DEL_VPS>` por la IP de tu servidor.
    ```bash
    ssh-keyscan <IP_DEL_VPS> | ssh-keygen -lf -
    ```
2.  **Identificación del Valor:** La salida mostrará varias claves. Identifica la línea correspondiente a **ED25519** (o **ECDSA** en caso de que se presenten errores) y copia únicamente la huella, que es la cadena de texto que comienza con `SHA256:`.

### 2.3 Obtención del Token de GHCR (`GHCR_TOKEN`)

Este token permite al workflow publicar la imagen de la aplicación en GitHub Container Registry.

1.  **Navegación:** En GitHub, ve a `Settings` \> `Developer settings` \> `Personal access tokens` \> `Tokens (classic)`.
2.  **Generación:** Crea un nuevo token.
3.  **Permisos Requeridos:** Asígnale únicamente los permisos (scopes) `write:packages` y `read:packages`.
4.  **Valor:** Copia el token generado. Se mostrará una sola vez.

### 2.4 Resumen de Secretos por Repositorio

A continuación se resume dónde debe ir cada secreto.

#### **Secretos para `ArandanoIRTSoftware`**

*Ubicación: `Settings > Secrets and variables > Actions`*
| Secreto | Valor |
| :--- | :--- |
| `GHCR_TOKEN` | El Personal Access Token generado en el paso 2.3. |
| `SSH_HOST` | La IP pública de tu servidor VPS. |
| `SSH_USERNAME` | El nombre de usuario no-root en el VPS. |
| `SSH_PRIVATE_KEY` | El contenido de la clave privada obtenido en el paso 2.1. |
| `SSH_FINGERPRINT` | La huella digital del servidor obtenida en el paso 2.2. |

#### **Secretos para `ArandanoIRTOps`**

*Ubicación: `Settings > Secrets and variables > Actions`*
| Secreto | Valor |
| :--- | :--- |
| `SSH_HOST` | Reutiliza el mismo valor que en el otro repositorio. |
| `SSH_USERNAME` | Reutiliza el mismo valor. |
| `SSH_PRIVATE_KEY` | Reutiliza el mismo valor. |
| `SSH_FINGERPRINT` | Reutiliza el mismo valor. |

## 3. Flujos de Trabajo y Ejecución

Con la automatización, existen dos flujos de trabajo distintos para gestionar la aplicación y la infraestructura.

### 3.1 Despliegue Manual (Entorno Local/Desarrollo)

Este método se usa para desarrollo, pruebas locales o para levantar la pila desde cero antes de que la automatización tome el control.

1.  **Configuración:** Este flujo depende de la existencia de un archivo `docker-compose.override.yml` en la raíz del repositorio `ArandanoIRTOps`, el cual **está excluido del control de versiones por `.gitignore`** y no debe existir en el servidor de producción.
2.  **Código del `docker-compose.override.yml`:**
    ```yaml
    # ArandanoIRTOps/docker-compose.override.yml
    # Este archivo es solo para desarrollo local.
    version: '3.8'

    services:
      arandano-app:
        build:
          context: ../ArandanoIRTSoftware
    ```
3.  **Comando de Ejecución:** Tal como se describe en la guía de la Fase 2, el comando para construir y levantar la pila localmente es:
    ```bash
    # Desde la raíz de ArandanoIRTOps
    docker compose up -d --build
    ```

### 3.2 Despliegue Automatizado con CI/CD (Entorno de Producción)

Este es el método estándar para todas las actualizaciones en producción una vez configurado.

1.  **Flujo de la Aplicación (`ArandanoIRTSoftware`):**

      * Un desarrollador hace `push` a una rama de desarrollo.
      * El workflow de CI (`ci.yml`) se ejecuta, realizando la compilación, el linting y el análisis de CodeQL.
      * Tras la aprobación del Pull Request, se fusiona a `main`.
      * El workflow de CD (`deploy.yml`) se dispara, construye la imagen Docker, la publica en GHCR y actualiza el servicio correspondiente en el servidor.

2.  **Flujo de la Infraestructura (`ArandanoIRTOps`):**

      * Un desarrollador hace un Pull Request con cambios en `docker-compose.yml` u otros archivos de configuración.
      * El workflow de validación (`validate-ops.yml`) se ejecuta, comprobando la sintaxis y las buenas prácticas.
      * Tras la aprobación del PR, se fusiona a `main`.
      * El workflow de CD (`cd.yml`) se dispara, pero **se pausa** esperando una **aprobación manual** en la interfaz de GitHub Actions.
      * Una vez aprobado, el workflow se conecta al servidor, ejecuta `git pull` para obtener los nuevos archivos de configuración y luego `docker-compose up -d` para aplicar los cambios.

## 4. Consideraciones y Buenas Prácticas

  * **Aprobación Manual para Infraestructura:** El uso de "Entornos Protegidos" en GitHub para el workflow de `ArandanoIRTOps` es una capa de seguridad crítica. Proporciona un equilibrio ideal entre la velocidad de la automatización y la seguridad del control humano.
  * **Optimización de Workflows:** Las directivas `concurrency: cancel-in-progress: true` y los filtros `paths:` son esenciales para evitar ejecuciones redundantes y optimizar el uso de los minutos de GitHub Actions.
  * **Seguridad de las Acciones:** Es una buena práctica fijar las versiones de las acciones de terceros (ej. `@v4`, `@v1.2.2`) en los archivos de workflow para proteger el proyecto contra posibles ataques a la cadena de suministro.
  * **Mantenimiento de Secretos:** Se recomienda rotar los secretos que tienen fecha de caducidad, como el `GHCR_TOKEN`, de forma periódica (ej. cada 90 días) para mantener un alto nivel de seguridad.

## 5. Conclusión

La Fase 5 ha transformado exitosamente el proyecto de un modelo de operación manual a un sistema de GitOps completamente automatizado. Se han implementado pipelines de CI/CD para ambos repositorios, garantizando que el código y la infraestructura sean validados y desplegados de forma consistente, rápida y segura.