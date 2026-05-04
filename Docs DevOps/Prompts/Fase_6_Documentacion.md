**Asunto:** Inicio de la Fase 6: Resiliencia y Documentación Final
**Fecha:** 2025-06-30
**Versión del Documento Maestro:** 4.0

**--- Resumen del Contexto ---**

* **Proyecto:** Sistema de Monitoreo de Estrés Hídrico en Plantas de Arándano.
* **Objetivo:** Implementar una aplicación monolítica .NET 8 con PostgreSQL, siguiendo una arquitectura agnóstica al proveedor, auto-hospedada y basada en contenedores.
* **Pila Tecnológica Clave:** Docker, Docker Compose, Caddy, PostgreSQL, MinIO, pgaudit, Serilog, Loki, Grafana, OneUptime y GitHub Actions.
* **Repositorios Git:**
    * Documentación: `https://github.com/RFGRONA/ArandanoIRT`
    * Operaciones (Infra): `https://github.com/RFGRONA/ArandanoIRTOps`
    * Software (App .NET): `https://github.com/RFGRONA/ArandanoIRTSoftware`

**--- Estado Actual ---**

* **Fase Actual del Plan de Trabajo:** Fase 6: Resiliencia y Documentación Final.
* **Tarea Específica a Abordar Hoy:** Tarea 6.1, 6.2 y 6.3 (Abordaje integral de la documentación final).
* **Último Logro:** Completamos exitosamente toda la Fase 5 de Automatización. El sistema ahora cuenta con pipelines de CI/CD para la aplicación y la infraestructura, incluyendo validaciones automáticas de calidad de código y despliegues controlados con aprobación manual.

**--- Mi Pregunta/Objetivo Para Hoy ---**

Mi objetivo es completar toda la documentación final del proyecto para asegurar su mantenibilidad y preparar la entrega. Para ello, necesito una guía detallada y estructurada para abordar las tres tareas finales de la Fase 6:

1.  **Plan de Recuperación de Desastres (Tarea 6.1):** Necesito que me guíes en la creación de un documento `Guia_Restauracion.md`. Este debe explicar, paso a paso, el procedimiento completo para recuperar el sistema en un escenario de desastre total. Debe cubrir:
    * El aprovisionamiento de un nuevo servidor VPS desde cero configurado para hacer despliegue manual (docker-compose.override.yml).
    * Consideraciones para implementar un sistema de tunnel para acceder a un servidor local.
    * La instalación del software base (Docker, Git, etc.).
    * La clonación de los repositorios.
    * La configuración del cliente de MinIO (`mc`).
    * El comando exacto para descargar el último backup de la base de datos desde el bucket de MinIO (en caso de que esté disponible).
    * El comando `pg_restore` necesario para restaurar el volcado (`.dump`) en un nuevo contenedor de PostgreSQL.
    * El comando final para levantar toda la pila de servicios con `docker compose`.

2.  **Manuales de Operaciones (Tarea 6.2):** Requiero una propuesta de estructura o creación de dos documentos distintos:
    * **Manual Técnico de Operaciones:** Un documento exhaustivo para un ingeniero de DevOps o un administrador de sistemas que herede el proyecto. Debe resumir la arquitectura, como hacer un despliegue inicial manual y cómo configurar el despliegue automatico, cómo funcionan los flujos de CI/CD, cómo gestionar los backups, cómo interpretar los dashboards de Grafana y dónde encontrar las configuraciones clave.

3.  **Checklist de Publicación Final (Tarea 6.3):** Para asegurar que no se me olvide nada, por favor, créame una checklist en formato Markdown con todos los puntos que debería revisar antes de considerar el proyecto como "finalizado". Por ejemplo:
    * Verificar que todos los `README.md` de los repositorios estén actualizados.
    * Asegurar que el código fuente esté debidamente comentado.
    * Confirmar que la documentación del proyecto en el repositorio principal esté completa y organizada.
    * Limpiar cualquier secreto o archivo temporal que no deba estar en los repositorios.