**Asunto:** Inicio de la Fase 2: Desarrollo y "Contenerización"
**Fecha:** 18 de Junio de 2025
**Versión del Documento Maestro:** 3.0

**--- Resumen del Contexto ---**

* **Proyecto:** Sistema de Monitoreo de Estrés Hídrico en Plantas de Arándano.
* **Objetivo:** Implementar una aplicación monolítica .NET 8 con PostgreSQL, siguiendo una arquitectura robusta, segura y replicable.
* **Pila Tecnológica Clave:** Docker, Docker Compose, Caddy, PostgreSQL, MinIO, pgaudit, Serilog, CrowdSec.
* **Repositorios Git:**
    * Repositorio de Documentación: `https://github.com/RFGRONA/ArandanoIRT`
    * Repositorio de Infraestructura: `https://github.com/RFGRONA/ArandanoIRTOps`
    * Repositorio de Software: `https://github.com/RFGRONA/ArandanoIRTSoftware`

**--- Estado Actual ---**

* **Fase Actual del Plan de Trabajo:** Fase 2: Desarrollo y "Contenerización"
* **Tarea Específica a Abordar Hoy:** Tarea 2.1: Implementación de la Lógica de Aplicación y Datos
* **Último Logro:** Se completó toda la Fase 1. El servidor VPS está aprovisionado, asegurado (firewall de nube y `ufw`), actualizado, y tiene todo el software base (Docker, Git) instalado.

**--- Mi Pregunta/Objetivo Para Hoy ---**

Mi objetivo hoy es comenzar a escribir el código .NET para la aplicación, enfocándome en la **Tarea 2.1**. Necesito ayuda para:

1.  **Crear las Clases de Entidad de EF Core:** ¿Cómo defino las clases en C# para que se mapeen correctamente al esquema de base de datos que diseñamos (ej. `users`, `devices`, `environmental_readings`), especialmente cómo manejar la relación entre ellas y el tipo `JSONB` para la columna `extra_data`? Teniendo en cuenta que anteriormente estaba usando Supabase, necesitamos migrar todo a PostgreSQL y la nueva base de datos.
2.  **Configurar el DbContext:** ¿Qué configuración se necesita en el `DbContext` para que Entity Framework Core entienda los tipos `ENUM` de PostgreSQL que creamos y la columna `JSONB`?
3.  **Implementar el Logging Estructurado:** ¿Cuál es la configuración básica de Serilog en `Program.cs` para que escriba logs en formato JSON a la consola, como se especificó en el diseño?