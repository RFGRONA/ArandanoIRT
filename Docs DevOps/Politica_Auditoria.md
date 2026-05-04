# Política de Auditoría de Base de Datos v1.0

* **Proyecto:** Sistema de Monitoreo de Estrés Hídrico en Plantas de Arándano
* **Versión:** 1.0
* **Fecha:** 2025-06-18

### 1. Propósito
Esta política define las directrices para la auditoría de la base de datos PostgreSQL del proyecto. Los objetivos principales son garantizar la integridad de los datos, detectar actividades no autorizadas y mantener el cumplimiento con las mejores prácticas de la industria y los marcos regulatorios aplicables, como la **Ley 1581 de 2012 de Colombia (Habeas Data)**.

### 2. Alcance
Esta política aplica a la base de datos PostgreSQL que opera en el entorno de **Producción**, alojado en Oracle Cloud Infrastructure (OCI). Los entornos de Staging y Desarrollo Local están excluidos de esta política de auditoría formal.

### 3. Herramienta y Configuración de Auditoría
* **Herramienta:** Se utilizará la extensión oficial de PostgreSQL **`pgaudit`** para todos los registros de auditoría.
* **Configuración:** `pgaudit` será configurado para registrar las siguientes clases de operaciones para capturar todos los eventos críticos:
    * **`DDL`**: Para auditar todos los comandos de Definición de Datos (ej. `CREATE`, `ALTER`, `DROP TABLE`).
    * **`ROLE`**: Para auditar todos los cambios relacionados con roles y permisos (ej. `GRANT`, `REVOKE`).
    * **`WRITE`**: Para auditar operaciones de modificación de datos (ej. `INSERT`, `UPDATE`, `DELETE`).

### 4. Almacenamiento y Retención de Logs
* **Destino de Almacenamiento:** Todos los logs de auditoría generados por `pgaudit` serán capturados por el **Agente de Monitoreo Unificado de OCI** y transmitidos de forma segura al servicio **OCI Logging**. Esto asegura que los logs se almacenen fuera del servidor en una ubicación centralizada e inmutable.
* **Periodo de Retención:** Se establecerá una política de retención inmutable de **24 meses** para todos los logs de auditoría almacenados en OCI Logging, para cumplir con los requerimientos de cumplimiento y análisis forense.

### 5. Trazabilidad de Usuario Final
Para vincular una operación de base de datos con un usuario final específico, la aplicación .NET establecerá una variable de sesión antes de ejecutar las transacciones que requieran auditoría.
* **Mecanismo:** La aplicación ejecutará el comando `SET application_name = 'user_id:<ID_DEL_USUARIO>'`.
* **Resultado:** La variable `application_name`, que contiene el ID único del usuario, será capturada por `pgaudit` en la entrada del log, proveyendo una traza de auditoría completa e inequívoca desde la interfaz web hasta el registro en la base de datos.

### 6. Revisión de la Política
Esta política será revisada anualmente o siempre que ocurra un cambio significativo en la arquitectura del sistema o en los requerimientos regulatorios.