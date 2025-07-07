# Prompt Maestro y Estrategia de Colaboración con IA

**Versión:** 1.0 
**Fecha:** 2025-07-06

## 1. Propósito

Este documento establece una metodología estandarizada para interactuar con un asistente de IA durante el desarrollo del proyecto Arandano IRT. Su propósito es doble:

1.  **Sincronizar a la IA:** Proporcionar rápidamente a la IA todo el contexto relevante sobre el estado actual del proyecto al inicio de cada sesión.
2.  **Fomentar la Precisión:** Guiar la formulación de preguntas para obtener respuestas técnicas precisas y evitar información genérica o incorrecta.

## 2. El Formato del "Prompt Maestro"

Al principio de cada nueva sesión de chat, el primer mensaje deberá usar la siguiente plantilla. El desarrollador debe actualizar los campos de estado antes de enviarlo.

```markdown
**Fecha:** [Fecha Actual]
**Version plan de trabajo:** [Versión del Plan de Trabajo]

--- Resumen del Contexto ---

* **Proyecto:** Sistema de Monitoreo de Estrés Hídrico en Plantas de Arándano.
* **Objetivo Actual:** Implementar cuatro nuevos módulos (Gestión de Usuarios, Bitácora, Reportes/Analíticas, y Alertas) y mejorar la arquitectura general del sistema.
* **Pila Tecnológica Clave:** .NET 8, PostgreSQL, Docker/Docker-Compose, Caddy, MinIO (Object Storage), Grafana/Loki (Logging), Brevo (Email API), Cloudflare Turnstile (Captcha).
* **Repositorio Git:** `https://github.com/rfgrona/arandanoirtsoftware`

--- Estado Actual del Proyecto ---

* **Fase Actual del Plan de Trabajo:** [Ej: Fase 0: Preparación y Cimientos]
* **Tarea Específica a Abordar:** [Ej: Tarea 0.1: Actualización del Esquema de la BD]
* **Último Logro:** [Ej: "Finalizamos la planificación y el diseño técnico de todos los módulos."]

--- Mi Pregunta/Objetivo Para Esta Sesión ---

[Aquí va la pregunta o el problema específico que se quiere resolver.]
```

## 3. Mejores Prácticas para Consultas Efectivas

La estructura de tus preguntas es fundamental. El formato que propusiste es excelente y debería seguirse siempre que sea posible para maximizar la calidad de la respuesta.

1.  **Contexto Específico de la Tarea:** *"Estoy en la Tarea 1.2 (Integrar Captcha). Ya he añadido el servicio de validación en el backend."*
2.  **Acción Realizada:** *"Ahora estoy intentando añadir el script de Cloudflare Turnstile en la vista de Login (`Login.cshtml`)"*
3.  **Resultado Observado (El Problema):** *"Pero el widget no aparece. La consola del navegador no muestra errores y he verificado que la clave del sitio está en `appsettings.json`."*
4.  **Pregunta Directa y Material de Apoyo:** *"¿Podrías revisar mi implementación en `Login.cshtml` para ver si me falta alguna referencia de script o algún atributo HTML específico que requiere Turnstile? Aquí está el código de mi vista: `[Pega el código relevante]`."*

## 4. Ejemplo de Inicio de una Nueva Sesión

Este es un ejemplo realista de cómo iniciar una sesión de trabajo para la primera tarea de nuestro plan.

> **Fecha:** 2025-07-08 
> **Version plan de trabajo:** 1.0
>
> **--- Resumen del Contexto ---**
>
>   * **Proyecto:** Sistema de Monitoreo de Estrés Hídrico en Plantas de Arándano.
>   * **Objetivo Actual:** Implementar cuatro nuevos módulos (Gestión de Usuarios, Bitácora, Reportes/Analíticas, y Alertas) y mejorar la arquitectura general del sistema.
>   * **Pila Tecnológica Clave:** .NET 8, PostgreSQL, Docker/Docker-Compose, Caddy, MinIO (Object Storage), Grafana/Loki (Logging), Brevo (Email API), Cloudflare Turnstile (Captcha).
>   * **Repositorio Git:** `https://github.com/rfgrona/arandanoirtsoftware`
>
> **--- Estado Actual del Proyecto ---**
>
>   * **Fase Actual del Plan de Trabajo:** Fase 0: Preparación y Cimientos
>   * **Tarea Específica a Abordar:** Tarea 0.1: Actualización del Esquema de la BD
>   * **Último Logro:** "Finalizamos la planificación con la creación del Diseño Técnico y el Plan de Trabajo."
>
> **--- Mi Pregunta/Objetivo Para Esta Sesión ---**
>
> "Necesito crear una nueva migración de Entity Framework Core para aplicar los dos cambios que definimos en la base de datos (añadir `is_admin` a `invitation_codes` y `analysis_data` a `thermal_captures`). ¿Podrías recordarme los pasos y el comando exacto de `dotnet-ef` para generar una nueva migración llamada `AddUserRolesAndAnalysisData` desde la línea de comandos, asumiendo que mi proyecto de `Infrastructure` es el que contiene el `DbContext`?"
