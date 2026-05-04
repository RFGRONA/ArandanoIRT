### Estrategia de Colaboración: "El Prompt Maestro"

La idea central es que cada vez que se incie una nueva sesión de chat con la IA, se comienza con un **"Prompt Maestro"** estandarizado. Este bloque de texto permitirá la sincronización de la IA sobre el estado actual del proyecto.

#### El Formato del "Prompt Maestro"

Al principio de cada chat, el primer mensaje deberá seguir esta plantilla actualizandola antes de cada sesión.

```markdown
**Asunto:** Continuación del Proyecto de Monitoreo de Arándanos
**Fecha:** [Fecha actual]
**Versión del Documento Maestro:** 2.0

**--- Resumen del Contexto ---**

* **Proyecto:** Sistema de Monitoreo de Estrés Hídrico en Plantas de Arándano.
* **Objetivo:** Implementar una aplicación monolítica .NET 8 con PostgreSQL, siguiendo una arquitectura robusta, segura y replicable (Cloud/Local).
* **Pila Tecnológica Clave:** Docker, Docker Compose, Caddy, PostgreSQL, OCI (ARM VM + Object Storage), MinIO, pgaudit, Serilog, CrowdSec.
* **Repositorio Git (Fuente Única de Verdad):** [Enlace a tu repositorio de GitHub]

**--- Estado Actual ---**

* **Fase Actual del Plan de Trabajo:** [Ej: Fase 2: Adaptación de Código y "Contenerización"]
* **Tarea Específica a Abordar Hoy:** [Ej: Tarea 3.1: Implementación de la Lógica de Aplicación y Datos]
* **Último Logro:** [Ej: "Terminamos de configurar la VM en Oracle y el software base (Fase 1)"]

**--- Mi Pregunta/Objetivo Para Hoy ---**
[Aquí va la pregunta o el problema específico que se quiere resolver en esta sesión]
```

#### Cómo Formular Preguntas

Para evitar que la IA "alucine" o dé respuestas genéricas, la precisión de las preguntas es clave. Se recomienda esta estructura:

1.  **Contexto Específico:** "Estoy en la Tarea X. He configurado mi `Caddyfile` como se ve en este enlace."
2.  **Acción Realizada:** "Cuando ejecuto `docker-compose up -d` y visito mi dominio..."
3.  **Resultado Observado (El Problema):** "...recibo un error 502 Bad Gateway. Aquí están los logs del contenedor de Caddy: `[Pega los logs relevantes]`."
4.  **Pregunta Directa:** "Mi hipótesis es que Caddy no puede resolver el nombre del contenedor de la app. ¿Es correcta mi configuración de `reverse_proxy` en el Caddyfile o me falta algo en la red de Docker Compose?"

### Ejemplo de Inicio de una Nueva Sesión

Imagina que es la próxima semana y se va a trabajar en la base de datos. El primer mensaje con la IA sería:

> **Asunto:** Continuación del Proyecto de Monitoreo de Arándanos
> **Fecha:** 21 de Junio de 2025
> **Versión del Documento Maestro:** 2.0
>
> **--- Resumen del Contexto ---**
>
> * **Proyecto:** Sistema de Monitoreo de Estrés Hídrico en Plantas de Arándano.
> * **Objetivo:** Implementar una aplicación monolítica .NET 8 con PostgreSQL, siguiendo una arquitectura robusta, segura y replicable (Cloud/Local).
> * **Pila Tecnológica Clave:** Docker, Docker Compose, Caddy, PostgreSQL, OCI (ARM VM + Object Storage), MinIO, pgaudit, Serilog, CrowdSec.
> * **Repositorio Git (Fuente Única de Verdad):** `https://github.com/gvanegas/proyecto-arandano`
>
> **--- Estado Actual ---**
>
> * **Fase Actual del Plan de Trabajo:** Fase 2: Adaptación de Código y "Contenerización"
> * **Tarea Específica a Abordar Hoy:** Tarea 3.1, específicamente el sub-punto "Implementar el modelo de datos híbrido (JSONB)".
> * **Último Logro:** "Completamos toda la Fase 1. La VM de Oracle está configurada con Docker, Git y los firewalls."
>
> **--- Mi Pregunta/Objetivo Para Hoy ---**
>
> "Quiero empezar a definir mis modelos de Entity Framework Core para reflejar la estructura híbrida (columnas + JSONB) que discutimos. ¿Podrías mostrarme un ejemplo de cómo se vería una clase de entidad en C# que tiene un campo `string` o `JsonDocument` que se mapea a un campo `JSONB` en PostgreSQL, y qué configuración del DbContext de EF Core se necesita para asegurar que se cree la columna del tipo correcto?"
