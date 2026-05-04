# Plan de Trabajo: Implementación de Nuevos Módulos

**Versión:** 1.2
**Fecha:** 2025-07-18

## 1. Introducción

Este documento detalla el plan de trabajo para el desarrollo e implementación de nuevas funcionalidades en la plataforma Arandano IRT. El plan se divide en fases secuenciales, desde la preparación del entorno y la base de código hasta el despliegue final y la documentación, asegurando un proceso ordenado y de alta calidad.

---

## Fase 0: Preparación y Cimientos (Foundation & Setup)

**Objetivo:** Asegurar que la base del proyecto (código y base de datos) esté preparada y estable antes de construir nuevas funcionalidades.

| Tarea | Descripción | Entregable Esperado |
| :--- | :--- | :--- |
| **0.1** | **Actualización del Esquema de la BD** | Ejecutar los scripts `ALTER TABLE` para añadir `is_admin` a `invitation_codes` y `thermal_mask_data` a `plants` en el entorno de desarrollo. |
| **0.2** | **Refactorización de `Program.cs`** | Mover las configuraciones de servicios (Identity, BD, CORS, etc.) a métodos de extensión dedicados para mejorar la legibilidad y el mantenimiento. |
| **0.3** | **Implementación y Configuración de MinIO** | Asegurar que la configuración de MinIO en `appsettings.json` es correcta. Implementar la lógica en `DataSubmissionService` para gestionar la subida de imágenes RGB al bucket correspondiente. |
| **0.4** | **Revisión General y Limpieza** | Revisar el código existente en busca de posibles mejoras, actualizar paquetes NuGet a versiones estables y asegurar que el proyecto compila y se ejecuta sin errores. |

---

## Fase 1: Módulo de Gestión de Usuarios y Sesión

**Objetivo:** Implementar un ciclo de vida de usuario completo, seguro y robusto.

| Tarea | Descripción | Entregable Esperado |
| :--- | :--- | :--- |
| **1.1** | **Implementar Registro con Invitación y Roles** | Desarrollar la lógica en `AccountController` para validar códigos de invitación y asignar el rol "Admin" si `is_admin` es `true`. |
| **1.2** | **Configurar Gestión de Sesión** | Configurar la cookie de sesión de Identity con expiración y crear el manejador de JS para la alerta de "Sesión Expirada". |
| **1.3** | **Implementar Recuperación de Contraseña** | Crear los endpoints y vistas para el flujo de dos pasos usando los tokens nativos de ASP\.NET Core Identity y Bcrypt. |
| **1.4** | **Crear Formulario de Ayuda** | Desarrollar la vista y el endpoint que envía un correo a los administradores con la preferencia de notificación activada. |
| **1.5** | **Desarrollar Vista de Perfil de Usuario** | Crear la página donde el usuario puede actualizar su información y gestionar sus preferencias de notificación opcionales. |
**1.6** | **Implementar Cambio de Contraseña de Usuario** | Añadir a la vista de perfil un formulario para cambiar la contraseña (requiriendo la actual y la nueva). |

---

## Fase 2: Módulo de Observaciones (Bitácora de Campo)

**Objetivo:** Permitir a los usuarios registrar y consultar observaciones inmutables sobre las plantas.

| Tarea | Descripción | Entregable Esperado |
| :--- | :--- | :--- |
| **2.1** | **Crear Vistas de Bitácora** | Desarrollar la vista de listado (`/Observations/Index`) con paginación y el formulario de creación. |
| **2.2** | **Implementar Creación Inmutable** | Desarrollar la lógica en `ObservationController` para guardar nuevos registros, asegurando la doble confirmación en el frontend antes de enviar la petición. |

---

## Fase 3: Sistema de Alertas y Notificaciones

**Objetivo:** Notificar proactivamente a los usuarios sobre eventos importantes del sistema.

| Tarea | Descripción | Entregable Esperado |
| :--- | :--- | :--- |
| **3.1** | **Implementar Servicio de Correo (Brevo)** | Crear la interfaz `IEmailService` y su implementación `BrevoEmailService` para conectarse a la API de Brevo. |
| **3.2** | **Crear Endpoint para Webhook de Grafana** | Desarrollar el endpoint `POST /api/alerts/grafana-webhook` protegido por un token secreto. |
| **3.3** | **Implementar Lógica de Disparo de Alertas** | Codificar las condiciones que disparan cada tipo de alerta (estrés hídrico, fallos de login, etc.). |
| **3.4** | **Conectar Lógica con Preferencias** | Asegurar que la lógica de envío de alertas consulte el campo `settings` del usuario para respetar sus preferencias de notificación. |
| **3.5** | **Crear Servicio de Tareas Programadas** | Implementar una clase que herede de `BackgroundService` (`IHostedService`) para ejecutar lógicas periódicas en segundo plano. |
| **3.6** | **Implementar Alerta de Inactividad** | Dentro del nuevo servicio, implementar la lógica que compruebe el ultimo dato ambiental recibido de los dispositivos y envíe una alerta a los administradores si superan el umbral de inactividad. |

---

## Fase 4: Módulo de Generación de Reportes y Analíticas

**Objetivo:** Dotar al sistema de herramientas para el análisis de datos térmicos y la generación de reportes.

| Tarea | Descripción | Entregable Esperado |
| :--- | :--- | :--- |
| **4.1** | **Desarrollar UI para Máscara Térmica** | Crear la vista con la cuadrícula interactiva que permite al usuario seleccionar los puntos de la máscara sobre la imagen térmica. |
| **4.2** | **Implementar Almacenamiento de Máscara** | Crear la lógica en `AnalyticsService` para recibir las coordenadas de la máscara y guardarlas en el campo ``thermal_mask_data` de la tabla `plants` correspondiente a `{plantId}`. |
| **4.3** | **Crear Servicio de Generación de PDF** | Implementar un `IPdfGeneratorService` usando una librería como QuestPDF para crear los reportes en una carpeta temporal. |
| **4.4** | **Implementar Distribución de Reportes** | Desarrollar la funcionalidad en la interfaz para visualizar en el navegador, descargar y enviar por correo el PDF generado. |

---

## Fase 5: Calidad y Despliegue Final

**Objetivo:** Asegurar la calidad, mantenibilidad y correcta documentación del software antes del despliegue.

| Tarea | Descripción | Entregable Esperado |
| :--- | :--- | :--- |
| **5.1** | **Integrar CAPTCHA (Cloudflare Turnstile)** | Añadir el widget y la validación backend de Turnstile en las vistas (publicas) de Registro, Login, Recuperación de Contraseña y Formulario de Ayuda. |
| **5.2** | **Escritura de Pruebas** | Crear proyectos de Test (xUnit/NUnit) y desarrollar pruebas unitarias y de integración para las lógicas de negocio más críticas de los nuevos módulos. |
| **5.3** | **Análisis Estático de Código** | Ejecutar SonarQube sobre el proyecto y resolver los "code smells", bugs y vulnerabilidades reportadas. |
| **5.4** | **Generación de Documentación Doxygen** | Comentar el nuevo código en inglés siguiendo el formato Doxygen. Generar la documentación HTML. |
| **5.5** | **Configurar Workflow de GitHub Actions** | Crear un workflow (`.github/workflows/docs.yml`) que se dispare en cada push a la rama principal, genere la documentación Doxygen y la despliegue en GitHub Pages. |