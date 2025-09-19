### **Plan de Trabajo Detallado (Versión 4.0): Arquitectura Agnóstica**

* **Proyecto:** Sistema de Monitoreo de Estrés Hídrico en Plantas de Arándano
* **Fecha de Revisión:** 26 de Junio de 2025
* **Versión:** 4.0

Este plan de trabajo actualiza la versión anterior para reflejar una arquitectura agnóstica al proveedor, enfocada en la portabilidad y el despliegue en un único entorno principal.

---

### **Fase 0: Planificación y Diseño Estratégico**

* **Tarea 0.1: Diseño y Modelado de la Arquitectura de Infraestructura.**
    * **Acción:** Crear un diagrama de arquitectura que visualice los componentes en un **único entorno de despliegue conceptual**, sus interacciones y los servicios externos.
    * **Entregable:** Diagrama de arquitectura v3.0 en la carpeta `/docs`.

* **Tarea 0.2: Diseño del Esquema de la Base de Datos.**
    * **Acción:** Diseñar el modelo de datos detallado, definiendo la estrategia híbrida de columnas tipadas y un campo `JSONB`, junto con la estrategia de indexación.
    * **Entregable:** Documento de diseño de base de datos y script SQL inicial.

* **Tarea 0.3: Diseño del Sistema de Auditoría.**
    * **Acción:** Redactar una política de auditoría formal que defina qué auditar (usando `pgaudit`), dónde almacenar (en un **sistema de logs centralizado**) y por cuánto tiempo retener los logs.
    * **Entregable:** Documento de "Política de Auditoría" en la carpeta `/docs`.

### **Fase 1: Preparación del Entorno**

* **Tarea 1.1: Aprovisionamiento de la Máquina Virtual (VM).**
    * **Acción:** Crear la VM principal en el proveedor de nube seleccionado.
    * **Entregable:** Una VM activa y accesible vía SSH.

* **Tarea 1.2: Configuración de la Seguridad de Red.**
    * **Acción:** Configurar el **Firewall de Red en la Nube** para permitir tráfico únicamente en los puertos necesarios (ej. 22, 80, 443).
    * **Entregable:** Reglas de firewall aplicadas a la VM.

* **Tarea 1.3: Instalación de Software Base y Seguridad del Servidor.**
    * **Acción:** Conectarse a la VM, actualizar el sistema operativo, e instalar el software esencial como Docker, Docker Compose y Git. Configurar la seguridad básica del host (ej. `ufw`, `Fail2Ban`).
    * **Entregable:** Servidor actualizado y con el software necesario para el despliegue.

### **Fase 2: Desarrollo y "Contenerización"**

* **Tarea 2.1: Implementación de la Lógica de Aplicación y Datos.**
    * **Acción:** En el código .NET, implementar el modelo de datos, el logging estructurado con Serilog, y la lógica para la trazabilidad de auditoría (`SET application_name`). Generar las migraciones iniciales de EF Core.
    * **Entregable:** Código fuente funcional y versionado en Git.

* **Tarea 2.2: Creación de Artefactos de Orquestación.**
    * **Acción:** Crear un archivo `docker-compose.yml` que orqueste todos los servicios: la aplicación .NET, PostgreSQL (configurado para usar `pgaudit`), un servidor de **Almacenamiento de Objetos S3-compatible** (ej. MinIO), y la **pila de logging** (ej. Loki, Promtail).
    * **Entregable:** Archivo `docker-compose.yml` completo y funcional.

### **Fase 3: Despliegue y Puesta en Marcha (Completada)**

* **Tarea 3.1: Configuración de Dominio y DNS.**
    * **Acción:** Apuntar el dominio `arandanoirt.co` y subdominios a la IP pública del VPS a través de Cloudflare o servicio DNS alternativo.
    * **Entregable:** El dominio resuelve a la IP del servidor protegido por el servicio de DNS establecido.
* **Tarea 3.2: Despliegue con Caddy y HTTPS.**
    * **Acción:** Ejecutar `docker-compose up -d` en el servidor y verificar el acceso web y los certificados SSL generados por Caddy.
    * **Entregable:** La aplicación y los servicios de gestión están en línea y funcionando correctamente.
* **Tarea 3.3: Implementación de Backups Automatizados.**
    * **Acción:** Crear un script de backup (`backup_postgres.sh`) que use `pg_dump` y `mc`, y programar un `cronjob` para su ejecución diaria.
    * **Entregable:** Un sistema de backup automático y funcional que almacena los respaldos en MinIO.

### **Fase 4: Observabilidad y Monitoreo**

* **Tarea 4.1: Configuración de Grafana y Dashboards.**
    * **Acción:** Conectar Grafana a la fuente de datos de Loki. Crear un dashboard inicial que muestre los logs de la aplicación .NET y los logs de auditoría de `pgaudit`. Configurar alertas básicas para errores críticos (ej. nivel `Error` o `Fatal`).
    * **Entregable:** Un dashboard funcional en Grafana que permita la visualización y consulta de logs centralizados.
* **Tarea 4.2: Configuración del Monitoreo Externo (OneUptime).**
    * **Acción:** Registrar el proyecto en OneUptime. Configurar monitores de `Health Check` para los endpoints `https://arandanoirt.co`, `https://grafana.arandanoirt.co` y `https://minio.arandanoirt.co`. Crear y publicar una página de estado pública.
    * **Entregable:** Alertas de monitoreo externas activas y una página de estado para comunicar la disponibilidad del servicio.
* **Tarea 4.3: Pruebas Funcionales y Verificación de Logs.**
    * **Acción:** Realizar operaciones CRUD (Crear, Leer, Actualizar, Borrar) en la aplicación web. Posteriormente, navegar al dashboard de Grafana para verificar que los logs de auditoría de `pgaudit` y los logs de la aplicación reflejan estas acciones correctamente.
    * **Entregable:** Confirmación de que los logs de actividad son visibles y correctos en Grafana.

### **Fase 5: Automatización (CI/CD)**

* **Tarea 5.1: Configuración del Pipeline de CI/CD con GitHub Actions.**
    * **Acción:** Configurar un registro de contenedores (ej. GitHub Container Registry). Crear los "Actions Secrets" en GitHub para las variables de entorno sensibles. Desarrollar un workflow de GitHub Actions (`.github/workflows/deploy.yml`) con jobs para `build`, `test` y `deploy`. El job de despliegue se conectará al VPS vía SSH para hacer `pull` de la nueva imagen y reiniciar el servicio.
    * **Entregable:** Un despliegue exitoso a producción provocado por un `push` a la rama `main` del repositorio `ArandanoIRTSoftware`.

### **Fase 6: Resiliencia y Documentación Final**

* **Tarea 6.1: Documentación del Plan de Recuperación de Desastres (DRP).**
    * **Acción:** Crear un documento detallado (`Guia_Restauracion.md`) que explique paso a paso cómo restaurar la base de datos a partir de un backup almacenado en MinIO. Incluir el procedimiento para levantar un nuevo servidor desde cero si fuera necesario.
    * **Entregable:** Un manual de operaciones de emergencia validado conceptualmente.
* **Tarea 6.2: Creación de Manuales de Usuario y Operaciones.**
    * **Acción:** Redactar una guía simple para el usuario final y un manual técnico que documente la arquitectura, el despliegue y el mantenimiento del sistema.
* **Tarea 6.3: Limpieza y Publicación Final.**
    * **Acción:** Asegurarse de que todos los READMEs de los repositorios estén actualizados, el código comentado y la documentación del proyecto organizada para la presentación final.