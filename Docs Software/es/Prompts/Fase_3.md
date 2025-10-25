**Fecha:** 2025-07-19
**Version plan de trabajo:** 1.2 

--- Resumen del Contexto ---

* **Proyecto:** Sistema de Monitoreo de Estrés Hídrico en Plantas de Arándano.
* **Objetivo Actual:** Implementar cuatro nuevos módulos (Gestión de Usuarios, Bitácora, Reportes/Analíticas, y Alertas) y mejorar la arquitectura general del sistema. 
* **Pila Tecnológica Clave:** .NET 8, PostgreSQL, Docker/Docker-Compose, Caddy, MinIO (Object Storage), Grafana/Loki (Logging), Brevo (Email API), Cloudflare Turnstile (Captcha).
* **Repositorio Git:** `https://github.com/rfgrona/arandanoirtsoftware`

--- Estado Actual del Proyecto ---

* **Fase Actual del Plan de Trabajo:** Fase 3: Sistema de Alertas y Notificaciones 
* **Tarea Específica a Abordar:** Tareas 3.2 a 3.6. 
* **Último Logro:** "Finalizamos por completo la Fase 2 (Módulo de Observaciones), implementando la creación y visualización paginada de la bitácora de campo inmutable."

--- Mi Pregunta/Objetivo Para Esta Sesión ---

"Hola. Hoy vamos a iniciar la **Fase 3: Sistema de Alertas y Notificaciones**. Mi objetivo es implementar toda la lógica para notificar a los usuarios sobre eventos importantes, tanto los generados por el sistema (estrés hídrico, inactividad) como los externos (Grafana). Ya tenemos el `IEmailService` (Tarea 3.1) listo.

Para abordar el resto de las tareas (3.2 a 3.6), te propongo que empecemos por el backend. Necesitaremos un servicio de tareas programadas (`IHostedService`) para la alerta de inactividad de dispositivos y un nuevo endpoint en un `AlertsController` para el webhook de Grafana. Tambien, necesitamos diseñar un `IAlertService` que centralice la lógica de disparo de alertas y a crear la estructura básica del `BackgroundService` para las tareas programadas.