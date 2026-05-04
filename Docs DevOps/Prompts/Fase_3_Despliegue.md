**Asunto:** Inicio de la Fase 3: Despliegue, DNS y Puesta en Marcha
**Fecha:** 2025-06-23
**Versión del Documento Maestro:** 3.0

**--- Resumen del Contexto ---**

* **Proyecto:** Sistema de Monitoreo de Estrés Hídrico en Plantas de Arándano.
* **Objetivo:** Implementar una aplicación monolítica .NET 8 con PostgreSQL, siguiendo una arquitectura agnóstica al proveedor, auto-hospedada y basada en contenedores.
* **Pila Tecnológica Clave:** Docker, Docker Compose, Caddy, PostgreSQL, MinIO, pgaudit, Serilog, CrowdSec, y una pila de logging auto-hospedada (Loki/Promtail/Grafana).
* **Repositorios Git:**
    * Repositorio de Documentación: `https://github.com/RFGRONA/ArandanoIRT`
    * Repositorio de Infraestructura: `https://github.com/RFGRONA/ArandanoIRTOps`
    * Repositorio de Software: `https://github.com/RFGRONA/ArandanoIRTSoftware`

**--- Estado Actual ---**

* **Fase Actual del Plan de Trabajo:** Fase 3: Despliegue, DNS y Puesta en Marcha
* **Tarea Específica a Abordar Hoy:** Todas las tareas de la Fase 3 (3.1, 3.2 y 3.3).
* **Último Logro:** Fase 2 completada exitosamente. La aplicación .NET está codificada, la configuración de secretos está gestionada, y toda la pila de servicios (PostgreSQL, MinIO, Loki, Grafana, Caddy) está orquestada en un `docker-compose.yml` final. Tenemos un manual de ejecución (`Fase_2_Guia_Configuracion.md`) que detalla cómo levantar todo el entorno.

**--- Mi Pregunta/Objetivo Para Hoy ---**

Mi objetivo es llevar el proyecto a producción, haciéndolo accesible desde internet de forma segura y estableciendo un plan de backups. Para ello, necesito una guía detallada para completar toda la Fase 3:

1.  **Para la Tarea 3.1 (Configuración de Dominio y DNS):**
    * Ya se tiene un dominio (`arandanoirt.co`) y tengo la IP pública de mi VPS, necesito que me ayudes a configurar de la mejor manera los registros DNS. Específicamente, el registro `A` para el dominio principal y si necesito crear `CNAME` o registros `A` adicionales para los subdominios que planeo usar (`grafana` y `minio`).

2.  **Para la Tarea 3.2 (Configuración de Caddy con HTTPS):**
    * Esta es la tarea más crítica. Necesito que me proporciones el contenido exacto para el archivo `Caddyfile` en el repositorio `ArandanoIRTOps`. La configuración debe:
        * a) Gestionar el dominio principal (`arandano-monitoreo.com`) y redirigir todo el tráfico a nuestro servicio `arandano-app` en el puerto `8080`.
        * b) Configurar subdominios para los servicios de administración. Por ejemplo, `grafana.arandano-monitoreo.com` debe redirigir al servicio `grafana` en el puerto `3000`, y `minio.arandano-monitoreo.com` al servicio `minio` en el puerto `9001`.
        * c) **Lo más importante:** Manejar automáticamente la obtención y renovación de certificados SSL/TLS de Let's Encrypt para todos los dominios y subdominios, forzando el tráfico a HTTPS.

3.  **Para la Tarea 3.3 (Backups Automatizados):**
    * Necesito crear una estrategia de backup robusta para la base de datos de PostgreSQL. Para ello, requiero:
        * a) Un **script de shell (`.sh`)** que utilice `pg_dump` dentro del contenedor de PostgreSQL para crear un backup de la base de datos. El script debe comprimir el archivo de backup y nombrarlo con la fecha y hora actual (ej. `backup-2025-06-24_10-30-00.sql.gz`).
        * b) El script también debe usar un cliente S3 (como `mc`, el cliente de MinIO) para **subir este backup comprimido** a un bucket específico (ej. `backups`) en nuestro servicio MinIO.
        * c) Finalmente, necesito saber cómo configurar un **`cronjob`** en el servidor VPS anfitrión para que ejecute este script de backup de forma automática todos los días a una hora específica (ej. 3:00 AM).