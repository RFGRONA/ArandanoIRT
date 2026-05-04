**Asunto:** Inicio del Proyecto y Fase 0: Planificación y Diseño Estratégico
**Fecha:** 2025-06-18
**Versión del Documento Maestro:** 2.1

**--- Resumen del Contexto ---**

* **Proyecto:** Sistema de Monitoreo de Estrés Hídrico en Plantas de Arándano.
* **Objetivo:** Definir la arquitectura completa y la planificación estratégica para un sistema de monitoreo robusto, seguro y replicable, listo para un desarrollo profesional.
* **Pila Tecnológica Clave:** Docker, Docker Compose, Caddy, PostgreSQL, OCI (ARM VM + Object Storage), MinIO, pgaudit, Serilog, CrowdSec.
* **Repositorio Git :** 
    - Repositorio de Documentación: `https://github.com/RFGRONA/ArandanoIRT` 
    - Repositorio de Infraestructura: `https://github.com/RFGRONA/ArandanoIRTOps` 

**--- Estado Actual ---**

* **Fase Actual del Plan de Trabajo:** Fase 0: Planificación y Diseño Estratégico
* **Tarea Específica a Abordar Hoy:** Todas las tareas de la Fase 0 (0.1, 0.2, 0.3).
* **Último Logro:** Decisión inicial del proyecto y creación de la estructura de repositorios en GitHub.

**--- Mi Pregunta/Objetivo Para Hoy ---**

Mi objetivo para esta fase es establecer una base de diseño sólida y profesional. Necesito tu ayuda para completar las tres tareas principales de la Fase 0, basadas en mis documentos de diseño y plan de trabajo:

1.  **Tarea 0.1 (Arquitectura):** Ayúdame a crear un diagrama de arquitectura detallado usando Mermaid. El diagrama debe visualizar la interacción entre los entornos de Producción (OCI), Staging (DigitalOcean) y los servicios externos como GitHub Actions y OCI Logging.
2.  **Tarea 0.2 (Base de Datos):** Tengo dos esquemas de base de datos previos: uno "empresarial" muy complejo y uno "simplificado" en uso. Quiero que los analices y me ayudes a diseñar un esquema final que sea un híbrido optimizado, tomando lo mejor de ambos mundos, que sea práctico de implementar con EF Core y que siga las mejores prácticas.
3.  **Tarea 0.3 (Auditoría):** Ayúdame a redactar una "Política de Auditoría" formal en un documento Markdown. Debe basarse en los requisitos de mi diseño técnico, como el uso de `pgaudit` con la configuración `ddl, role, write`, el almacenamiento de logs en OCI Logging y el cumplimiento con la ley colombiana.