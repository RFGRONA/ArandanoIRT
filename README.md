# Sistema para la Detección de Estrés Hídrico en el Arándano Biloxi mediante Termografía de Bajo Costo

<div align="center">

[![Libro de Grado](https://img.shields.io/badge/Libro_de_Grado-PDF-DC143C?logo=latex&logoColor=white)](./Libro%20de%20Grado/main.pdf)
[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](./LICENSE)
[![Universidad de Cundinamarca](https://img.shields.io/badge/Universidad_de_Cundinamarca-Facatativá-004A97)](https://www.ucundinamarca.edu.co/)
[![GISTFA](https://img.shields.io/badge/Grupo_GISTFA-LINUDECX-2E8B57)](https://www.ucundinamarca.edu.co/)
[![Ingeniería de Sistemas](https://img.shields.io/badge/Ingeniería_de_Sistemas_y_Computación-2026-4B0082)](https://www.ucundinamarca.edu.co/)

**Repositorio central del proyecto de grado AIRT (*Arándano IRT*) — contiene el libro de grado en LaTeX, el modelado UML y la arquitectura de software y DevOps del sistema de monitoreo agrícola de precisión.**

[Descripción del Sistema](#-descripción-del-sistema) · [Arquitectura Global](#-arquitectura-global-del-sistema) · [Repositorios del Ecosistema](#-ecosistema-de-repositorios) · [Documentación](#-documentación-y-modelado) · [Libro de Grado](#-libro-de-grado-latex) · [Autores](#-autores)

</div>

---

## 📋 Descripción del Sistema

**AIRT** (*Arándano IRT*) es un sistema integral de monitoreo agrícola de precisión orientado a la **detección temprana del estrés hídrico** en cultivos de arándano (*Vaccinium corymbosum* L., variedad Biloxi). El sistema aborda una brecha tecnológica crítica en el sector agrícola: la imposibilidad de los pequeños y medianos productores de acceder a tecnología termográfica comercial, cuyo costo resulta prohibitivo.

La propuesta de valor del sistema reside en la combinación de **hardware de bajo costo** basado en sensores de matriz térmica (MLX90640) con un **stack de software completo y de nivel productivo**, que incluye nodos IoT embebidos, una plataforma web de gestión, un microservicio de inteligencia artificial (RAG), y una infraestructura de despliegue automatizada. El sistema calcula el **Índice de Estrés Hídrico del Cultivo (CWSI)** como métrica principal, complementado por indicadores de diferencial de temperatura (ΔT) y un modelo de inferencia ONNX, para proporcionar recomendaciones agronómicas objetivas y basadas en datos.

### Contexto Académico

Este proyecto constituye el trabajo de grado de la carrera de **Ingeniería de Sistemas y Computación** de la **Universidad de Cundinamarca — Extensión Facatativá**, desarrollado bajo el auspicio del **Grupo de Investigación de Sistemas y Tecnología de Facatativá (GISTFA)** y el semillero **LINUDECX**.

| Campo | Detalle |
|---|---|
| **Título** | Sistema para la Detección de Estrés Hídrico en el Arándano Biloxi mediante Termografía de Bajo Costo |
| **Modalidad** | Proyecto de investigación aplicada |
| **Directora** | Mg. Gina Maribel Valenzuela Sabogal |
| **Institución** | Universidad de Cundinamarca, Facultad de Ingeniería |
| **Programa** | Ingeniería de Sistemas y Computación |
| **Año** | 2026 |

> [!IMPORTANT]
> Este repositorio es el **nodo central** del proyecto. No contiene código fuente ejecutable de aplicaciones, sino el **libro de grado en LaTeX**, los **artefactos de modelado UML** y la **documentación de diseño técnico** de software y DevOps. El código de cada componente vive en su repositorio dedicado (ver [Ecosistema de Repositorios](#-ecosistema-de-repositorios)).

---

## 🏛️ Arquitectura Global del Sistema

El sistema AIRT se articula como un **ecosistema distribuido de múltiples capas** que van desde el hardware embebido hasta la presentación web, pasando por una infraestructura en la nube completamente automatizada:

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                          CAPA DE CAMPO (Hardware IoT)                           │
│                                                                                 │
│   ┌─────────────────────────────────────────────────────────────┐               │
│   │   Nodo Sensor ESP32-S3 (ArandanoIRTFirmware)                │               │
│   │   MLX90640 (Térmica) · OV2640 (Visual) · BME280 · BH1750   │               │
│   │   DS18B20 · MicroSD · LittleFS · AES-256-GCM · JWT         │               │
│   └──────────────────────────────┬──────────────────────────────┘               │
└─────────────────────────────────│───────────────────────────────────────────────┘
                                  │ HTTPS / Multipart
                                  ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│                         CAPA DE APLICACIÓN (Software)                           │
│                                                                                 │
│   ┌──────────────────────────────────────────────────────────────────────────┐  │
│   │               Plataforma Web AIRT — ASP.NET Core 8 MVC                   │  │
│   │      (ArandanoIRTSoftware)                                               │  │
│   │                                                                          │  │
│   │  ┌────────────────┐  ┌────────────────┐  ┌────────────────────────────┐ │  │
│   │  │   Dashboard    │  │  Gestión de    │  │   Análisis CWSI / ΔT       │ │  │
│   │  │  Tiempo Real   │  │ Cultivos,      │  │   Modelo ONNX (DT)         │ │  │
│   │  │                │  │ Dispositivos   │  │   Generación de Reportes   │ │  │
│   │  └────────────────┘  └────────────────┘  └────────────────────────────┘ │  │
│   │                                                                          │  │
│   │  ┌─────────────────────────────────────────────────────────────────┐    │  │
│   │  │     Microservicio IA — RAG (ArandanoIRT-ML)                     │    │  │
│   │  │     FastAPI · ChromaDB · Gemini 2.5 · MiniLM (Embeddings)      │    │  │
│   │  └─────────────────────────────────────────────────────────────────┘    │  │
│   └──────────────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────────────┘
                                  │
                                  ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│                        CAPA DE INFRAESTRUCTURA (DevOps)                         │
│                                                                                 │
│   ┌──────────────────────────────────────────────────────────────────────────┐  │
│   │          VPS Linux — Docker Compose (ArandanoIRTOps)                     │  │
│   │                                                                          │  │
│   │  Caddy (TLS) · PostgreSQL 16 · MinIO (S3) · Loki · Promtail · Grafana  │  │
│   │  GitHub Actions CI/CD → GHCR → Deploy SSH                              │  │
│   └──────────────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

### Stack Tecnológico Consolidado

| Capa | Tecnología | Propósito |
|---|---|---|
| **Firmware** | ESP32-S3, Arduino/PlatformIO, C++ | Adquisición de datos de campo y comunicación IoT |
| **Backend** | ASP.NET Core 8, EF Core, PostgreSQL 16 | Plataforma web, API IoT, análisis CWSI/ONNX |
| **IA / RAG** | FastAPI, Python 3.12, ChromaDB, Gemini 2.5 | Asistente agronómico con recuperación aumentada |
| **Almacenamiento** | MinIO (S3-compatible) | Imágenes térmicas y objetos binarios |
| **Observabilidad** | Loki + Promtail + Grafana | Agregación de logs y dashboards operacionales |
| **Proxy / TLS** | Caddy | Reverse proxy con certificados Let's Encrypt automáticos |
| **CI/CD** | GitHub Actions, GHCR, SSH Deploy | Entrega continua de software e infraestructura |
| **Documento** | LaTeX (APA, apacite) | Libro de grado con bibliografía científica |

---

## 🗂️ Ecosistema de Repositorios

El proyecto está organizado en **cuatro repositorios especializados** con responsabilidades bien delimitadas, más este repositorio central de documentación:

| Repositorio | Rol | Descripción |
|---|---|---|
| **[`ArandanoIRT`](https://github.com/rfgrona/ArandanoIRT)** (este) | **Documentación Central** | Libro de grado LaTeX, modelado UML, diseño técnico de Software y DevOps |
| **[`ArandanoIRTSoftware`](https://github.com/rfgrona/ArandanoIRTSoftware)** | Aplicación Web | Plataforma ASP.NET Core 8 MVC: gestión de cultivos, análisis CWSI, asistente RAG, API IoT |
| **[`ArandanoIRTOps`](https://github.com/rfgrona/ArandanoIRTOps)** | Infraestructura (IaC) | Docker Compose, Caddy, Loki/Grafana, pipelines CI/CD, scripts de operaciones |
| **[`ArandanoIRTFirmware`](https://github.com/rfgrona/ArandanoIRTFirmware)** | Firmware Embebido | PlatformIO/ESP32-S3: captura térmica/visual, sensores ambientales, cola offline, JWT |
| **[`ArandanoIRT-ML`](https://github.com/rfgrona/ArandanoIRT-ML)** | Microservicio IA | FastAPI + RAG: asistente agrícola con ChromaDB, Gemini 2.5 y embeddings multilingüe |

### Guía de Navegación

```
Si quieres...                                     Ve a...
──────────────────────────────────────────────────────────────────
Leer el libro de grado completo (PDF)          → Libro de Grado/main.pdf
Revisar el modelado UML del sistema            → Libro de Grado/UML/
Explorar el diseño técnico de software        → Docs Software/
Explorar el diseño técnico de infraestructura → Docs DevOps/
Consultar los manuales de usuario y técnicos  → Manuales/
Clonar y ejecutar la aplicación web            → ArandanoIRTSoftware + ArandanoIRTOps
Flashear el firmware en un nodo ESP32-S3       → ArandanoIRTFirmware
Desplegar el microservicio IA                  → ArandanoIRT-ML
```

---

## 📐 Documentación y Modelado

Este repositorio centraliza todos los artefactos de ingeniería del proyecto. La estructura de carpetas refleja las dos grandes áreas de diseño del sistema:

### `Docs Software/` — Diseño Técnico de la Aplicación

Contiene los documentos de diseño técnico que guiaron el desarrollo de la plataforma web:

| Documento | Descripción |
|---|---|
| [`Diseño_Tecnico.md`](./Docs%20Software/Diseño_Tecnico.md) | Especificación de los módulos de software: gestión de usuarios, observaciones de campo, generación de reportes PDF, sistema de alertas y servicios en segundo plano. Incluye decisiones de arquitectura, modelo de datos y estrategia de integración con Cloudflare Turnstile, Brevo y MinIO. |
| [`Plan_Trabajo.md`](./Docs%20Software/Plan_Trabajo.md) | Cronograma y organización del trabajo de desarrollo del software. |
| [`Prompt_Maestro.md`](./Docs%20Software/Prompt_Maestro.md) | Contexto de dominio y directrices utilizadas para asistencia con IA en el desarrollo. |

### `Docs DevOps/` — Diseño Técnico de Infraestructura

Contiene la especificación de la estrategia de despliegue, operaciones y seguridad del sistema:

| Documento | Descripción |
|---|---|
| [`Diseño_Tecnico.md`](./Docs%20DevOps/Diseño_Tecnico.md) | Estrategia de despliegue v3.0: arquitectura IaC, stack de infraestructura (Caddy, Docker, PostgreSQL, MinIO), sistema de auditoría con `pgaudit`, pipeline CI/CD con GitHub Actions, estrategia de seguridad por capas (WAF, UFW, Fail2Ban, CrowdSec) y plan de resiliencia. |
| [`Modelado/`](./Docs%20DevOps/Modelado/) | Diagramas de arquitectura de la infraestructura (draw.io, PNG, SVG) y modelo simplificado de la base de datos. |
| [`Plan_Trabajo.md`](./Docs%20DevOps/Plan_Trabajo.md) | Cronograma y organización del trabajo de DevOps. |
| [`Politica_Auditoria.md`](./Docs%20DevOps/Politica_Auditoria.md) | Política formal de auditoría de base de datos con `pgaudit` y política de retención de logs. |

### `Manuales/` — Documentación de Usuario y Técnica

Manuales de referencia para operadores, usuarios finales y técnicos del sistema:

| Manual | Descripción |
|---|---|
| [`Manual_Tecnico_Software.md`](./Manuales/Manual_Tecnico_Software.md) | Referencia técnica detallada de la aplicación web: arquitectura, base de datos, servicios, APIs y guías de configuración para desarrolladores. |
| [`Manual_Tecnico_Operaciones.md`](./Manuales/Manual_Tecnico_Operaciones.md) | Guía de operaciones y mantenimiento de la infraestructura: gestión de contenedores, backups, rotación de secretos y procedimientos de escalado. |
| [`Manual_Usuario_Software.md`](./Manuales/Manual_Usuario_Software.md) | Manual de usuario final de la plataforma web: flujos de navegación, gestión de cultivos, interpretación de indicadores CWSI y uso del asistente IA. |
| [`Guia_Recuperacion_Desastres.md`](./Manuales/Guia_Recuperacion_Desastres.md) | Procedimientos de recuperación ante desastres: restauración desde backup, conmutación de DNS y validación post-restauración. |
| [`Configuracion_Alertas.md`](./Manuales/Configuracion_Alertas.md) | Guía de configuración del sistema de alertas: Grafana Webhooks, Brevo, umbrales de estrés y preferencias de notificación. |

---

## 📖 Libro de Grado (LaTeX)

El directorio [`Libro de Grado/`](./Libro%20de%20Grado/) contiene el código fuente LaTeX completo del documento de investigación, compilado bajo el estilo académico de la Universidad de Cundinamarca con formato APA.

### Estructura del Documento

| Capítulo | Archivo | Contenido |
|---|---|---|
| **Introducción** | `main.tex` | Contexto, justificación y estructura del documento |
| **Cap. I — Investigación** | `capitulo_investigacion.tex` | Marco teórico, estado del arte, planteamiento del problema, objetivos, alcance y metodología |
| **Cap. II — Software** | `capitulo_software.tex` | Arquitectura MVC/Onion, requerimientos, diseño UML completo (CU, Secuencia, Actividad, Clases, Despliegue), estimación por Puntos de Casos de Uso |
| **Cap. III — Hardware** | `capitulo_hardware.tex` | Selección y caracterización metrológica de componentes: MLX90640, BME280, BH1750, DS18B20, OV2640 y ESP32-S3 |
| **Cap. IV — Experimento** | `capitulo_experimento.tex` | Prueba de concepto (n=1, 55 días): protocolo de estrés hídrico inducido, recolección de datos, resultados de respuesta térmica (ΔT, CWSI) |
| **Cap. V — Conclusiones** | `capitulo_conclusiones.tex` | Resultados integrados, consecución de objetivos, limitaciones, impacto y trabajo futuro |
| **Bibliografía** | `bibliografia.bib` | Referencias bibliográficas en formato BibTeX (APA) |

### Compilar el Documento

**Requisitos:** LaTeX con `pdflatex` o `latexmk` y los paquetes: `babel` (spanish), `apacite`, `newtxtext`, `listings`, `booktabs`, `siunitx`, `mhchem`, `pdfpages`.

```bash
cd "Libro de Grado"

# Compilación completa con bibliografía APA
latexmk -pdf -bibtex main.tex

# O manualmente:
pdflatex main.tex
bibtex main
pdflatex main.tex
pdflatex main.tex
```

> El documento compilado se encuentra en [`Libro de Grado/main.pdf`](./Libro%20de%20Grado/main.pdf).

### Modelado UML

El directorio [`Libro de Grado/UML/`](./Libro%20de%20Grado/UML/) contiene el proyecto Visual Paradigm (`ArandanoIRT.vpp`) y exportaciones PNG de todos los diagramas:

| Tipo | Contenido |
|---|---|
| **Casos de Uso** (`CasosUso/`) | 8 diagramas de casos de uso (RF1–RF9): gestión de cultivos, plantas, dispositivos, observaciones, análisis, alertas y administración |
| **Clases** (`Clases/`) | Diagrama de clases del dominio y arquitectura por capas |
| **Secuencia** (`Secuencia/`) | Flujos de interacción clave: autenticación, ciclo IoT, análisis CWSI, consulta RAG |
| **Actividad** (`Actividad/`) | Diagramas de flujo de los procesos de negocio principales |
| **Otros** (`Otros/`) | Diagramas adicionales de despliegue y componentes |

---

## 🔬 Alcance y Resultados del Proyecto

El proyecto demostró la **viabilidad técnica y económica** de un sistema de monitoreo termográfico de bajo costo para la agricultura de precisión:

- ✅ **Hardware validado**: Caracterización metrológica del sensor MLX90640 y componentes ambientales en condiciones controladas, confirmando consistencia de medición con componentes nuevos y bien seleccionados.
- ✅ **Prueba de concepto exitosa**: Monitoreo continuo durante 55 días de una planta de arándano Biloxi en un protocolo de estrés hídrico inducido y recuperación, con detección clara de la respuesta térmica mediante ΔT y CWSI.
- ✅ **Sistema de software completo**: Plataforma web en producción con análisis automatizado, asistente IA con RAG, alertas por correo, reportes PDF y pipeline CI/CD funcional.
- ✅ **Infraestructura de nivel productivo**: Despliegue en VPS con TLS automático, observabilidad con Loki/Grafana, backups automatizados y auditoría de base de datos.
- ✅ **Firmware robusto**: Nodo IoT con cola offline, cifrado AES-256-GCM, portal de configuración embebido y autenticación JWT sin intervención humana.

---

## 📂 Estructura del Repositorio

```
ArandanoIRT/                         ← Este repositorio (documentación central)
│
├── Libro de Grado/                  # Código fuente LaTeX del trabajo de grado
│   ├── main.tex                     # Documento maestro (portada, resumen, estructura)
│   ├── main.pdf                     # ← Documento compilado (PDF final)
│   ├── capitulo_investigacion.tex   # Cap. I: Investigación y marco teórico
│   ├── capitulo_software.tex        # Cap. II: Documentación de software y UML
│   ├── capitulo_hardware.tex        # Cap. III: Hardware y validación metrológica
│   ├── capitulo_experimento.tex     # Cap. IV: Estudio experimental (PoC)
│   ├── capitulo_conclusiones.tex    # Cap. V: Conclusiones y trabajo futuro
│   ├── bibliografia.bib             # Referencias bibliográficas (BibTeX/APA)
│   ├── UML/                         # Proyecto Visual Paradigm y diagramas exportados
│   │   ├── ArandanoIRT.vpp          # Proyecto VP con todos los diagramas
│   │   ├── CasosUso/                # Diagramas de casos de uso (RF1–RF9)
│   │   ├── Clases/                  # Diagrama de clases del dominio
│   │   ├── Secuencia/               # Diagramas de secuencia
│   │   ├── Actividad/               # Diagramas de actividad
│   │   └── Otros/                   # Despliegue y componentes
│   ├── img/                         # Imágenes y figuras del documento
│   └── anexos/                      # Anexos del documento de grado
│
├── Docs Software/                   # Diseño técnico del componente de software
│   ├── Diseño_Tecnico.md            # Especificación de módulos y arquitectura
│   ├── Plan_Trabajo.md              # Planificación del desarrollo
│   └── Prompt_Maestro.md            # Contexto de dominio para asistencia IA
│
├── Docs DevOps/                     # Diseño técnico de infraestructura y DevOps
│   ├── Diseño_Tecnico.md            # Estrategia de despliegue, seguridad e IaC
│   ├── Modelado/                    # Diagramas de arquitectura de infraestructura
│   │   ├── Diagrama Arquitectura.drawio      # Fuente editable (draw.io)
│   │   ├── Diagrama Arquitectura.drawio.png  # Exportación PNG
│   │   ├── Diagrama Arquitectura.drawio.svg  # Exportación SVG
│   │   ├── Diagrama de Arquitectura.md       # Descripción textual
│   │   └── DB_Simple.png            # Modelo simplificado de la base de datos
│   ├── Plan_Trabajo.md              # Planificación de DevOps
│   └── Politica_Auditoria.md        # Política de auditoría con pgaudit
│
├── Manuales/                        # Manuales técnicos y de usuario
│   ├── Manual_Tecnico_Software.md   # Referencia técnica de la aplicación web
│   ├── Manual_Tecnico_Operaciones.md # Guía de operaciones e infraestructura
│   ├── Manual_Usuario_Software.md   # Manual del usuario final
│   ├── Guia_Recuperacion_Desastres.md # Procedimientos de recuperación ante desastres
│   ├── Configuracion_Alertas.md     # Configuración del sistema de alertas
│   ├── Manual_Tecnico_V1.pdf        # Manual técnico compilado (PDF)
│   └── Manual_Usuario_V2.pdf        # Manual de usuario compilado (PDF)
│
├── Docs Backend (Deprecated).zip    # Documentación de versiones anteriores (archivada)
├── Docs Firmware.zip                # Documentación de versiones anteriores del firmware
├── LICENSE                          # Licencia GNU GPLv3
└── README.md                        # Este documento
```

---

## 🚀 Inicio Rápido del Ecosistema Completo

Para levantar el sistema completo en un entorno de desarrollo o producción, sigue el orden de dependencias:

**1. Infraestructura** → Clona [`ArandanoIRTOps`](https://github.com/rfgrona/ArandanoIRTOps) y levanta los servicios base (PostgreSQL, MinIO).

**2. Aplicación web** → Clona [`ArandanoIRTSoftware`](https://github.com/rfgrona/ArandanoIRTSoftware), configura `appsettings.Development.json` y ejecuta `dotnet run`.

**3. Microservicio IA** → Clona [`ArandanoIRT-ML`](https://github.com/rfgrona/ArandanoIRT-ML), vectoriza el corpus con `uv run setup_database.py` y despliega con Docker.

**4. Firmware** → Clona [`ArandanoIRTFirmware`](https://github.com/rfgrona/ArandanoIRTFirmware), carga el LittleFS, configura el dispositivo y ejecuta `pio run --target upload`.

Consulta los READMEs individuales de cada repositorio para las instrucciones detalladas, o los archivos `README_*.md` en este repositorio.

---

## 📄 Licencia

Este proyecto, en todos sus repositorios, está distribuido bajo la **GNU General Public License v3.0**. El código se publica con fines educativos y de reproducibilidad científica. Consulta el archivo [LICENSE](./LICENSE) para más detalles.

---

## 👥 Autores

| Autor | Rol |
|---|---|
| **Juan Esteban Fuentes Rojas** | Investigador — Desarrollo de hardware, validación metrológica y experimentación de campo |
| **Gabriel Esteban Martinez Roldan** | Investigador — Arquitectura de software, DevOps, firmware e integración del sistema |

**Directora:** Mg. Gina Maribel Valenzuela Sabogal

**Institución:** Universidad de Cundinamarca — Facultad de Ingeniería — Programa de Ingeniería de Sistemas y Computación - Extensión Facatativá

**Grupo de Investigación:** GISTFA — Semillero en Software Libre LINUDECX

**Año:** 2026

---

<div align="center">
  <sub><em>Universidad de Cundinamarca · Extensión Facatativá · Ingeniería de Sistemas y Computación · 2026</em></sub><br>
  <sub><em>Grupo de Investigación GISTFA · Semillero LINUDECX</em></sub>
</div>