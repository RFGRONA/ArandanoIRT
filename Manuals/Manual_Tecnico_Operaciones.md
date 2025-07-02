# Manual Técnico de Operaciones y Mantenimiento v1.0

* **Proyecto:** Sistema de Monitoreo de Estrés Hídrico en Plantas de Arándano
* **Versión del Documento:** 1.0
* **Fecha:** 2025-07-01

## 1.0 Introducción

### 1.1 Propósito

Este documento proporciona una guía técnica exhaustiva para el despliegue, la configuración, la operación y el mantenimiento del "Sistema de Monitoreo de Estrés Hídrico". Está dirigido a ingenieros de DevOps, administradores de sistemas o personal técnico encargado de la infraestructura y la puesta en marcha del proyecto en un entorno de producción.

### 1.2 Alcance

El manual cubre todo el ciclo de vida de las operaciones, desde el aprovisionamiento inicial de un servidor hasta el monitoreo continuo, la gestión de copias de seguridad y los flujos de trabajo de despliegue automatizado. No cubre el desarrollo o la modificación del código fuente de la aplicación, para lo cual se debe consultar el Manual Técnico del Software.

---

## 2.0 Arquitectura del Sistema

La arquitectura del sistema se basa en un conjunto de principios clave: uso de software de código abierto, infraestructura como código (IaC), portabilidad a través de contenedores y seguridad por diseño.

### 2.1 Diagrama de Arquitectura

<img src="../Docs%20DevOps/es/Modelado/Diagrama%20Arquitectura.drawio.png" alt="Diagrama de Arquitectura del Sistema" width="600"/>

### 2.2 Flujo de Componentes

El sistema se despliega en una única Máquina Virtual (VM) en un proveedor de nube. El tráfico entrante es gestionado por un firewall de red y luego por un proxy inverso (Caddy), que lo distribuye a los servicios correspondientes. Todos los servicios se ejecutan como contenedores Docker orquestados por Docker Compose.

* **Aplicación Principal (.NET 8):** Expone la interfaz web y la API.
* **Base de Datos (PostgreSQL 16):** Almacena los datos de la aplicación. Utiliza la extensión `pgaudit` para auditoría.
* **Almacenamiento de Objetos (MinIO):** Servicio compatible con S3 para almacenar backups.
* **Pila de Observabilidad (Loki y Grafana):** Agrega y visualiza logs de la aplicación y de la base de datos.
* **Proxy Inverso (Caddy):** Gestiona el tráfico entrante y la terminación SSL/TLS de forma automática.

### 2.3 Filosofía de Diseño

Este proyecto, nacido en un semillero de investigación enfocado en software libre, se adhiere a una filosofía **"Open Source Primero"**. El objetivo es maximizar el control, la transparencia y la portabilidad, minimizando la dependencia de proveedores y los costos asociados.

Sin embargo, el proyecto también adopta un enfoque pragmático y robusto, integrando estratégicamente servicios privativos de alto rendimiento para funciones donde ofrecen una ventaja competitiva clara (como la gestión de DNS/WAF o la protección anti-bots).

El resultado es una arquitectura híbrida que refleja un balance profesional del mundo real:

* **Total de Herramientas en la Pila:** 14
* **Open Source:** 78.6% (11 de 14)
* **Privativo:** 21.4% (3 de 14)

---

## 3.0 Pila Tecnológica

A continuación se detallan las tecnologías utilizadas en el proyecto, clasificadas por su rol en el sistema.

### 3.1 Infraestructura y Operaciones

| Componente | Tecnología | Tipo | Propósito |
| :--- | :--- | :--- | :--- |
| Sistema Operativo | Ubuntu Server 24.04 LTS| Open Source | SO base del servidor anfitrión (Vital). |
| Orquestación | Docker & Compose | Open Source | Contenerización y gestión de servicios (Vital). |
| Control de Versiones | Git | Open Source | Sistema de control de versiones para todo el código (Vital). |
| Proxy Inverso | Caddy | Open Source | Enrutamiento de tráfico y gestión automática de SSL/TLS (Vital). |
| DNS y Seguridad Perimetral | Cloudflare | Privativo | Gestión de DNS, WAF y Proxy de red (Vital). |
| Almacenamiento S3 | MinIO | Open Source | Almacenamiento de objetos para backups de base de datos (Complementario). |
| Agregación de Logs | Loki & Promtail | Open Source | Sistema de agregación y recolección de logs centralizado (Complementario). |
| Visualización | Grafana | Open Source | Creación de dashboards y alertas a partir de logs (Complementario). |
| Monitoreo Externo | OneUptime | Open Source | Monitoreo de disponibilidad de endpoints y página de estado (Complementario). |
| Prevención de Intrusos | Fail2Ban | Open Source | Protección a nivel de host contra ataques de fuerza bruta (Complementario). |
| Firewall de Host | UFW | Open Source | Seguridad a nivel de sistema operativo. |

### 3.2 Aplicación y Base de Datos

| Componente | Tecnología | Tipo | Propósito |
| :--- | :--- | :--- | :--- |
| Framework Backend | .NET 8 | Open Source | Lógica principal de la aplicación y API (Vital). |
| Base de Datos | PostgreSQL 16| Open Source | Almacenamiento de datos relacionales y semi-estructurados (Vital). |
| Protección Anti-Bot | Cloudflare Turnstile | Privativo | Sistema de CAPTCHA no intrusivo para formularios web (Complementario). |
| Notificaciones | Brevo | Privativo | Servicio de envío de correos transaccionales vía API para alertas (Complementario). |
| Acceso a Datos | Entity Framework Core | Open Source | ORM para la interacción con la base de datos. |
| Logging de Aplicación | Serilog | Open Source | Generación de logs estructurados en formato JSON. |
| Auditoría de BD | pgaudit | Open Source | Extensión de PostgreSQL para registrar eventos de auditoría. |
| Observabilidad externa | OneUptime | Open Source | Monitoreo de disponibilidad de endpoints y página de estado. | |

-----

## 4.0 Instalación y Puesta en Marcha Inicial

Este capítulo detalla el procedimiento de despliegue **manual** del sistema en un servidor nuevo y sin configuración previa. Este proceso, conocido como *bootstrapping*, es el fundamento sobre el cual operarán los despliegues automatizados posteriores y es el método requerido para la recuperación ante desastres.

Este flujo de trabajo depende de la construcción de la imagen de la aplicación .NET de forma local en el servidor de destino, facilitada por un archivo `docker-compose.override.yml`.

### 4.1 Prerrequisitos

* **Acceso al Servidor:** Se requiere acceso vía SSH a un servidor con Ubuntu 24.04 LTS.
* **Software Local:** Un cliente de Git para clonar los repositorios.
* **Estructura de Repositorios:** Es **mandatorio** que los dos repositorios del proyecto, `ArandanoIRTSoftware` y `ArandanoIRTOps`, se clonen dentro de la misma carpeta padre en el servidor. La configuración de Docker Compose depende de esta estructura de rutas relativas.
    ```
    /home/user/ArandanoProject/
    |
    ├── ArandanoIRTSoftware/  <-- Repositorio de la aplicación .NET
    └── ArandanoIRTOps/       <-- Repositorio de operaciones (Docker)
    ```

### 4.2 Aprovisionamiento y Configuración del Servidor

Estos pasos preparan y aseguran el servidor anfitrión.

#### 4.2.1 Configuración del Firewall de Red
Antes de configurar el servidor, se debe establecer un firewall a nivel de proveedor de nube (ej. DigitalOcean Cloud Firewall). Las reglas de entrada deben ser:
* **SSH (Puerto 22/TCP):** Permitido desde IPs específicas y conocidas.
* **HTTP (Puerto 80/TCP):** Permitido desde cualquier dirección.
* **HTTPS (Puerto 443/TCP):** Permitido desde cualquier dirección.

#### 4.2.2 Configuración del Servidor Anfitrión (Vía SSH)
Los siguientes comandos deben ejecutarse en el servidor.

1.  **Actualizar el sistema:**
    ```bash
    sudo apt update && sudo apt upgrade -y
    ```
   

2.  **Crear un usuario no-root con privilegios `sudo`** (reemplace `NAMEUSER` con el nombre deseado):
    ```bash
    adduser NAMEUSER
    usermod -aG sudo NAMEUSER
    rsync --archive --chown=NAMEUSER:NAMEUSER /root/.ssh /home/NAMEUSER
    ```
   
    *Después de este paso, se debe cerrar la sesión de `root` y volver a conectar con el nuevo usuario.*

3.  **Instalar software base (Docker y Git):**

    1. **Desinstalar cualquier versión antigua o no oficial de Docker**

    ```bash
    sudo apt-get remove docker.io docker-doc docker-compose podman-docker containerd runc
    ```
    2. **Configurar el repositorio oficial de Docker**
    ```bash
    # 1. Actualizar el índice de paquetes e instalar prerrequisitos
    sudo apt-get update
    sudo apt-get install -y ca-certificates curl

    # 2. Añadir la clave GPG oficial de Docker
    sudo install -m 0755 -d /etc/apt/keyrings
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
    sudo chmod a+r /etc/apt/keyrings/docker.asc

    # 3. Añadir el repositorio de Docker a las fuentes de APT
    echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
    $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
    sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt-get update

    # 4. Permitir al usuario actual ejecutar comandos de Docker sin sudo
    sudo usermod -aG docker $USER
    ```
    **Importante:** Después del último comando, es necesario **cerrar la sesión SSH y volver a conectarse** para que los permisos del grupo `docker` se apliquen.

    3. **Instalar Docker Engine, CLI y el plugin de Compose**
    ```bash
    sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    ```
    4. **Instalar Git**
    ```bash
    sudo apt-get install -y git
    ```

4.  **Configurar el firewall local (UFW):**
    ```bash
    sudo ufw allow OpenSSH
    sudo ufw allow http
    sudo ufw allow https
    sudo ufw enable
    ```
   

5.  **Instalar el sistema de prevención de intrusiones:**
    ```bash
    sudo apt install fail2ban -y
    sudo systemctl enable --now fail2ban
    ```
   

### 4.3 Preparación del Entorno de Despliegue

1.  **Clonar los repositorios** (asegúrese de estar en la carpeta padre, ej. `~/ArandanoProject/`):
    ```bash
    git clone [https://github.com/RFGRONA/ArandanoIRTSoftware.git](https://github.com/RFGRONA/ArandanoIRTSoftware.git)
    git clone [https://github.com/RFGRONA/ArandanoIRTOps.git](https://github.com/RFGRONA/ArandanoIRTOps.git)
    ```

2.  **Configurar los secretos de la aplicación:**
    * Navegue al directorio `ArandanoIRTOps`.
    * Copie el archivo de plantilla `.env.template` a un nuevo archivo `.env`.
    * Edite el archivo `.env` y complete todas las credenciales y variables de entorno. Este archivo es ignorado por Git y nunca debe ser subido al repositorio.

3.  **Habilitar la construcción local de la imagen:**
    * En la raíz del repositorio `ArandanoIRTOps`, cree un archivo llamado `docker-compose.override.yml`. Este archivo le indica a Docker Compose que debe construir la imagen de la aplicación (`arandano-app`) desde el código fuente local en lugar de buscarla en un registro de contenedores.
    * Añada el siguiente contenido al archivo:
    ```yaml
    # ArandanoIRTOps/docker-compose.override.yml
    # Este archivo es solo para desarrollo local o despliegue manual inicial.

    services:
      arandano-app:
        build:
          context: ../ArandanoIRTSoftware
    ```
   

### 4.4 Despliegue de la Pila de Servicios

Con toda la configuración en su lugar, el despliegue se realiza con un único comando desde la raíz del repositorio `ArandanoIRTOps`.

```bash
# Navegar al directorio de operaciones
cd ~/ArandanoProject/ArandanoIRTOps

# Levantar la pila completa, construyendo la imagen de la app en el proceso
docker compose up -d --build
```

  * `up`: Inicia los servicios.
  * `-d`: Modo "detached", para que se ejecute en segundo plano.
  * `--build`: Fuerza la reconstrucción de la imagen a partir de su Dockerfile, esencial la primera vez o tras cambios en el código .NET.

### 4.5 Verificación del Despliegue

Para confirmar que todos los servicios se han iniciado correctamente:

  * **Verificar el estado de los contenedores:**
```bash
    docker compose ps
```
  * **Monitorear los logs en tiempo real:**
```bash
    docker compose logs -f
```

Una vez que todos los contenedores estén en estado "running" o "healthy", el sistema estará operativo y accesible a través de la dirección IP pública del servidor. Los servicios de gestión como Grafana o MinIO Console estarán accesibles inicialmente en `http://localhost:3000` y `http://localhost:9001` respectivamente, y serán expuestos públicamente en el siguiente capítulo.

Perfecto. La adición de los pasos detallados para la instalación de Docker es una mejora excelente, hace que el manual sea aún más autocontenido.

Hemos completado el despliegue manual inicial. Ahora, el siguiente paso lógico es tomar ese sistema en ejecución y configurarlo para el mundo real: exponerlo de forma segura a Internet y asegurar la integridad de sus datos.

Continuemos con el siguiente capítulo.

-----

## 5.0 Configuración del Entorno de Producción

Una vez que la pila de servicios se está ejecutando localmente en el servidor, este capítulo describe los pasos finales para exponer la aplicación de forma segura al público, configurar un dominio y establecer una estrategia de copias de seguridad automáticas.

### 5.1 Configuración de Dominio y DNS

Para maximizar la seguridad y el rendimiento, la gestión del DNS se delega a un servicio como Cloudflare. Esto proporciona una capa de protección (WAF, Anti-DDoS) y oculta la dirección IP real del servidor.

#### 5.1.1 Configuración de Registros DNS
En el panel de control del gestor de DNS (ej. Cloudflare), se deben configurar los siguientes registros para apuntar el dominio y los subdominios a la dirección IP pública del servidor VPS.

| Tipo | Nombre | Contenido | Estado de Proxy |
| :--- | :--- | :--- | :--- |
| **A** | `arandanoirt.co` | `<IP_PÚBLICA_DEL_VPS>` | Redirigido mediante proxy |
| **A** | `grafana` | `<IP_PÚBLICA_DEL_VPS>` | Redirigido mediante proxy |
| **A** | `minio` | `<IP_PÚBLICA_DEL_VPS>` | Redirigido mediante proxy |
| **CNAME**| `www` | `arandanoirt.co` | Redirigido mediante proxy |

*(Nota: Se deben mantener los registros MX, TXT y otros necesarios para la operatividad de servicios externos como el correo electrónico).*

#### 5.1.2 Verificación de la Propagación
Para confirmar que los registros DNS se han propagado correctamente, se puede usar el comando `dig` desde una terminal local. La salida debería resolver a direcciones IP del proveedor de proxy (ej. Cloudflare), no a la IP directa del VPS.
```bash
dig arandanoirt.co
dig grafana.arandanoirt.co
```

### 5.2 Configuración del Proxy Inverso (Caddy)

Caddy es el servicio que actúa como puerta de enlace a la aplicación. Gestiona todo el tráfico HTTP/S entrante, obtiene y renueva automáticamente los certificados SSL/TLS, y enruta las peticiones al contenedor de servicio apropiado. Esta configuración se define en el archivo `ArandanoIRTOps/caddy/Caddyfile`.

Después de modificar este archivo, es necesario reiniciar la pila de Docker Compose para que Caddy aplique la nueva configuración:

```bash
# Desde la raíz de ArandanoIRTOps
docker compose restart
```

### 5.3 Implementación de Backups Automatizados

Para garantizar la recuperación ante desastres, se debe implementar un sistema de copias de seguridad diarias y automáticas de la base de datos PostgreSQL. **El RPO (Recovery Point Objective) definido para este sistema es de 24 horas.**

#### 5.3.1 Configuración del Cliente MinIO (mc)

Se debe instalar y configurar el cliente de línea de comandos de MinIO (`mc`) en el servidor anfitrión. Este cliente se utilizará para comunicarse con el servicio de MinIO que corre en Docker.

1.  **Instalar `mc`:**
    ```bash
    wget [https://dl.min.io/client/mc/release/linux-amd64/mc](https://dl.min.io/client/mc/release/linux-amd64/mc)
    chmod +x mc
    sudo mv mc /usr/local/bin/
    ```
2.  **Configurar el alias de conexión** (reemplace con las credenciales `MINIO_ROOT_USER` y `MINIO_ROOT_PASSWORD` definidas en su archivo `.env`):
    ```bash
    mc alias set localminio [http://127.0.0.1:9000](http://127.0.0.1:9000) <MINIO_ROOT_USER> <MINIO_ROOT_PASSWORD>
    ```
3.  **Crear el bucket de destino** para las copias de seguridad:
    ```bash
    mc mb localminio/backups
    ```

#### 5.3.2 Creación del Script de Backup

El repositorio `ArandanoIRTOps` debe contener un script `backup_postgres.sh` para realizar la copia de seguridad. El script debe realizar un volcado de la base de datos usando `pg_dump` dentro del contenedor de PostgreSQL y luego usar `mc` para subir el archivo al bucket de MinIO.

#### 5.3.3 Automatización con Cronjob

Para automatizar la ejecución del script, se configura un `cronjob` en el servidor anfitrión.

1.  Abrir el editor de crontab:
    ```bash
    crontab -e
    ```
2.  Añadir la siguiente línea al final del archivo para programar la ejecución todos los días a las 3:00 AM (reemplace `<usuario>` y la ruta al script):
    ```crontab
    0 3 * * * /home/<usuario>/ArandanoProject/ArandanoIRTOps/backup_postgres.sh >> /home/<usuario>/ArandanoProject/ArandanoIRTOps/backup.log 2>&1
    ```

### 5.4 Verificación Final

Tras completar estos pasos, el sistema está completamente desplegado, operativo y accesible públicamente de forma segura. Se debe verificar el acceso a todas las URLs públicas y confirmar que los certificados HTTPS son válidos:

  * `https://arandanoirt.co`
  * `https://grafana.arandanoirt.co`
  * `https://minio.arandanoirt.co`

Entendido. Tiene sentido mantener los archivos de configuración en el repositorio como la única fuente de verdad y referenciarlos desde el manual. Es una práctica excelente.

Hemos cubierto el despliegue inicial y la configuración de producción. Ahora, cerraremos el ciclo de vida del despliegue explicando cómo se gestiona el proyecto en el día a día a través de la automatización.

Aquí tienes la propuesta para el siguiente capítulo, que se basa casi en su totalidad en la detallada `Fase_5_Guia_Configuracion.md`.

-----

## 6.0 Operación Continua (CI/CD)

Una vez completada la puesta en marcha inicial descrita en los capítulos anteriores, el proyecto pasa a un modelo de operación basado en la filosofía GitOps. Esto significa que toda actualización, ya sea del código de la aplicación o de la configuración de la infraestructura, debe gestionarse a través de los pipelines de Integración Continua y Despliegue Continuo (CI/CD) configurados con GitHub Actions.

Este enfoque automatizado aumenta la velocidad y fiabilidad de los despliegues, a la vez que reduce drásticamente el riesgo de error humano y mantiene un registro de cambios auditable en el historial de Git.

### 6.1 Configuración de Secretos y Permisos

Para que los flujos de trabajo de GitHub Actions puedan interactuar de forma segura con los recursos externos (el servidor VPS y el registro de contenedores), es necesario configurar "Actions Secrets" en los repositorios de GitHub. Esta es una configuración que se realiza una única vez.

A continuación, se resumen los secretos necesarios por repositorio:

**Secretos para `ArandanoIRTSoftware` y `ArandanoIRTOps`:**
*(Ubicación en GitHub: `Project Settings > Secrets and variables > Actions`)*

| Secreto | Descripción | Repositorio |
| :--- | :--- | :--- |
| `SSH_HOST` | La dirección IP pública del servidor VPS. | Ambos |
| `SSH_USERNAME` | El nombre del usuario no-root en el VPS con privilegios `sudo`. | Ambos |
| `SSH_PRIVATE_KEY` | El contenido de la clave privada SSH para acceder al VPS. | Ambos |
| `SSH_FINGERPRINT` | La huella digital de la clave pública del servidor para verificar su identidad. | Ambos |
| `GHCR_TOKEN` | Un token de acceso personal para publicar en GitHub Container Registry. | `ArandanoIRTSoftware` |

#### 6.1.1 Obtención de los Valores de los Secretos

A continuación se detalla cómo obtener el valor para cada uno de los secretos críticos.

**Obtención de `SSH_PRIVATE_KEY`**

Esta clave permite al workflow autenticarse en el servidor VPS.

1.  **Comando de Generación:** Ejecuta el siguiente comando en tu terminal local para crear un par de claves dedicadas para GitHub Actions.
    ```bash
    ssh-keygen -t ed25519 -f ~/.ssh/arandano_deploy_key -C "github-actions-arandanoirt"
    ```
2.  **Comando para Obtener el Valor:** El valor para el secreto es el contenido del archivo de la clave **privada**.
    ```bash
    cat ~/.ssh/arandano_deploy_key
    ```
    Copia toda la salida, incluyendo `-----BEGIN OPENSSH PRIVATE KEY-----` y `-----END OPENSSH PRIVATE KEY-----`.

**Obtención de `SSH_FINGERPRINT`**

Esto permite al workflow verificar la identidad del servidor al que se conecta.

1.  **Comando de Obtención:** Ejecuta este comando en tu terminal local, reemplazando `<IP_DEL_VPS>` por la IP de tu servidor.
    ```bash
    ssh-keyscan <IP_DEL_VPS> | ssh-keygen -lf -
    ```
2.  **Identificación del Valor:** La salida mostrará varias claves. Identifica la línea correspondiente a **ED25519** (o **ECDSA**) y copia únicamente la huella, que es la cadena de texto que comienza con `SHA256:`.

**Obtención de `GHCR_TOKEN`**

Este token permite al workflow publicar la imagen de la aplicación en GitHub Container Registry.

1.  **Navegación:** En GitHub, ve a `User Settings` > `Developer settings` > `Personal access tokens` > `Tokens (classic)`.
2.  **Generación:** Crea un nuevo token.
3.  **Permisos Requeridos:** Asígnale únicamente los permisos (scopes) `write:packages` y `read:packages`.
4.  **Valor:** Copia el token generado. Se mostrará una sola vez.

### 6.2 Pipeline de la Aplicación (`ArandanoIRTSoftware`)

Este flujo de trabajo automatiza el despliegue de una nueva versión de la aplicación .NET.

* **Disparador:** El flujo se activa automáticamente tras fusionar un Pull Request a la rama `main`.
* **Proceso:**
    1.  El workflow de CI (`ci.yml`) se ejecuta en el Pull Request, realizando la compilación, pruebas y análisis de calidad de código con CodeQL.
    2.  Tras la aprobación y fusión a `main`, el workflow de CD (`deploy.yml`) se dispara.
    3.  **Construcción y Publicación:** Se construye la imagen Docker de la aplicación y se publica en GitHub Container Registry (GHCR).
    4.  **Despliegue:** El workflow se conecta al servidor VPS vía SSH, descarga la nueva versión de la imagen desde GHCR y reinicia el servicio `arandano-app` con `docker compose up -d` para aplicar la actualización.

### 6.3 Pipeline de la Infraestructura (`ArandanoIRTOps`)

Este flujo de trabajo gestiona los cambios en la configuración de la infraestructura, como modificaciones en `docker-compose.yml` o en el `Caddyfile`.

* **Disparador:** El flujo se activa tras fusionar un Pull Request a la rama `main` del repositorio `ArandanoIRTOps`.
* **Proceso:**
    1.  El workflow de validación (`validate-ops.yml`) comprueba la sintaxis y buenas prácticas de los archivos de configuración en el Pull Request.
    2.  Tras la fusión a `main`, el workflow de CD (`cd.yml`) se dispara, pero **se detiene inmediatamente**.
    3.  **Aprobación Manual:** El despliegue se pausa esperando una **aprobación manual** de un revisor autorizado en la interfaz de GitHub Actions. Esta es una capa de seguridad crítica para evitar cambios no deseados en la infraestructura de producción.
    4.  **Despliegue:** Una vez aprobado, el workflow se conecta al servidor VPS vía SSH, actualiza los archivos de configuración ejecutando `git pull` en el repositorio local y finalmente aplica los cambios con `docker compose up -d`.

### 6.4 Buenas Prácticas de CI/CD

Para mantener un sistema de CI/CD seguro y eficiente, se siguen las siguientes prácticas:

* **Aprobación para Infraestructura:** El uso de "Entornos Protegidos" en GitHub para requerir la aprobación manual de los despliegues de `ArandanoIRTOps` es una medida de seguridad deliberada.
* **Optimización de Ejecuciones:** Se utilizan directivas como `concurrency: cancel-in-progress: true` y filtros de `paths:` en los workflows para evitar ejecuciones redundantes.
* **Seguridad de las Acciones:** Las versiones de las acciones de terceros utilizadas en los workflows (ej., `actions/checkout@v4`) están fijadas para proteger el proyecto contra posibles ataques a la cadena de suministro.
* **Mantenimiento de Secretos:** Se recomienda rotar periódicamente (ej., cada 90 días) los secretos con fecha de caducidad, como el `GHCR_TOKEN`.

----

## 7.0 Estrategia de Observabilidad

Para garantizar una alta disponibilidad y una rápida resolución de problemas, el sistema implementa una estrategia de observabilidad en múltiples capas. Esta estrategia permite no solo visualizar el estado de salud del sistema en tiempo real, sino también recibir alertas ante cualquier anomalía.

La estrategia se divide en dos componentes principales:
* **Monitoreo Interno:** Centrado en el comportamiento del software a través de la recolección y visualización de logs estructurados y eventos de auditoría.
* **Monitoreo Externo:** Enfocado en la disponibilidad de los servicios desde la perspectiva del usuario final.

### 7.1 Monitoreo Interno (Logs y Alertas con Grafana)

La pila de monitoreo interno está compuesta por Loki, que agrega los logs, y Grafana, que se utiliza para la visualización y la configuración de alertas. El acceso a la interfaz se realiza a través del subdominio configurado (ej., `https://grafana.arandanoirt.co`).

#### 7.1.1 Visualización de Logs (Dashboards)
Se han preconfigurado varios dashboards en Grafana para interpretar los datos más relevantes. Estos dashboards utilizan el lenguaje de consulta LogQL.

* **Dashboard de Errores de Aplicación:** Se centra en la salud del servicio .NET.
    * **Consulta para logs de Error/Fatal:** ` {container="arandano-app"} | json | Level="Error" `.
    * **Consulta para contador de errores (últimas 24h):** `sum(count_over_time({container="arandano-app"} | json | Level="Error" [24h]))`.

* **Dashboard de Logs de Dispositivos:** Filtra los logs que provienen específicamente de los dispositivos de hardware.
    * **Consulta:** `{container="arandano-app"} | json |= "Log desde DeviceId"`.

* **Dashboard de Auditoría de Base de Datos:** Muestra los eventos de auditoría generados por la extensión `pgaudit` en PostgreSQL, permitiendo ver operaciones críticas en la base de datos.
    * **Consulta:** `{container="arandano-postgres"} |~ "AUDIT"`.

#### 7.1.2 Sistema de Alertas Internas
Grafana está configurado para notificar proactivamente sobre problemas detectados en los logs.

* **Alerta de Errores de Aplicación:** Se dispara si se detecta más de un error (`Error` o `Fatal`) en un periodo de 1 minuto.
    * **Consulta:** `count_over_time({container="arandano-app"} | json | Level=~"Error|Fatal" [1m])`.
    * **Condición:** `is above 0`.

#### 7.1.3 Canal y Políticas de Notificación
Para evitar el bloqueo de puertos SMTP por parte de algunos proveedores de nube, las notificaciones se envían a través de un webhook a la API del servicio Brevo.

Para evitar la fatiga por alertas, se han configurado las siguientes políticas de notificación en Grafana:
* **`Group wait`: `5m`**: Agrupa una ráfaga inicial de errores en una sola notificación.
* **`Group interval`: `5m`**: Permite el envío rápido de notificaciones para problemas diferentes.
* **`Repeat interval`: `1h`**: Silencia recordatorios para el mismo problema durante una hora.

### 7.2 Monitoreo Externo de Disponibilidad (OneUptime)

Se utiliza un servicio externo como OneUptime para simular la experiencia del usuario final y verificar constantemente que los puntos de acceso públicos estén en línea.

* **Endpoints Monitoreados:** Se han configurado monitores de tipo `Website` para los siguientes endpoints:
    * `https://arandanoirt.co` (Aplicación Principal)
    * `https://grafana.arandanoirt.co` (Dashboard Grafana)
    * `https://minio.arandanoirt.co` (Almacenamiento de Objetos)

* **Página de Estado Pública:** Para comunicar de forma transparente la disponibilidad del sistema, se ha configurado una página de estado pública accesible en `status.arandanoirt.co`.

### 7.3 Verificación Funcional de Logs

Para realizar una prueba completa del sistema de observabilidad, el operador debe:
1.  Realizar operaciones CRUD (Crear, Leer, Actualizar, Borrar) en la aplicación web.
2.  Navegar al dashboard de Grafana.
3.  Verificar que tanto los logs de la aplicación .NET como los logs de auditoría de `pgaudit` reflejen correctamente las acciones realizadas.
