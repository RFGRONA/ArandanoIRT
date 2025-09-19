**Asunto:** Inicio de la Fase 1: Preparación de Entornos
**Fecha:** 2025-06-18
**Versión del Documento Maestro:** 2.1 

**--- Resumen del Contexto ---**

* **Proyecto:** Sistema de Monitoreo de Estrés Hídrico en Plantas de Arándano.
* **Objetivo:** Implementar una aplicación monolítica .NET 8 con PostgreSQL, siguiendo la arquitectura robusta, segura y replicable definida en la Fase 0.
* **Pila Tecnológica Clave:** Docker, Docker Compose, Caddy, PostgreSQL, OCI (ARM VM + Object Storage), MinIO, pgaudit, Serilog, CrowdSec.
* **Repositorio Git :** 
    - Repositorio de Documentación: `https://github.com/RFGRONA/ArandanoIRT` 
    - Repositorio de Infraestructura: `https://github.com/RFGRONA/ArandanoIRTOps` 

**--- Estado Actual ---**

* **Fase Actual del Plan de Trabajo:** Fase 1: Preparación de Entornos
* **Tarea Específica a Abordar Hoy:** Todas las tareas de la Fase 1 (1.1 - 1.6).
* **Último Logro:** Completamos toda la Fase 0 de Planificación. Tenemos un diagrama de arquitectura, un esquema de base de datos finalizado y una política de auditoría formal. Todos los documentos están actualizados en sus repositorios correspondientes.

**--- Mi Pregunta/Objetivo Para Hoy ---**

Quiero empezar con la Fase 1. Mi objetivo hoy es aprovisionar y asegurar la Máquina Virtual (VM) en Oracle Cloud Infrastructure (OCI) que servirá como nuestro entorno de producción. Necesito una guía paso a paso con los comandos recomendados para alguien con experiencia básica en la nube, cubriendo:

1.  Los pasos clave para crear la instancia de cómputo Ampere A1.
2.  La configuración inicial de la red (VCN y Listas de Seguridad/Security Lists) para permitir solo el tráfico necesario (SSH por el puerto 22, HTTP por el 80 y HTTPS por el 443).
3.  Las primeras acciones de seguridad recomendadas en el servidor Ubuntu una vez que me conecte por SSH (actualizar el sistema, crear un usuario no-root con privilegios `sudo`, y configurar el firewall `ufw`).