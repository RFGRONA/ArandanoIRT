### **Documento de Diseño Técnico y Estrategia de Despliegue (Versión 3.0)**

* **Proyecto:** Sistema de Monitoreo de Estrés Hídrico en Plantas de Arándano
* **Autor:** Gabriel Martinez (con asistencia de IA Gemini)
* **Fecha:** 18 de Junio de 2025
* **Versión:** 3.0
* **Ubicación:** Madrid, Cundinamarca, Colombia

### 1.0 Introducción y Propósito

El presente documento detalla la arquitectura técnica, la estrategia de despliegue, las operaciones y las medidas de seguridad para el "Sistema de Monitoreo de Estrés Hídrico en Plantas de Arándano". Esta versión 3.0 generaliza el diseño para ser agnóstico al proveedor de nube, enfocándose en patrones arquitectónicos robustos y portables.

### 2.0 Contexto del Proyecto

La aplicación es un sistema monolítico desarrollado en la plataforma **.NET CORE 8**, que proporciona una interfaz web y una API para la recolección y visualización de datos de sensores. El objetivo principal es la detección temprana de estrés hídrico para optimizar el riego.

### 3.0 Filosofía y Principios de Diseño

La arquitectura y el flujo de trabajo se rigen por los siguientes principios fundamentales:
* **Open Source Primero:** Se priorizará el uso de herramientas de código abierto (Docker, Caddy, PostgreSQL, MinIO, CrowdSec) para maximizar el control y minimizar costos.
* **Infraestructura como Código (IaC):** Toda la configuración de la infraestructura será definida en código (Docker Compose, scripts), versionada en Git y automatizada.
* **Portabilidad y Resiliencia:** El diseño basado en contenedores permite que la aplicación se despliegue de forma idéntica en cualquier servidor VPS que soporte Docker.
* **Seguridad y Cumplimiento por Diseño:** La seguridad y la auditoría son componentes integrales del diseño, no añadidos.

### 4.0 Arquitectura de la Infraestructura

#### 4.1 Plataforma de Alojamiento
El sistema está diseñado para ser desplegado en un **Entorno de Despliegue** único, basado en una Máquina Virtual (VM) de un proveedor de nube estándar. La arquitectura de contenedores garantiza la portabilidad entre diferentes proveedores si fuera necesario.

#### 4.2 Pila Tecnológica de Infraestructura
* **Sistema Operativo:** Ubuntu Server 24.04 LTS.
* **Orquestación:** Docker y Docker Compose.
* **Proxy Inverso:** Caddy (con gestión automática de SSL/TLS).
* **Almacenamiento de Archivos:** Se utilizará un servicio de **Almacenamiento de Objetos compatible con la API S3**, auto-hospedado en un contenedor (ej. MinIO) para los backups y archivos de la aplicación.

### 5.0 Arquitectura de la Aplicación y Desarrollo
*(Esta sección permanece mayormente sin cambios, ya que es agnóstica por naturaleza)*

#### 5.1 Pila Tecnológica de Software
* **Framework:** .NET 8 / ASP.NET Core.
* **Base de Datos:** PostgreSQL 16.
* **Acceso a Datos:** Entity Framework Core 8.

#### 5.2 Gestión de Datos y Almacenamiento
Se adopta un **modelo híbrido** con columnas tipadas para datos estructurados y un campo `JSONB` para datos de sensores flexibles.

#### 5.3 Control de Versiones de la Base de Datos
Se utilizará **Entity Framework Core Migrations** para gestionar el esquema de la base de datos de forma versionada.

#### 5.5 Logging Estructurado de Aplicación
La aplicación implementará la librería **Serilog** para emitir logs estructurados en formato JSON a la consola.

### 6.0 Sistema de Auditoría y Cumplimiento

#### 6.1 Herramienta y Configuración
Se utilizará la extensión **`pgaudit`** para PostgreSQL para auditar operaciones críticas como `ddl`, `role` y `write`.

#### 6.2 Trazabilidad End-to-End
Se vinculará la operación de base de datos con el usuario final mediante el uso de `SET application_name = 'user_id:...'` en la aplicación .NET.

#### 6.3 Infraestructura de Logs de Auditoría
Los logs de auditoría generados por `pgaudit` serán recolectados por un **Agente de Recolección de Logs** (ej. Promtail). Este agente enviará los logs de forma segura a un **Sistema Centralizado de Agregación de Logs** (ej. Loki), que también correrá en un contenedor. Esto asegura que los logs se almacenen de forma centralizada y segura. Se establecerá una política de retención para estos logs.

### 7.0 Estrategia de Operaciones y Mantenimiento (Day 2)

* **Monitoreo:** Un servicio externo como **OneUptime** se usará para el monitoreo de disponibilidad.
* **Copias de Seguridad:** Un `cron job` diario ejecutará un script que usa `pg_dump` para crear un volcado de la base de datos, el cual será subido al servicio de Almacenamiento de Objetos S3-compatible a través de `rclone`. Se establece un **RPO (Recovery Point Objective) de 24 horas**.

### 8.0 Estrategia de Seguridad General

La seguridad se implementará en capas:
1.  **Red (Nube):** Un **Firewall de Red en la Nube** para filtrar el tráfico a nivel de proveedor.
2.  **Servidor (Ubuntu):** Firewall `ufw` y `Fail2Ban`.
3.  **Aplicación (WAF):** **CrowdSec** como sistema de protección contra intrusiones (IPS).
4.  **Secretos:** Las credenciales se gestionarán mediante **Docker Secrets**.
5.  **Seguridad a Nivel de Aplicación:** Se aplicarán las mejores prácticas de seguridad en el código .NET.

### 9.0 Automatización y Flujo de Trabajo (CI/CD)

Se implementará un pipeline de CI/CD con **GitHub Actions** que automatizará la construcción, escaneo de vulnerabilidades, y despliegue de la aplicación al entorno principal.

### 10.0 Plan de Resiliencia y Contingencia

* **Falla del Servidor Principal:** El plan primario es la restauración a partir de los backups en Object Storage a una nueva VM. El servidor local sirve como respaldo de último recurso.
* **Falla de Servicios de Terceros:** Se define una estrategia de "switch" de Nameservers a nivel de registrador de dominio como plan de contingencia ante una falla global de un proveedor como Cloudflare.

### 11.0 Conclusión

Este documento establece un marco de trabajo completo para la implementación de un sistema robusto, seguro, auditable y de nivel profesional. Al adherirse a estos principios y estrategias, el proyecto no solo cumplirá con sus objetivos funcionales, sino que también representará una implementación ejemplar de las mejores prácticas de la ingeniería de software moderna, listo para su presentación académica y su potencial crecimiento futuro.