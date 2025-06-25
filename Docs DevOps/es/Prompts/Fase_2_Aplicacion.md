**Asunto:** Inicio de la Fase 2: Desarrollo y "Contenerización"
**Fecha:** 2025-06-22
**Versión del Documento Maestro:** 3.0

**--- Resumen del Contexto ---**

* **Proyecto:** Sistema de Monitoreo de Estrés Hídrico en Plantas de Arándano.
* **Objetivo:** Implementar una aplicación monolítica .NET 8 con PostgreSQL, siguiendo una arquitectura agnóstica al proveedor, auto-hospedada y basada en contenedores.
* **Pila Tecnológica Clave:** Docker, Docker Compose, Caddy, PostgreSQL, MinIO, pgaudit, Serilog, CrowdSec, y una pila de logging auto-hospedada (Loki/Promtail).
* **Repositorios Git:**
    * Repositorio de Documentación: `https://github.com/RFGRONA/ArandanoIRT`
    * Repositorio de Infraestructura: `https://github.com/RFGRONA/ArandanoIRTOps`
    * Repositorio de Software: `https://github.com/RFGRONA/ArandanoIRTSoftware`

**--- Estado Actual ---**

* **Fase Actual del Plan de Trabajo:** Fase 2: Desarrollo y "Contenerización"
* **Tarea Específica a Abordar Hoy:** Todas las tareas de la Fase 2 (2.1 y 2.2).
* **Último Logro:** Completamos toda la Fase 1. El servidor principal en nuestro proveedor de nube está aprovisionado, asegurado (con firewall y Fail2Ban) y con todo el software base (Docker, Git) instalado y listo.

**--- Mi Pregunta/Objetivo Para Hoy ---**

Mi objetivo es completar toda la Fase 2, lo que implica codificar la lógica de la aplicación y crear la orquestación de contenedores. Para ello, necesito una guía detallada y paso a paso que aborde las dos tareas principales de esta fase, explicando la interacción entre los diferentes artefactos (`Dockerfile`, `docker-compose.yml`, archivos de configuración):

1.  **Para la Tarea 2.1 (Lógica de Aplicación y Creación de la Imagen):**
    Necesito ayuda para construir la aplicación en el proyecto `.NET`. Específicamente:
    * a) Revisar y corregir las clases de entidad y el `DbContext` de **Entity Framework Core** que se mapeen correctamente a nuestro esquema de base de datos `v3.0`.
    * b) Configurar **Serilog** para tener logging estructurado en formato JSON que escriba en la consola.
    * c) Implementar un **`ActionFilter` o `Middleware`** de ASP.NET Core para pasar el `application_name` del usuario a PostgreSQL para la trazabilidad de `pgaudit`.
    * d) Crear un **`Dockerfile` multi-etapa** optimizado para compilar la aplicación en modo `Release` y generar una imagen final ligera.

2.  **Para la Tarea 2.2 (Orquestación con Docker Compose):**
    Necesito ayuda en la construcción del archivo `docker-compose.yml` en el repositorio `ArandanoIRTOps`, detallando cómo cada servicio se configura y se interconecta:
    * a) **Servicio PostgreSQL:** Definir el servicio `postgres`, incluyendo un **volumen para montar un archivo `postgresql.conf` personalizado** desde el host para activar `pgaudit`.
    * b) **Servicio de la Aplicación .NET:** Definir el servicio `arandano-app` para que **use el `Dockerfile` del repositorio `ArandanoIRTSoftware`** (con la instrucción `build: context`). Además, inyectar la cadena de conexión de forma segura como **variable de entorno**.
    * c) **Servicios de Infraestructura:** Proporcióname las definiciones de servicio para **MinIO** (almacenamiento S3) y la pila de logging completa (**Loki, Promtail, Grafana**), incluyendo la configuración de volúmenes para la persistencia de sus datos.
    * d) **Red y Dependencias:** Asegúrate de que todos los servicios se comuniquen en la **misma red de Docker** y usa `depends_on` para gestionar el orden de arranque correcto.
    * e) **Proxy Inverso:** Incluye una definición básica para el servicio de **Caddy**, que dejaremos lista para configurar con el dominio en la Fase 3.