# Guía de Recuperación ante Desastres v1.0

* **Proyecto:** Sistema de Monitoreo de Estrés Hídrico en Plantas de Arándano
* **Versión del Documento:** 1.0
* **Fecha:** 2025-07-01

## 1.0 Introducción y Escenario

### 1.1 Propósito
Este documento proporciona un procedimiento detallado paso a paso para restaurar el "Sistema de Monitoreo de Estrés Hídrico" en caso de una falla catastrófica del servidor principal. El objetivo es garantizar una recuperación rápida y fiable, minimizando la pérdida de datos y el tiempo de inactividad.

### 1.2 Escenario de Desastre
El escenario asumido es la **pérdida total e irrecuperable de la Máquina Virtual (VM) de producción**. Los únicos activos que se asumen disponibles son:
1.  Los repositorios de Git (`ArandanoIRTSoftware`, `ArandanoIRTOps`).
2.  Las copias de seguridad de la base de datos almacenadas en el bucket del servicio de almacenamiento de objetos (MinIO).

### 1.3 Objetivo de Recuperación
El objetivo de punto de recuperación (RPO) es de **24 horas**, lo que significa que, tras la restauración, la pérdida de datos será como máximo de las últimas 24 horas.

---

## 2.0 Fases de la Recuperación

El proceso de restauración se divide en tres fases principales:
* **Fase 1: Preparación del Nuevo Servidor:** Aprovisionamiento y configuración de una nueva VM desde cero.
* **Fase 2: Restauración de la Base de Datos:** Descarga de la última copia de seguridad y restauración de los datos en un nuevo contenedor de PostgreSQL.
* **Fase 3: Despliegue y Verificación:** Puesta en marcha de toda la pila de servicios y verificación de la integridad del sistema.

---

## 3.0 Procedimiento Detallado

### 3.1 Fase 1: Preparación del Nuevo Servidor

**Paso 1: Aprovisionar y Configurar la VM**
Siga exactamente los pasos descritos en el **Capítulo 4.2** del `Manual Técnico de Operaciones` para:
1.  Crear una nueva VM con Ubuntu 24.04 LTS.
2.  Configurar el firewall de red del proveedor de nube.
3.  Conectarse vía SSH, actualizar el sistema y crear un usuario no-root.
4.  Instalar Git y Docker (incluyendo Docker Compose).
5.  Configurar el firewall local `ufw` y el servicio `Fail2Ban`.

**Paso 2: Clonar Repositorios y Configurar el Entorno**
1.  Cree una carpeta de proyecto y clone los repositorios manteniendo la estructura de directorios requerida.
    ```bash
    mkdir -p ~/ArandanoProject
    cd ~/ArandanoProject
    git clone [https://github.com/RFGRONA/ArandanoIRTSoftware.git](https://github.com/RFGRONA/ArandanoIRTSoftware.git)
    git clone [https://github.com/RFGRONA/ArandanoIRTOps.git](https://github.com/RFGRONA/ArandanoIRTOps.git)
    ```
2.  Navegue a `ArandanoIRTOps`, cree el archivo de secretos `.env` a partir de la plantilla y rellene todas las variables.
3.  Cree el archivo `docker-compose.override.yml` para habilitar la construcción local de la imagen, como se detalla en la sección 4.3 del Manual de Operaciones.

### 3.2 Fase 2: Restauración de la Base de Datos

**Paso 3: Instalar y Configurar el Cliente MinIO (`mc`)**
Instale el cliente `mc` en el nuevo servidor anfitrión para poder acceder a los backups.
```bash
# Descargar e instalar
wget [https://dl.min.io/client/mc/release/linux-amd64/mc](https://dl.min.io/client/mc/release/linux-amd64/mc)
chmod +x mc
sudo mv mc /usr/local/bin/

# Configurar el alias con las credenciales ROOT de MinIO
mc alias set localminio [http://127.0.0.1:9000](http://127.0.0.1:9000) <MINIO_ROOT_USER> <MINIO_ROOT_PASSWORD>
```

*Nota: Si su bucket de MinIO estaba en un servicio externo, ajuste la URL y las credenciales acordemente.*

**Paso 4: Descargar el Último Backup**

1.  Liste los archivos en su bucket de backups para identificar el más reciente.
    ```bash
    mc ls localminio/backups
    ```
2.  Descargue el archivo de backup más reciente a su directorio actual.
    ```bash
    mc cp localminio/backups/backup-YYYY-MM-DD_HH-MM-SS.dump .
    ```

**Paso 5: Iniciar el Contenedor de PostgreSQL**
Para poder restaurar los datos, primero se necesita una instancia de base de datos en ejecución. Inicie únicamente el servicio de PostgreSQL.

```bash
# Desde la raíz de ArandanoIRTOps
docker compose up -d postgres
```

**Paso 6: Ejecutar la Restauración con `pg_restore`**

1.  Copie el archivo de backup desde el servidor anfitrión al interior del contenedor de PostgreSQL.
    ```bash
    docker cp backup-YYYY-MM-DD_HH-MM-SS.dump arandano-postgres:/tmp/backup.dump
    ```
2.  Ejecute `pg_restore` dentro del contenedor para restaurar la base de datos.
    ```bash
    docker exec -it arandano-postgres pg_restore -U arandano_user -d arandano_db -c -v /tmp/backup.dump
    ```
      * `-U arandano_user`: Especifica el usuario de la base de datos.
      * `-d arandano_db`: Especifica la base de datos de destino.
      * `-c`: (Clean) Limpia los objetos de la base de datos antes de recrearlos.
      * `-v`: (Verbose) Muestra información detallada del proceso.

### 3.3 Fase 3: Despliegue y Verificación Final

**Paso 7: Levantar la Pila Completa de Servicios**
Con la base de datos restaurada, inicie el resto de los servicios. El flag `--build` es necesario para construir la imagen de la aplicación .NET.

```bash
# Desde la raíz de ArandanoIRTOps
docker compose up -d --build
```

**Paso 8: Verificar el Sistema**

1.  **Actualizar DNS:** Si la IP del nuevo servidor es diferente, actualice los registros DNS (ej. en Cloudflare) para que apunten a la nueva dirección.
2.  **Verificar Acceso:** Acceda a la URL pública de la aplicación (`https://arandanoirt.co`) y verifique que los servicios están en línea.
3.  **Verificar Datos:** Navegue por la aplicación y confirme que los datos restaurados están presentes y son correctos.

---

## 4.0 Recomendaciones de Resiliencia 

La guía anterior asume la disponibilidad de los backups en MinIO. Sin embargo, en un escenario de desastre donde el volumen de datos del servidor se pierde por completo, se requiere una estrategia de respaldo externo (off-site). A continuación se detallan recomendaciones críticas para robustecer el plan de recuperación.

### 4.1 Copia de Seguridad de Credenciales (`.env`)

El archivo `.env` contiene todos los secretos de configuración y es vital para levantar la aplicación. Perderlo es tan crítico como perder la base de datos. **Este archivo NUNCA debe almacenarse en un repositorio de Git en texto plano.**

**Recomendación:** Almacenar una copia cifrada y segura del contenido del archivo `.env` en una ubicación externa y accesible en caso de emergencia.

* **Método 1 (Recomendado): Gestor de Contraseñas.**
    * Utilice un gestor de contraseñas de confianza (como **Bitwarden** «_Open Source_» o 1Password).
    * Cree una "Nota Segura" dentro del gestor y pegue el contenido completo del archivo `.env`.
    * **Ventajas:** Está cifrado, accesible desde cualquier lugar con su contraseña maestra y es una práctica de seguridad.

* **Método 2 (Avanzado): Repositorio Git Cifrado.**
    * Utilice herramientas como `git-crypt` para mantener una versión cifrada del archivo `.env` en un repositorio Git **privado** y separado.
    * **Ventajas:** Permite el versionado de los secretos.
    * **Desventajas:** Requiere una configuración más compleja y la gestión segura de la clave de cifrado.

### 4.2 Copia de Seguridad Externa (Off-Site) de la Base de Datos

Para cumplir con la regla de backup **3-2-1** (3 copias, en 2 medios distintos, 1 off-site), es indispensable que una copia de los volcados de la base de datos resida fuera del VPS principal.

* **Opción 1 (Recomendada): Sincronización a un Proveedor S3 Externo.**
    * **Descripción:** Modificar el script `backup_postgres.sh` para que, después de subir el backup al MinIO local, ejecute un segundo comando para subir ese mismo archivo a un proveedor de almacenamiento de objetos externo y de bajo costo (ej. **Backblaze B2**, **Wasabi**, AWS S3 Glacier).
    * **Implementación:**
        1.  Crear una cuenta en el proveedor externo y obtener credenciales de API.
        2.  Configurar un segundo alias en el cliente `mc`: `mc alias set s3-externo <URL_DEL_PROVEEDOR> <ACCESS_KEY> <SECRET_KEY>`.
        3.  Añadir la línea `mc cp $BACKUP_FILE s3-externo/backups/` al final del script `backup_postgres.sh`.
    * **Ventajas:** Proceso totalmente automatizado y muy robusto.

* **Opción 2 (Arquitectónica): Usar Volúmenes de Bloque Externos.**
    * **Descripción:** La mayoría de los proveedores de nube (como DigitalOcean) permiten crear "Volúmenes de Almacenamiento en Bloque" que son independientes de las VMs.
    * **Implementación:** Al configurar el sistema, se debe montar el directorio de datos de MinIO (`minio_data`) en uno de estos volúmenes externos.
    * **Ventajas:** En caso de que la VM sea destruida, el volumen de datos (con todos los backups) sobrevive. Se puede simplemente crear una nueva VM y "adjuntar" el volumen existente para recuperar el acceso a los backups instantáneamente.
