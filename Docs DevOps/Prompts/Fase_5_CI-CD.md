**Asunto:** Inicio de la Fase 5: Automatización (CI/CD)
**Fecha:** 2025-06-29
**Versión del Documento Maestro:** 4.0

**--- Resumen del Contexto ---**

* **Proyecto:** Sistema de Monitoreo de Estrés Hídrico en Plantas de Arándano.
* **Objetivo:** Implementar una aplicación monolítica .NET 8 con PostgreSQL, siguiendo una arquitectura agnóstica al proveedor, auto-hospedada y basada en contenedores.
* **Pila Tecnológica Clave:** Docker, Docker Compose, Caddy, PostgreSQL, MinIO, pgaudit, Serilog, Loki, Grafana y OneUptime.
* **Repositorios Git:**
    * Documentación: `https://github.com/RFGRONA/ArandanoIRT`
    * Operaciones (Infra): `https://github.com/RFGRONA/ArandanoIRTOps`
    * Software (App .NET): `https://github.com/RFGRONA/ArandanoIRTSoftware`

**--- Estado Actual ---**

* **Fase Actual del Plan de Trabajo:** Fase 5: Automatización (CI/CD).
* **Tarea Específica a Abordar Hoy:** Tarea 5.1: Configuración del Pipeline de CI/CD con GitHub Actions.
* **Último Logro:** Completamos exitosamente toda la Fase 4 de Observabilidad y Monitoreo. El sistema ahora cuenta con monitoreo interno (Grafana/Loki), alertas con notificaciones externas (vía Webhook a Brevo) y monitoreo de disponibilidad y página de estado pública (con OneUptime). Adicionalmente, se configuraron alertas a nivel de infraestructura en DigitalOcean.

**--- Mi Pregunta/Objetivo Para Hoy ---**

Mi objetivo es automatizar completamente el despliegue de mi aplicación .NET. Para ello, necesito una guía detallada y paso a paso para implementar la **Tarea 5.1** usando GitHub Actions. La guía debe cubrir:

1.  **Configuración del Registro de Contenedores:** ¿Cuáles son los pasos para configurar y usar GitHub Container Registry (GHCR) para alojar la imagen Docker de mi aplicación `ArandanoIRTSoftware`?

2.  **Gestión de Secretos:** ¿Qué "Actions Secrets" necesito crear en el repositorio de GitHub (`ArandanoIRTSoftware`) y cómo debo generarlos? Específicamente para:
    * Las credenciales para hacer login en GHCR (`DOCKER_USERNAME`, `DOCKER_PASSWORD` o `GH_TOKEN`).
    * Las credenciales para acceder al servidor VPS vía SSH (`SSH_HOST`, `SSH_USERNAME`, `SSH_PRIVATE_KEY`).

3.  **Creación del Workflow de CI/CD (con lógica de PR y Merge):**
    * Necesito la estructura completa de un archivo `.github/workflows/deploy.yml` que se comporte de dos maneras, considerando que mi rama `main` está protegida:
        a.  **En un Pull Request a `main`:** Debe ejecutar los jobs de `build` y `test` para validar los cambios, pero NO desplegar.
        b.  **En un `push` a `main` (después de fusionar un PR):** Debe ejecutar los jobs de `build`, `test` y `deploy`.
    * Por favor, muéstrame cómo configurar la sección `on:` para ambos disparadores.
    * Para el job `deploy`, necesito que incluyas la **condición `if`** necesaria para asegurar que solo se ejecute cuando el evento sea un `push` a la rama `main`.

4.  **Detalles de los Jobs:**
    * **Para el job `build`:** Necesito el código YAML para construir la imagen Docker de la aplicación .NET y publicarla en GHCR con un tag (ej. `latest`).
    * **Para el job `deploy`:** Necesito el código YAML que se conecte al VPS vía SSH (usando los secretos) y ejecute un script o una serie de comandos para actualizar el servicio.

5.  **Script de Despliegue en el Servidor:** ¿Cuál es el script o la secuencia de comandos recomendada que el job `deploy` debe ejecutar en el servidor? Mi hipótesis es que debería ser algo como:
    * Hacer login en GHCR.
    * Navegar al directorio de `ArandanoIRTOps`.
    * Ejecutar `docker compose pull arandano-app`.
    * Ejecutar `docker compose up -d arandano-app`.

Por favor, proporciona los fragmentos de código YAML y de scripts Bash necesarios para cada paso.