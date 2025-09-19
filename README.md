# Sistema de Aproximación a la Detección Temprana de *Estrés Hídrico* en el Arándano Biloxi mediante Termografía

Este proyecto tiene como objetivo desarrollar un sistema de bajo costo para la detección temprana de estrés hídrico en plantas de arándano, variedad Biloxi, utilizando imágenes termográficas y técnicas de inteligencia artificial. Se busca proporcionar una solución eficiente y asequible para los agricultores, ayudándoles a gestionar de manera más eficiente y responsable los recursos hídricos, lo cual resulta en una cosecha más saludable y productiva.

### Estructura del Proyecto

El código y la documentación de este proyecto están organizados en los siguientes repositorios, cada uno enfocado en un componente clave del sistema:

- **Software (Aplicación Web)**: [ArandanoIRTSoftware](https://github.com/rfgrona/ArandanoIRTSoftware)
  - Contiene el código fuente de la aplicación web principal (desarrollada en .NET 8 MVC), que permite a los usuarios interactuar con el sistema, visualizar datos y ver los resultados del análisis.

- **Inteligencia Artificial (ML)**: [ArandanoIRT-ML](https://github.com/rfgrona/ArandanoIRT-ML)
  - Aloja los modelos de Machine Learning, los notebooks de experimentación y los scripts utilizados para el análisis y procesamiento de las imágenes termográficas.

- **Firmware (Control de Hardware)**: [ArandanoIRTFirmware](https://github.com/rfgrona/ArandanoIRTFirmware)
  - Código desarrollado en PlatformIO para el microcontrolador que gestiona la captura de imágenes termográficas y la comunicación con el hardware del sistema.

- **Infraestructura (Ops)**: [ArandanoIRTOps](https://github.com/rfgrona/ArandanoIRTOps)
  - Contiene la configuración de la infraestructura como código (IaC) utilizando Docker para automatizar el despliegue y la operación de todos los servicios en el servidor.

#### *Para más información, visite la Wiki del repositorio*
---
*Universidad de Cundinamarca - Extensión Facatativá*

*Ingenieria de sistemas y computación - 2025*

*GISTFA - LINUDECX*