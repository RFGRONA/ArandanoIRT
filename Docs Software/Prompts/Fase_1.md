Fecha: 2025-07-15
Version plan de trabajo: 1.1

--- Resumen del Contexto ---

Proyecto: Sistema de Monitoreo de Estrés Hídrico en Plantas de Arándano.

Objetivo Actual: Implementar cuatro nuevos módulos (Gestión de Usuarios, Bitácora, Reportes/Analíticas, y Alertas) y mejorar la arquitectura general del sistema.

Pila Tecnológica Clave: .NET 8, PostgreSQL, Docker/Docker-Compose, Caddy, MinIO (Object Storage), Grafana/Loki (Logging), Brevo (Email API), Cloudflare Turnstile (Captcha).

Repositorio Git: https://github.com/RFGRONA/ArandanoIRTSoftware/tree/alpha-gabriel

--- Estado Actual del Proyecto ---

Fase Actual del Plan de Trabajo: Fase 1: Módulo de Gestión de Usuarios y Sesión

Tarea Específica a Abordar: Todas las tareas de la Fase 1, desde la 1.1 hasta la 1.7.

Último Logro: "Finalizamos por completo la Fase 0. Se actualizó la BD, se refactorizó Program.cs, se configuró la subida de imágenes a MinIO y se limpiaron los controladores principales (CropsController, PlantsController) usando un BaseAdminController. También se aplicaron correcciones en la gestión de plantas y dispositivos. La base del código está estable y lista para las nuevas funcionalidades."

--- Mi Pregunta/Objetivo Para Esta Sesión ---

"Hola. Hoy vamos a ejecutar toda la Fase 1: Módulo de Gestión de Usuarios y Sesión.

Mi objetivo es implementar el ciclo de vida de usuario completo, siguiendo secuencialmente todas las tareas del plan de trabajo. Necesito tu guía para construir, paso a paso, las siguientes funcionalidades:

Tarea 1.1: Registro de usuarios mediante invitación y asignación de roles de administrador.

Tarea 1.2: Integración de CAPTCHA (Cloudflare Turnstile) en los formularios públicos (Registro, Login, etc.).

Tarea 1.3: Configuración de la duración de la sesión y manejo de su expiración en el cliente.

Tarea 1.4: Flujo completo de recuperación de contraseña.

Tarea 1.5: Formulario de ayuda con notificación por correo a los administradores.

Tarea 1.6 y 1.7: Vista de perfil de usuario para actualizar datos y cambiar la contraseña.
