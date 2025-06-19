### **Plan de Trabajo Detallado (Versión 3.0): Arquitectura Agnóstica**

* **Proyecto:** Sistema de Monitoreo de Estrés Hídrico en Plantas de Arándano
* **Fecha de Revisión:** 18 de Junio de 2025
* **Versión:** 3.0

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

### **Fase 3: Despliegue y Validación**

* **Tarea 3.1: Configuración de DNS y Almacenamiento.**
    * **Acción:** Apuntar el dominio a la IP pública de la VM. Configurar los buckets iniciales en el servicio de Almacenamiento de Objetos.
    * **Entregable:** El dominio resuelve a la IP del servidor.

* **Tarea 3.2: Despliegue Inicial y Verificación.**
    * **Acción:** Ejecutar `docker-compose up -d` en el servidor y realizar una verificación funcional básica (acceso web, certificado SSL generado por Caddy, funcionalidad principal). La aplicación debe aplicar las migraciones de EF Core al iniciarse.
    * **Entregable:** La aplicación está en línea y funcionando correctamente.

### **Fase 4: Implementación de Sistemas Operativos**

* **Tarea 4.1: Verificación del Sistema de Auditoría.**
    * **Acción:** Realizar operaciones CRUD en la aplicación y navegar a la interfaz del sistema de logs (ej. Grafana) para verificar que los eventos de `pgaudit` están llegando y registrando la información correctamente.
    * **Entregable:** Confirmación (ej. captura de pantalla) de que los logs de auditoría son visibles.

* **Tarea 4.2: Implementación de Backups Automatizados.**
    * **Acción:** Crear el script de backup, configurar `rclone` para conectarse al servicio de Almacenamiento de Objetos, y programar el `cron job` para la ejecución diaria.
    * **Entregable:** Un backup `.sql.gz` generado y subido automáticamente.

* **Tarea 4.3: Configuración del Monitoreo de Funcionamiento.**
    * **Acción:** En un servicio como OneUptime, configurar los monitores de Uptime y Health Check para la URL del proyecto.
    * **Entregable:** Alertas de monitoreo activas.

### **Fase 5: Pruebas de Resiliencia**

* **Tarea 5.1: Simulacro de Restauración de Desastres.**
    * **Acción:** Realizar una prueba completa de restauración de la base de datos a partir de un backup almacenado en el servicio de Object Storage.
    * **Entregable:** Procedimiento de restauración validado y documentado.

### **Fase 6: Automatización (CI/CD)**

* **Tarea 6.1: Configuración del Pipeline de CI/CD.**
    * **Acción:** Configurar el registro de contenedores y crear el workflow de GitHub Actions (con jobs para `build`, `test` y `deploy` al entorno principal).
    * **Entregable:** Un despliegue exitoso a través de una acción de GitHub.

### **Fase 7: Documentación Final y Entrega**

* **Tarea 7.1: Creación del Manual de Usuario.**
    * **Acción:** Redactar una guía simple para el usuario final sobre cómo utilizar la aplicación web.
* **Tarea 7.2: Creación del Manual Técnico/de Operaciones.**
    * **Acción:** Documentar cómo desplegar el proyecto desde cero, cómo realizar backups/restauraciones, y cómo monitorear el sistema.
* **Tarea 7.3: Limpieza y Publicación Final.**
    * **Acción:** Asegurarse de que todos los READMEs de los repositorios estén actualizados, el código comentado y la Wiki del proyecto organizada para la presentación final.