**Fecha:** 2025-07-12
**Version plan de trabajo:** 1.1

**--- Resumen del Contexto ---**

* **Proyecto:** Sistema de Monitoreo de Estrés Hídrico en Plantas de Arándano.
* **Objetivo Actual:** Implementar cuatro nuevos módulos (Gestión de Usuarios, Bitácora, Reportes/Analíticas, y Alertas) y mejorar la arquitectura general del sistema.
* **Pila Tecnológica Clave:** .NET 8, PostgreSQL, Docker/Docker-Compose, Caddy, MinIO (Object Storage), Grafana/Loki (Logging), Brevo (Email API), Cloudflare Turnstile (Captcha).
* **Repositorio Git:** `https://github.com/RFGRONA/ArandanoIRTSoftware/tree/alpha-gabriel`

**--- Estado Actual del Proyecto ---**

* **Fase Actual del Plan de Trabajo:** Fase 0: Preparación y Cimientos
* **Tarea Específica a Abordar:** Todas las tareas de la Fase 0 (0.1, 0.2, 0.3, 0.4).
* **Último Logro:** "Finalizamos la planificación detallada con la creación del Diseño Técnico y el Plan de Trabajo, incluyendo las últimas correcciones."

**--- Mi Pregunta/Objetivo Para Esta Sesión ---**

"Hola. Hoy vamos a ejecutar toda la **Fase 0** de nuestro plan de trabajo para preparar la base del código antes de empezar con los módulos. Necesito tu guía para las siguientes cuatro tareas, en orden:

1.  **Tarea 0.1 (Actualización de la BD):** Primero, necesito modificar el script de la base de datos para añadir la columna `is_admin` a la tabla `invitation_codes` y la columna `thermal_mask_data` a la tabla `plants`. Por favor, muéstrame cómo debería quedar el script SQL para estas modificaciones, ya que es crucial que este script quede actualizado para el repositorio de infraestructura, y cómo ejecutarlo en mi entorno de desarrollo y el comando de EF para aplicar los cambios en la aplicación

2.  **Tarea 0.2 (Refactorización de `Program.cs`):** Quiero mover la configuración de servicios a métodos de extensión para limpiar `Program.cs`. Ayudame a crear una clase estática `DependencyInjection.cs` en la capa de `Infrastructure` y otras clases de extensión necesarias para separar la configuración de Identity, CORS, etc. Ayudame con el código de `Program.cs` y de las nuevas clases de extensión.

3.  **Tarea 0.3 (Configuración de MinIO):** El servicio `MinioStorageService` existe, pero necesito asegurarme de que esté bien implementado para la subida de imágenes RGB que vienen desde la API. Revisa el método `SubmitThermalDataAsync` en `DataSubmissionService.cs` y dame la lógica para que, además de guardar los datos térmicos, se guarde el archivo de la imagen RGB (que viene como `IFormFile` en `ThermalDataDto`) en MinIO y se almacene la ruta en la entidad `ThermalCapture`.

4.  **Tarea 0.4 (Revisión General):** Finalmente, como parte de la revisión general, me gustaría saber si ves alguna oportunidad de mejora obvia o "code smell" en los controladores principales como `CropsController.cs` o `PlantsController.cs` que debamos considerar antes de añadir más lógica."