# Diseño Técnico del Software: Nuevos Módulos para Arandano IRT

**Versión:** 1.1 
**Fecha:** 2025-07-12

## 1. Introducción

Este documento describe el diseño técnico para la implementación de nuevas funcionalidades en el sistema de monitoreo de estrés hídrico en arándanos. El diseño se centra en la extensibilidad, la seguridad y la reutilización de la infraestructura existente, siguiendo un enfoque de desarrollo rápido y minimizando las modificaciones a la base de datos. Todo el código nuevo se desarrollará en inglés.

## 2. Modificaciones a la Base de Datos

Se realizarán únicamente dos modificaciones controladas al esquema de la base de datos PostgreSQL.

| Tabla Afectada | Modificación | Propósito |
| :--- | :--- | :--- |
| `public.invitation_codes` | Añadir columna `is_admin BOOLEAN NOT NULL DEFAULT FALSE` | Permitir la creación de usuarios con rol de "Administrador" directamente desde la invitación. |
| `public.plants` | Añadir columna `thermal_mask_data JSONB NULL` | Almacenar la máscara térmica única para cada planta, que se aplicará a todas sus capturas. |

## 3. Módulo 1: Gestión de Usuarios y Sesión

Este módulo mejora la seguridad y la experiencia del usuario en el ciclo de vida de la cuenta.

### 3.1. Arquitectura y Componentes Clave

  - **Backend:** Se modificarán `AccountController.cs` y se crearán nuevos servicios y DTOs en las capas `Application` y `Domain`. Se aprovechará **ASP\.NET Core Identity**.
  - **Frontend:** Se crearán/modificarán vistas Razor (`.cshtml`) para los nuevos formularios.
  - **Seguridad:** Se integrará **Cloudflare Turnstile** en todos los formularios públicos.

### 3.2. Flujo de Registro por Invitación

1.  **Generación de Invitación:** Un administrador podrá generar códigos de invitación. La lógica asignará `is_admin = true` o `false` al código.
2.  **Formulario de Registro (`/Account/Register`):**
      - Campos: Código de Invitación, Email, Nombre, Apellido, Contraseña, Confirmar Contraseña.
      - Se incluirá el widget de **Cloudflare Turnstile**.
3.  **Lógica de Backend:**
      - El `AccountController` recibirá los datos del formulario.
      - Se validará el token de Turnstile con la API de Cloudflare.
      - Se verificará que el código de invitación exista, no esté usado y no haya expirado.
      - Se creará el `User` usando `UserManager.CreateAsync()` y Bcrypt para hashear la contraseña.
      - Si el `invitation_code.is_admin` es `true`, se asignará el rol "Admin" al nuevo usuario usando `UserManager.AddToRoleAsync()`.
      - Se marcará el código de invitación como usado.
      - Se creará una plantilla de configuración por defecto en el campo `settings` del nuevo usuario.

### 3.3. Inicio de Sesión y Gestión de Sesión

1.  **Formulario de Login (`/Account/Login`):**
      - Se añadirá el widget de **Cloudflare Turnstile**.
2.  **Configuración de Sesión (Cookie):**
      - En `Program.cs` (o en un archivo de extensión de configuración), se configurará el servicio de cookies de Identity:
        ```csharp
        builder.Services.ConfigureApplicationCookie(options =>
        {
            options.ExpireTimeSpan = TimeSpan.FromHours(builder.Configuration.GetValue<int>("SessionSettings:TimeoutHours"));
            options.SlidingExpiration = true;
            options.LoginPath = "/Admin/Account/Login";
        });
        ```
      - El valor `SessionSettings:TimeoutHours` se leerá de `appsettings.json` (por defecto 4).
3.  **Manejo de Expiración en Cliente:**
      - Se implementará un interceptor de peticiones (usando `fetch` o `axios` en `site.js`) que detecte respuestas con código de estado `401 Unauthorized`.
      - Al detectar un `401`, se mostrará un modal de "Sesión Expirada" y se redirigirá al login tras unos segundos.

### 3.4. Recuperación de Contraseña

1.  **Formulario 1 - Solicitud de Reseteo (`/Account/ForgotPassword`):**
      - Campo: Email.
      - Se incluirá el widget de **Cloudflare Turnstile**.
      - Al enviar, se llama a un método en `AccountController`. Este genera un token de reseteo usando `UserManager.GeneratePasswordResetTokenAsync()` y lo envía al correo del usuario a través de un nuevo servicio `IEmailService`.
2.  **Formulario 2 - Actualización de Contraseña (`/Account/ResetPassword`):**
      - Campos: Email, Nueva Contraseña, Confirmar Contraseña, y el token (leído desde la URL).
      - Al enviar, se llama a `UserManager.ResetPasswordAsync()` para validar el token y actualizar la contraseña utilizando Bcrypt.

### 3.5. Formulario de Ayuda y Perfil de Usuario

  - **Formulario de Ayuda:**
      - Vista pública con campos: Nombre, Email de Contacto, Asunto, Descripción y **Captcha**.
      - Un endpoint en un nuevo controlador (ej. `SupportController`) recibirá los datos.
      - La lógica buscará a todos los usuarios con rol "Admin" y cuyo campo `settings` contenga `"receive_help_alerts": true`.
      - Se enviará el correo a los administradores que cumplan la condición usando `IEmailService`.
  - **Perfil de Usuario (`/Account/Manage`):**
      - Permitirá al usuario cambiar su información básica (nombre, apellido).
      - Permitirá editar las preferencias de notificación opcionales (ver Módulo 4).
  - **Cambio de Contraseña:** 
      - La vista de Perfil de Usuario (`/Account/Manage`) también incluirá un formulario dedicado para que el usuario pueda cambiar su propia contraseña. 
      - Este requerirá tres campos: `Contraseña Actual`, `Nueva Contraseña` y `Confirmación de la Nueva Contraseña`. 
      - La lógica del backend en `AccountController` utilizará un método para comprobar la contraseña anterior y registrar la nueva mediante Bcrypt. Esta acción no requerirá CAPTCHA ni código de confirmación, ya que se asume que el usuario ya ha iniciado sesión.

## 4. Módulo 2: Observaciones (Bitácora de Campo)

### 4.1. Arquitectura y Componentes Clave

  - **Backend:** Se creará un nuevo `ObservationController.cs` y `IObservationService`. Se utilizarán las entidades y DTOs existentes.
  - **Frontend:** Se creará una nueva sección "Bitácora" con vistas para listar y crear observaciones.

### 4.2. Funcionalidad

1.  **Inmutabilidad:** La lógica de negocio será estrictamente de **Creación y Lectura (CR)**. No se implementarán métodos ni endpoints para actualizar o eliminar observaciones (`Update`/`Delete`).
2.  **Creación de Observación:**
      - El formulario de creación solicitará la planta asociada (`plant_id`) y el texto de la observación (`observation_text`).
      - **Doble Verificación:** El flujo en el frontend será:
        1.  Clic en "Guardar".
        2.  Modal 1: "¿Está seguro?".
        3.  Clic en "Confirmar".
        4.  Modal 2: "Esta acción es irreversible. ¿Desea continuar?".
        5.  Clic en "Confirmar" -\> Se envía la petición al backend.
3.  **Visualización:**
      - Una vista `/Observations/Index` mostrará una tabla con todas las observaciones de todos los usuarios, incluyendo datos como la planta, el usuario que la creó, el texto y la fecha.
      - Se implementará paginación para manejar grandes volúmenes de datos.

## 5. Módulo 3: Generación de Reportes y Analíticas

### 5.1. Arquitectura y Componentes Clave

  - **Backend:** Se creará un `AnalyticsController` y un `IAnalyticsService`. Se añadirá un servicio `IPdfGeneratorService`.
  - **Frontend:** Se creará una vista compleja para la creación de máscaras y una vista para la gestión de reportes.
  - **Base de Datos:** Se utilizará la nueva columna `thermal_mask_data` en la tabla `plants`.

### 5.2. Creación de Máscara Térmica

1.  **Interfaz de Usuario:**
      - La vista `/Analytics/MaskCreator/{captureId}` mostrará la imagen RGB y la imagen térmica de una captura.
      - La imagen térmica se presentará como una **cuadrícula interactiva**.
      - El usuario podrá hacer clic en las celdas de la cuadrícula para seleccionarlas/deseleccionarlas. El conjunto de celdas seleccionadas conforma la máscara.
2.  **Almacenamiento de la Máscara:**
      - Al confirmar la máscara, el frontend enviará un array de coordenadas de los puntos seleccionados al backend.
      - El `AnalyticsService` guardará esta información en la columna `thermal_mask_data` de la tabla `plants` correspondiente a `{plantId}`, con la siguiente estructura JSON:
        ```json
        {
          "thermal_mask": {
            "type": "points",
            "coordinates": [ [x1, y1], [x2, y2], ... ]
          }
        }
        ```

### 5.3. Generación y Distribución de Reportes PDF

1.  **Selección y Análisis:**
      - El usuario seleccionará una o varias plantas y un rango de fechas en la vista de analíticas.
      - El backend recuperará los datos térmicos y usará las máscaras guardadas en `analysis_data` para realizar los cálculos (las fórmulas se definirán posteriormente).
2.  **Generación de PDF:**
      - Se utilizará una librería como **QuestPDF** o **IronPDF** para generar el archivo.
      - El archivo se creará en una **carpeta temporal** en el servidor para evitar consumir memoria excesiva.
      - El contenido base incluirá: título, imágenes, métricas básicas y placeholders para las futuras gráficas.
3.  **Distribución:**
      - La interfaz ofrecerá tres opciones para el PDF generado:
        1.  **Visualizar:** Abrir el PDF en una nueva pestaña del navegador.
        2.  **Descargar:** Bajar el archivo al disco local.
        3.  **Enviar por Correo:** Abrirá un modal con campos `Destinatario`, `Asunto`, `Mensaje` y un botón **"Enviar a mi correo"**. El backend adjuntará el PDF y lo enviará usando `IEmailService`.

## 6. Módulo 4: Sistema de Alertas y Notificaciones

### 6.1. Arquitectura y Componentes Clave

  - **Backend:** Módulo puramente de backend.
      - Nuevo servicio `IEmailService` con una implementación para la **API de Brevo** (`BrevoEmailService`).
      - Nuevo `AlertsController` con un endpoint para el webhook de Grafana.
      - Nueva lógica en `IUserService` o similar para gestionar las preferencias de notificación.

### 6.2. Endpoint para Grafana

  - **Ruta:** `POST /api/alerts/grafana-webhook`
  - **Seguridad:** El endpoint requerirá un token de autenticación (`X-Api-Key`) en la cabecera. Este token se configurará en `appsettings.json` y se validará en un middleware o filtro de acción.
  - **Payload:** Aceptará un JSON simple desde Grafana, ej: `{"alertName": "...", "message": "...", "level": "critical"}`.
  - **Lógica:** Según el `alertName`, determinará el tipo de alerta (error de dispositivo o de aplicación) y notificará a los administradores correspondientes.

### 6.3. Lógica de Alertas y Destinatarios

| Tipo de Alerta | Disparador | Lógica de Notificación | Destinatarios |
| :--- | :--- | :--- | :--- |
| **Estrés Hídrico Inicial** | Lógica de análisis del backend | Opcional, configurable por el usuario | Usuarios con la notificación activada en su perfil. |
| **Estrés Hídrico Grave** | Lógica de análisis del backend | Obligatoria | **Todos** los usuarios del sistema. |
| **Error de Dispositivo** | Grafana -\> Webhook | Fija | Todos los **Administradores**. |
| **Fallo de Aplicación** | Grafana -\> Webhook | Opcional, configurable por admin | Administradores con la notificación activada. |
| **Login Fallido (5 veces)**| `SignInManager` de Identity | Fija | Únicamente al **usuario** cuyo inicio de sesión está fallando. |
| **Inactividad de Dispositivo** | Tarea programada en backend | Fija. El servicio busca periódicamente dispositivos cuya última comunicación supere un umbral configurable (ej. 1 hora). | Administradores con la notificación activada. |

### 6.4. Gestión de Preferencias

  - Las preferencias de notificación se almacenarán en la columna `settings` de la tabla `users`.
  - El perfil de usuario (`/Account/Manage`) permitirá modificar únicamente las alertas marcadas como "Opcionales".

### **6.5. Tareas Programadas en Backend (Background Services)**
  - Para implementar alertas que no son disparadas por una acción directa del usuario o una llamada externa (como la de inactividad de dispositivo), se creará un **Servicio Alojado** que implemente la interfaz `IHostedService` de .NET. 
  - Este servicio se ejecutará en segundo plano dentro de la propia aplicación, ejecutando tareas periódicas (ej. cada 15 minutos) para verificar el estado y la última comunicación de los dispositivos. Para esto se verificará la fecha del ultimo dato ambiental enviado por el dispositivo, y si supera un umbral configurable (ej. 1 hora), se enviará una alerta a los administradores.

## 7. Consideraciones Adicionales de Arquitectura

  - **Refactorización de `Program.cs`:** La configuración de servicios, autenticación y otros middlewares se separará en métodos de extensión en archivos dedicados (ej. `DependencyInjection.cs`, `AuthenticationSetup.cs`) para mejorar la legibilidad.
  - **Almacenamiento de Archivos:** El servicio `MinioStorageService` se usará para todas las operaciones de almacenamiento de objetos, incluyendo las nuevas imágenes RGB y los archivos de reporte que se necesiten persistir.
  - **Pruebas y Calidad:** Se crearán proyectos de test (xUnit/NUnit) para cubrir la lógica de negocio crítica en los nuevos servicios. El código deberá pasar el análisis de SonarQube antes del despliegue final.
  - **Documentación:** Se generará documentación usando Doxygen y se automatizará su publicación a GitHub Pages mediante un workflow de GitHub Actions.