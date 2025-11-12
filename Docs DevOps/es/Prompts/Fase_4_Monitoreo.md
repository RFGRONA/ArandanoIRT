**Asunto:** Inicio de la Fase 4: Observabilidad y Monitoreo
**Fecha:** 2025-06-26
**Versión del Documento Maestro:** 4.0

**--- Resumen del Contexto ---**

* **Proyecto:** Sistema de Monitoreo de Estrés Hídrico en Plantas de Arándano.
* **Objetivo:** Implementar una aplicación monolítica .NET 8 con PostgreSQL, siguiendo una arquitectura agnóstica al proveedor, auto-hospedada y basada en contenedores.
* **Pila Tecnológica Clave:** Docker, Docker Compose, Caddy, PostgreSQL, MinIO, pgaudit, Serilog, y una pila de logging auto-hospedada (Loki/Promtail/Grafana).
* **Repositorios Git:**
    * Repositorio de Documentación: `https://github.com/RFGRONA/ArandanoIRT`
    * Repositorio de Infraestructura: `https://github.com/RFGRONA/ArandanoIRTOps`
    * Repositorio de Software: `https://github.com/RFGRONA/ArandanoIRTSoftware`

**--- Estado Actual ---**

* **Fase Actual del Plan de Trabajo:** Fase 4: Observabilidad y Monitoreo.
* **Tarea Específica a Abordar Hoy:** Todas las tareas de la Fase 4 (4.1, 4.2 y 4.3).
* **Último Logro:** Fase 3 completada exitosamente. El sistema está desplegado, en línea, accesible de forma segura vía HTTPS en `arandanoirt.co` y sus subdominios. Los backups diarios automatizados a MinIO están configurados y funcionando.

**--- Mi Pregunta/Objetivo Para Hoy ---**

Mi objetivo es implementar una estrategia completa de observabilidad y monitoreo para el sistema. Para ello, necesito una guía detallada para completar toda la Fase 4:

1.  **Para la Tarea 4.1 (Configuración de Grafana y Dashboards):**
    * Necesito conectar Grafana (accesible en `https://grafana.arandanoirt.co`) al servicio Loki como una fuente de datos ("Data Source"). La URL interna de Loki desde Grafana debería ser `http://loki:3100`.
    * Una vez conectado, requiero construir un dashboard de "Logs Generales" con dos paneles:
        * **Panel 1 (Logs de Aplicación):** Debe mostrar los logs provenientes del contenedor `arandano-app`. Quiero poder filtrar estos logs por nivel (ej. `Info`, `Error`, `Warning`) y obtener por otro lado los que provienen de dispositivos.
        * **Panel 2 (Logs de Auditoría):** Debe mostrar únicamente los logs de auditoría generados por `pgaudit` desde el contenedor `arandano-postgres`. Necesito la consulta LogQL para filtrar y mostrar solo las líneas que contienen la palabra `AUDIT`.
    * Finalmente, ayudame a configurar una **regla de alerta básica en Grafana** que me notifique (por ahora, dentro de Grafana o por correo si es posible) si se detecta un log con `Level="Error"` o `Level="Fatal"` en el contenedor `arandano-app`.

2.  **Para la Tarea 4.2 (Configuración del Monitoreo Externo con OneUptime):**
    * Necesito una guía paso a paso para configurar monitores de estado (Health Checks) en un servicio como OneUptime.
    * Los monitores deben verificar la disponibilidad (respondiendo con un código 200 OK) de los siguientes tres endpoints: `https://arandanoirt.co`, `https://grafana.arandanoirt.co`, y `https://minio.arandanoirt.co`.
    * También, indícame cómo crear y publicar una página de estado pública básica usando la misma herramienta.
    * Analizar qué otras opciones o herramientas de monitoreo ofrece OneUpTime en su tier gratuita que podrían ser útiles para el proyecto.

3.  **Para la Tarea 4.3 (Pruebas Funcionales y Verificación de Logs):**
    * Después de configurar los dashboards, realizaré operaciones CRUD en la aplicación (ej. crear un nuevo usuario, editar un dispositivo).
    * Para verificar que todo se registra correctamente, necesito que me proporciones las **consultas LogQL exactas** que debo usar en la sección "Explore" de Grafana para encontrar:
        * Los logs de la aplicación .NET que se generan al realizar estas acciones.
        * Los logs de auditoría de `pgaudit` que muestren las sentencias `INSERT`, `UPDATE` o `DELETE` correspondientes a mis acciones.