# Manual de Usuario: Sistema Arandano IRT

* **Versión del Documento:** 1.0
* **Fecha:** 2025-10-12

## Capítulo 1: Introducción

### 1.1. ¿Qué es Arandano IRT?

Arandano IRT es un sistema de software diseñado para la detección temprana del estrés hídrico en el cultivo de arándano, específicamente en la variedad Biloxi. Utiliza una técnica no invasiva llamada **termografía infrarroja (IRT)**, la cual mide la temperatura de las plantas para evaluar su estado fisiológico. 

El principio de funcionamiento se basa en que una planta bajo estrés hídrico reduce su transpiración, lo que provoca un aumento en la temperatura de sus hojas.  El sistema captura estas variaciones térmicas mediante hardware de bajo costo y procesa los datos para ayudar a los agricultores a tomar decisiones informadas sobre el manejo del riego.  El objetivo final es optimizar el uso del agua, minimizar pérdidas en el cultivo y promover prácticas agrícolas más sostenibles. 

### 1.2. ¿A quién va dirigido este manual?

Este manual está dirigido a los usuarios finales del sistema Arandano IRT. El perfil principal de usuario incluye:
* **Agricultores de arándano**, especialmente los de pequeña y mediana escala que buscan mejorar la eficiencia en el manejo del agua. 
* **Estudiantes y académicos** de áreas como agronomía, ingeniería de sistemas y ciencias ambientales interesados en la aplicación de tecnología en la agricultura de precisión. 

### 1.3. Conceptos Clave

Para utilizar el sistema de manera efectiva, es importante comprender los siguientes conceptos:

* **Estrés Hídrico:** Es una condición que ocurre cuando una planta no dispone de suficiente agua. En el arándano, debido a su sistema de raíces superficial, esta condición puede comprometer el crecimiento, el rendimiento y la calidad del fruto. 

* **Termografía Infrarroja (IRT):** Es una técnica que permite visualizar la temperatura de una superficie. En este sistema, se utiliza para detectar el aumento de temperatura en las hojas de la planta, que es un indicador temprano y fiable del estrés hídrico, a menudo visible antes de que aparezcan síntomas como la marchitez. 

* **Máscara Térmica:** Es una herramienta digital que se utiliza en el software para definir el área específica de la imagen térmica que corresponde a la planta. Esto permite que el sistema ignore las temperaturas del fondo (suelo, soportes, etc.) y analice únicamente los datos de la vegetación, mejorando la precisión de los cálculos.

* **Grupos Experimentales:** Para realizar análisis comparativos, las plantas en el sistema se pueden organizar en diferentes grupos, siendo los más importantes:
    * **Grupo Control:** Plantas que se mantienen bajo un régimen de riego óptimo y sirven como referencia de un estado saludable.
    * **Grupo Estrés:** Plantas a las que se les induce un déficit hídrico controlado para calibrar y validar las mediciones del sistema.
    * **Grupo Monitoreado:** Plantas del cultivo comercial a las que se les aplica el monitoreo para tomar decisiones de riego.

## Capítulo 2: Primeros Pasos

Este capítulo describe los procedimientos fundamentales para acceder y comenzar a utilizar el sistema Arandano IRT, desde la inicialización del primer administrador hasta el registro de usuarios subsecuentes.

### 2.1. Acceso al Sistema

El acceso al sistema se realiza a través de una página de inicio de sesión. Para ingresar, el usuario debe proporcionar el correo electrónico y la contraseña asociados a su cuenta.


El formulario contiene los siguientes campos:
* **Correo Electrónico:** La dirección de correo electrónico con la que fue registrado en el sistema.
* **Contraseña:** La contraseña personal definida durante el registro.
* **Verificación de seguridad:** Un componente que asegura que el acceso no es automatizado.

Además, se proporcionan enlaces para **Registrarse** (si se posee un código de invitación) y para iniciar el proceso de **recuperación de contraseña**.

### 2.2. Inicialización del Sistema (Primer Administrador)

Este es un procedimiento especial que se realiza **una única vez** cuando el sistema se instala por primera vez y no existen cuentas de administrador. Su objetivo es crear el primer administrador, quien luego podrá gestionar el resto de los usuarios.

El flujo es el siguiente:

1.  **Acceso de Arranque:** Se debe navegar a la URL especial de inicialización (`dominio.com/boostrap/login`). Esta página solo es accesible si no hay administradores en la base de datos. Se presentará el formulario de "Acceso de Arranque".
    2.  **Ingreso de Credenciales Root:** En este formulario, se deben ingresar las credenciales del usuario `root`, las cuales son definidas durante la instalación del sistema (revisar manual técnico).
3.  **Redirección a la Creación de Invitación:** Una vez autenticado, el sistema redirige automáticamente a la vista "Generar Códigos de Invitación". Durante esta sesión, el acceso a cualquier otra parte del sistema está restringido.
    4.  **Envío de Invitación al Primer Administrador:** Se debe completar el formulario con el correo electrónico de la persona que será el primer administrador y seleccionar el **Tipo de Rol** como "Administrador".
5.  **Confirmación y Cierre de Sesión:** Al hacer clic en "Generar y Enviar Invitación", el sistema enviará el correo electrónico y mostrará un mensaje de éxito. Inmediatamente después, la sesión del usuario `root` se cerrará automáticamente.
    
A partir de este punto, el nuevo administrador debe seguir el flujo normal de creación de cuenta que se describe en la siguiente sección.

### 2.3. Creación de una Cuenta (Registro por Invitación)

El acceso para todos los usuarios (incluyendo el primer administrador) se gestiona a través de un sistema de invitaciones para garantizar la seguridad.

1.  **Recepción del Correo:** El usuario invitado recibirá un correo electrónico con un asunto srelacionado. Este correo contiene un código de invitación de un solo uso y un botón para acceder a la página de registro.
    2.  **Acceso a la Página de Registro:** Al hacer clic en el botón "Crear mi Cuenta" del correo, el usuario será dirigido a la página de registro. Alternativamente, puede navegar a la página de registro manualmente y pegar el código.
3.  **Completar el Formulario de Registro:** En esta página, el usuario deberá completar todos los campos requeridos:
    * Código de Invitación
    * Nombre y Apellido
    * Email
    * Contraseña y su confirmación
    4.  **Inicio de Sesión Automático:** Si todos los datos son correctos, la cuenta será creada y el sistema iniciará sesión automáticamente, redirigiendo al usuario al Dashboard principal.

### 2.4. Recuperación de Contraseña

En caso de olvidar la contraseña, el sistema proporciona un mecanismo para recuperarla de forma segura.

1.  Desde la página de **Inicio de Sesión**, se debe hacer clic en el enlace "¿Olvidaste tu contraseña?".
2.  El sistema solicitará que se ingrese la dirección de correo electrónico asociada a la cuenta.
3.  Si el correo existe en la base de datos, se enviará un mensaje con un enlace para el restablecimiento.
4.  El usuario debe hacer clic en dicho enlace, el cual lo dirigirá a una página donde podrá establecer una nueva contraseña.
5.  Una vez confirmada la nueva contraseña, podrá acceder al sistema con sus nuevas credenciales.

## Capítulo 3: El Dashboard - Tu Resumen Diario

El Dashboard es la pantalla principal del sistema y se muestra inmediatamente después de iniciar sesión. Su propósito es ofrecer una vista general y actualizada del estado del monitoreo, permitiendo al usuario evaluar rápidamente las condiciones actuales y las tendencias recientes.

El Dashboard se compone de cuatro secciones principales:
1.  Filtros
2.  Tarjetas de resumen (KPIs)
3.  Gráficos de datos ambientales de las ultimas 24 horas
4.  Tabla de últimas capturas térmicas


### 3.1. Filtros

Por defecto, el Dashboard muestra los datos consolidados de todas las plantas y cultivos. Es posible filtrar esta información para centrarse en un cultivo o una planta específica.


Para aplicar un filtro, se deben seguir los siguientes pasos:
1.  Hacer clic en el botón **Filtros**, ubicado en la esquina superior derecha de la pantalla.
2.  Aparecerá un panel con dos menús desplegables.
3.  **Filtrar por Cultivo:** Permite seleccionar un cultivo específico. Al hacerlo, el Dashboard se recargará para mostrar únicamente los datos de las plantas que pertenecen a ese cultivo.
4.  **Filtrar por Planta:** Este menú se activa después de seleccionar un cultivo. Permite acotar la información a una única planta.
5.  Para volver a la vista general, se puede utilizar el botón **Limpiar**.


### 3.2. Tarjetas de Resumen (KPIs)

La sección superior del Dashboard contiene un conjunto de tarjetas que muestran los indicadores de rendimiento clave (KPIs) más recientes.

* **Temperatura Amb. (Ambiental):** Muestra la última lectura de temperatura del aire registrada por el sensor, junto con los valores máximo, promedio y mínimo de las últimas 24 horas.

* **Humedad Amb. (Ambiental):** Presenta el último valor de humedad relativa del aire, así como los valores máximo, promedio y mínimo de las últimas 24 horas.

* **Luminosidad:** Indica la última medición de intensidad de luz en lux (lx), acompañada de los valores máximo, promedio y mínimo de las últimas 24 horas.

* **Temp. Térmica:** Resume la información de la última captura de imagen térmica realizada.
    * El valor principal es la **temperatura promedio** de la planta en esa captura.
    * También muestra la temperatura del píxel más caliente (Máx. última) y el más frío (Mín. última) de esa misma imagen, junto con la hora en que se tomó.

* **Disp. Activos (Dispositivos):** Ofrece un resumen del estado del sistema y del entorno.
    * Muestra la cantidad de dispositivos que están actualmente activos y enviando datos.
    * Incluye la hora de la última lectura ambiental recibida y datos climáticos externos como la temperatura de la ciudad y la condición meteorológica.


### 3.3. Gráficos de 24 horas

Esta sección presenta tres gráficos de líneas que ilustran la evolución de las variables ambientales durante las últimas 24 horas.

* **Temperatura (°C) - 24h:** Permite visualizar la fluctuación de la temperatura ambiental a lo largo del día y la noche.
* **Humedad (%) - 24h:** Muestra la variación de la humedad relativa en el mismo periodo.
* **Luz (lx) - 24h:** Ilustra los cambios en la intensidad lumínica, permitiendo identificar claramente el ciclo día/noche.

Estos gráficos son útiles para identificar tendencias, picos o comportamientos inusuales en el microclima del cultivo.


### 3.4. Tabla de Últimas Capturas Térmicas

La parte inferior del Dashboard muestra una tabla con un resumen de las capturas térmicas más recientes realizadas por el sistema.

Cada fila corresponde a una medición y contiene la siguiente información:
* **Planta:** El nombre de la planta que fue medida.
* **Fecha:** La fecha y hora exactas en que se realizó la captura.
* **Temp. Máx:** La temperatura máxima registrada en un píxel de esa imagen térmica.
* **Temp. Prom:** La temperatura promedio de la planta en esa imagen.
* **Temp. Mín:** La temperatura mínima registrada en un píxel de esa imagen.

Esta tabla proporciona un vistazo rápido a los datos térmicos más recientes sin necesidad de navegar a otras secciones.

## Capítulo 4: Gestión de Entidades

Para que el sistema funcione correctamente, es necesario registrar y organizar las tres entidades fundamentales que componen el monitoreo: **Cultivos**, **Plantas** y **Dispositivos**. Este capítulo detalla cómo administrar cada una de ellas.

### 4.1. Gestión de Cultivos

Un **Cultivo** es la unidad organizativa principal. Representa una ubicación física, un lote específico o un grupo de plantas que comparten las mismas condiciones y parámetros de análisis.

#### 4.1.1. Vista Principal de Cultivos

Al acceder a la sección "Cultivos" desde el menú de navegación, se presenta una tabla que lista todos los cultivos registrados en el sistema.


Desde esta vista, se pueden realizar las siguientes acciones:
* **Crear Nuevo Cultivo:** Botón ubicado en la esquina superior derecha que inicia el proceso de registro de un nuevo cultivo.
* **Acciones por Cultivo:** Cada fila de la tabla tiene un conjunto de iconos para:
    * **Editar** (icono de lápiz): Permite modificar la información de un cultivo existente.
    * **Detalles** (icono de información): Muestra toda la información del cultivo en modo de solo lectura.
    * **Eliminar** (icono de papelera): Inicia el proceso para eliminar un cultivo.

#### 4.1.2. Crear y Editar un Cultivo

El formulario para crear y editar un cultivo es el mismo. Al crear, el formulario está vacío; al editar, se muestra con la información actual del cultivo.


El formulario se divide en dos secciones:

**Información General:**
* **Nombre del Cultivo:** Un nombre identificativo (ej. "Lote Norte - Siembra 2025").
* **Dirección:** Ubicación física del cultivo.
* **País, Departamento, Ciudad/Municipio:** Selectores para definir la ubicación geográfica precisa.

**Parámetros y Umbrales del Cultivo:**
Esta sección define las reglas que usará el sistema para realizar los análisis automáticos.
* **Análisis (CWSI):**
    * **Umbral de Estrés Incipiente/Crítico:** Valores del índice CWSI (entre 0.0 y 1.0) que determinan cuándo una planta pasa a un estado de estrés leve o severo.
    * **Hora de Inicio/Fin:** Define la ventana de tiempo durante el día en que los datos térmicos son considerados válidos para el análisis (generalmente en las horas de mayor radiación solar).
    * **Umbral de Luz (lux):** Nivel mínimo de luminosidad requerido para que una captura térmica sea procesada.
* **Anomalías Nocturnas:** Parámetros para detectar posibles problemas bióticos (plagas/enfermedades) durante la noche.
* **Calibración:** Frecuencia en meses para recibir recordatorios de mantenimiento de los sensores asociados al cultivo.

#### 4.1.3. Ver Detalles de un Cultivo

Al seleccionar la opción "Detalles", el sistema muestra una vista de solo lectura con toda la información general y los parámetros configurados para ese cultivo.

#### 4.1.4. Eliminar un Cultivo

Al hacer clic en el icono de "Eliminar", el sistema solicita una confirmación final para prevenir borrados accidentales. Es importante destacar que **esta acción es permanente y no se puede deshacer**.

### 4.2. Gestión de Plantas

Las **Plantas** son las entidades individuales que se monitorean dentro de un cultivo. Cada planta debe tener un dispositivo asociado. El proceso de crear, editar, ver detalles y eliminar una planta es muy similar al de los cultivos. A continuación, se destacan las particularidades.

#### 4.2.1. Vista de Detalles de la Planta

La pantalla de "Detalles de la Planta" es el centro de operaciones para una planta individual. Además de mostrar la información básica (nombre, estado, cultivo), proporciona acceso a acciones específicas.


Desde aquí se puede acceder a tres funciones clave:
* **Editar:** Permite cambiar el nombre de la planta, el cultivo al que pertenece o su grupo experimental.
* **Cambiar Estado:** Permite actualizar el estado de salud de la planta y registrar una observación.
* **Máscara Térmica:** Abre la herramienta para definir el contorno de la planta en las imágenes térmicas.

#### 4.2.2. Cambiar el Estado de una Planta

A diferencia de otros campos, el estado de una planta (ej. Óptimo, Estrés Leve) se modifica desde una sección dedicada para mantener un registro histórico de los cambios.

Al hacer clic en "Cambiar Estado", el sistema presenta un formulario donde se selecciona el **Nuevo Estado** y se puede añadir una **Observación** que justifique el cambio. Esta información quedará registrada en el **Historial de Estados**.

#### 4.2.3. Máscara Térmica

La creación de una máscara térmica es un paso fundamental para la precisión de los análisis. Esta herramienta permite "dibujar" sobre una imagen térmica para indicarle al sistema qué píxeles corresponden a la planta y cuáles al fondo.

El uso de esta herramienta se detallará en el **Capítulo 6: Análisis y Reportes**.

### 4.3. Gestión de Dispositivos (Solo Administradores)

Los **Dispositivos** son los sensores físicos (cámaras térmicas, sensores ambientales) que recolectan y envían los datos al sistema. La gestión de estos es una tarea reservada para los usuarios con rol de **Administrador**.

El proceso para listar, editar y eliminar dispositivos sigue el mismo patrón que el de cultivos y plantas. Al crear un nuevo dispositivo, se deben configurar campos importantes para su correcto funcionamiento:

* **ID del Dispositivo:** Un identificador único generado por el sistema. Este ID es **importante**, ya que el hardware debe ser configurado con este valor para que el sistema pueda reconocerlo cuando envíe datos.
* **Código de Activación:** Un código de seguridad de un solo uso que el dispositivo utiliza en su primer contacto con el sistema para autenticarse y quedar vinculado.
* **Planta Asignada:** La planta específica que este dispositivo estará monitoreando.
* **Intervalo de Recolección:** La frecuencia en minutos con la que el dispositivo tomará y enviará las mediciones.

## Capítulo 5: Flujos de Trabajo Esenciales

Este capítulo describe las funcionalidades del día a día para el seguimiento y la consulta de datos históricos, herramientas fundamentales para comprender el comportamiento de las plantas a lo largo del tiempo.

### 5.1. La Bitácora: Registro de Observaciones

La bitácora es un diario digital diseñado para registrar observaciones de campo manuales. Permite complementar los datos automáticos de los sensores con la percepción y el conocimiento del personal en el cultivo.

Al acceder a la sección "Bitácora" desde el menú, se presenta una tabla con el historial de todas las observaciones.

Para registrar una nueva entrada, se deben seguir estos pasos:
1.  Hacer clic en el botón **Registrar Observación**.
2.  En el formulario, seleccionar la **Planta Observada**.
3.  En el campo **Descripción**, detallar los hallazgos (ej. "presencia de hojas amarillentas en la base", "se observa crecimiento de nuevos brotes").
4.  Asignar una **Calificación Subjetiva** (Mala, Regular o Buena) basada en la apreciación visual del estado general de la planta.
5.  Guardar la observación.

**Nota Importante:** Las observaciones son **inmutables**. Una vez guardadas, no se pueden editar ni eliminar. Por este motivo, el sistema solicitará una doble confirmación antes de registrar la entrada de forma permanente.

### 5.2. Historial de Estados de la Planta

Esta sección funciona como un registro de auditoría de la salud de las plantas. Muestra todos los cambios de estado que han ocurrido, ya sea de forma manual por un usuario o de forma automática por el sistema.

La vista se compone de dos partes:

* **Panel de Filtros:** Permite acotar la búsqueda por **Planta** y por un rango de fechas (**Desde** / **Hasta**), facilitando la consulta de la evolución de una planta específica en un periodo determinado.
* **Tabla de Registros:** Muestra la siguiente información:
    * **Fecha de Cambio:** El momento exacto en que ocurrió el cambio de estado.
    * **Planta:** La planta afectada.
    * **Estado:** El nuevo estado de la planta, representado por una insignia de color para una rápida identificación visual.
    * **Origen:** Indica si el cambio fue realizado por un **Usuario** (manual) o por el **Sistema** (automático, debido a un análisis de CWSI o una anomalía).
    * **Observación:** La justificación o nota que se añadió al momento del cambio.

### 5.3. Visualización de Datos Crudos

El sistema permite explorar, filtrar y exportar todos los datos numéricos recolectados por los sensores. Esta funcionalidad se divide en dos secciones: "Datos Ambientales" y "Capturas Térmicas".

#### 5.3.1. Datos Ambientales

Esta vista presenta el historial completo de las mediciones de los sensores ambientales (temperatura, humedad, luz, etc.).

* **Filtros:** Se dispone de un panel de filtros avanzado para buscar datos por **Cultivo**, **Planta**, **Dispositivo** y un rango de fechas. También es posible definir cuántos resultados se muestran por página.
* **Descargar:** El botón "Descargar" permite exportar los datos que coinciden con los filtros aplicados a un archivo en formato **CSV**, útil para análisis externos.
* **Tabla de Datos:** Muestra en detalle cada una de las lecturas de los sensores, incluyendo datos del clima local si estaban disponibles en ese momento.

#### 5.3.2. Capturas Térmicas y RGB

Esta sección contiene el historial de todas las imágenes térmicas y sus análisis estadísticos.

* **Filtros y Descarga:** Al igual que en la vista de datos ambientales, se puede filtrar por **Cultivo**, **Planta**, **Dispositivo** y fechas, y descargar los resultados en formato **CSV**.
* **Tabla de Capturas:** Resume cada captura con su ID, dispositivo, planta, las temperaturas **Máx**, **Prom** y **Mín** calculadas, si tiene una imagen **RGB** asociada, y la fecha de registro.
* **Acciones:** Cada fila tiene un icono de un ojo (👁️) que permite acceder al **Detalle de la Captura**.

Al hacer clic en el icono de detalles, se accede a una vista individual de la captura.

Esta pantalla muestra:
* **Detalles de la Captura:** Metadatos como el dispositivo, planta y hora.
* **Estadísticas de Temp.:** Las temperaturas máxima, promedio y mínima de esa captura específica.
* **Visualización Térmica:** Un mapa de calor (heatmap) interactivo de la imagen térmica. Al pasar el cursor sobre él, se puede ver la temperatura de cada píxel.
* **Imagen RGB:** La fotografía visual de la planta tomada en el mismo momento que la captura térmica, si está disponible.

## Capítulo 6: Análisis y Reportes

El corazón del sistema Arandano IRT es su capacidad para analizar los datos térmicos y convertirlos en información útil para la toma de decisiones. Este capítulo detalla el flujo de trabajo para realizar análisis, desde la preparación inicial hasta la generación de reportes.

### 6.1. Panel Principal de Analíticas

Al acceder a la sección "Analíticas y Reportes" desde el menú de navegación, se presenta el "Monitor de Cultivo y Analíticas". Esta pantalla es el punto de partida para todos los análisis.

Esta vista se organiza por cultivo y muestra dos componentes clave:

* **Requisitos para el Análisis:** Un panel de verificación que indica si un cultivo cumple con las condiciones mínimas para realizar un análisis de estrés hídrico. Para que el análisis sea preciso, se requiere que existan plantas definidas en los grupos 'Control' y 'Monitoreado', y que estas plantas de referencia ya tengan una máscara térmica definida.
* **Listado de Plantas:** Una tabla con todas las plantas del cultivo, mostrando su estado actual y si tienen una máscara térmica. Desde aquí se inician las acciones:
    * **Analizar:** Este botón se habilita para las plantas del grupo "Monitoreado" una vez que el cultivo cumple los requisitos y la planta tiene su propia máscara. Al hacer clic, se accede al panel de análisis detallado.
    * **Máscara:** Permite acceder a la herramienta para crear o editar la máscara térmica de la planta.

### 6.2. Creación de una Máscara Térmica (Requisito Previo)

La **Máscara Térmica** es un requisito fundamental para que los análisis sean precisos. Esta herramienta permite indicarle al sistema qué parte de la imagen térmica corresponde a la planta y qué parte debe ignorar (fondo, suelo, etc.).

El proceso para crear una máscara es el siguiente:

1.  **Visualización de Referencia:** La herramienta muestra dos imágenes una al lado de la otra:
    * **Imagen RGB (Referencia):** Una fotografía normal que sirve como guía visual.
    * **Cuadrícula Térmica Interactiva:** Una representación de la imagen térmica donde cada celda es un píxel de temperatura, coloreado según su valor (azul para frío, rojo para caliente).
2.  **Selección de Modo:** Se puede elegir entre dos modos de trabajo:
    * **Pintar:** Permite seleccionar las celdas que corresponden a la planta.
    * **Borrar:** Permite deseleccionar celdas que se hayan marcado por error.
3.  **Ajuste de la Máscara:** El usuario debe hacer clic y arrastrar el cursor sobre la cuadrícula térmica para "pintar" el área que corresponde a la planta. Las celdas seleccionadas se resaltarán. Los botones "Limpiar" y "Seleccionar Todo" facilitan el ajuste.
4.  **Guardado:** Una vez que el contorno de la planta está correctamente definido, se debe hacer clic en **Guardar Máscara**.

### 6.3. Panel de Análisis Detallado

Al hacer clic en el botón "Analizar" de una planta, se accede a su panel de análisis individual. Esta es la vista principal para interpretar la salud hídrica de la planta a lo largo del tiempo.

Componentes de la vista:
* **Filtro de Fechas:** Permite seleccionar un rango de fechas (**Desde** / **Hasta**) para acotar el análisis a un período específico.
* **Gráfico de Evolución del CWSI:** Es el elemento más importante de esta vista.
    * La **línea azul/turquesa** representa el valor del Índice de Estrés Hídrico (CWSI) de la planta a lo largo del tiempo. Un valor más alto indica mayor estrés.
    * La **línea punteada naranja** representa el "Umbral de Estrés Incipiente" definido en la configuración del cultivo. Cuando el CWSI cruza esta línea, la planta entra en un estado de estrés leve.
    * La **línea punteada roja** representa el "Umbral de Estrés Crítico". Si el CWSI supera esta línea, la planta está en un estado de estrés severo.
* **(Gráfico de Temperaturas):** Debajo del gráfico de CWSI, se encuentra un segundo gráfico que compara la temperatura de la planta con la temperatura ambiental, permitiendo un análisis más profundo.

### 6.4. Generación de Historial de Análisis

Si se accede al panel de análisis de una planta y no se encuentran datos para el período seleccionado, el sistema ofrece una opción para procesar toda la información histórica.

El botón **Generar Historial de Análisis** inicia un proceso en segundo plano que recorre todos los datos crudos de la planta, calcula los valores de CWSI correspondientes y los almacena para su futura consulta. Esta función es útil para poblar el sistema con datos históricos o para recuperar análisis después de una interrupción del servicio.

### 6.5. Generación y Envío de Reportes

Desde el panel de análisis detallado, se pueden exportar los resultados en formato PDF.

* **Descargar PDF:** Al hacer clic en este botón, el sistema genera un documento PDF con un resumen de los datos del período seleccionado, incluyendo tarjetas de resumen y el gráfico de evolución del CWSI. Este archivo se descarga directamente en el computador del usuario.

* **Enviar por Correo:** Esta opción abre un formulario que permite enviar el reporte PDF directamente a una dirección de correo electrónico especificada.

## Capítulo 7: Administración del Sistema (Solo Administradores)

Este capítulo describe las funcionalidades reservadas para los usuarios con el rol de **Administrador**. Estas herramientas permiten gestionar el acceso de otros usuarios y configurar aspectos personales de la cuenta.

### 7.1. Gestión de Usuarios

La sección "Usuarios" es el panel de control central para administrar todas las cuentas del sistema.

Desde esta vista, se puede consultar un listado de todos los usuarios registrados, mostrando su nombre, correo electrónico, rol y fecha de registro. Las acciones principales son:

#### 7.1.1. Invitar a un Nuevo Usuario

Para registrar a una persona en el sistema, es necesario enviarle una invitación.
1.  Hacer clic en el botón **Invitar Usuario** ubicado en la esquina superior derecha.
2.  El sistema redirigirá al formulario para generar códigos de invitación.

El procedimiento detallado para enviar una invitación y el proceso de registro que debe seguir el nuevo usuario se encuentran descritos en el **Capítulo 2, secciones 2.2 y 2.3**.

#### 7.1.2. Acciones sobre Usuarios

Dependiendo del rol del usuario en la lista, se dispondrán de diferentes acciones:

* **Para Usuarios Estándar:**
    * **Ascender a Administrador (icono de escudo):** Esta acción permite otorgar permisos de administrador a un usuario estándar. Por seguridad, el sistema solicitará una confirmación antes de aplicar el cambio.
    * **Eliminar Usuario (icono de papelera):** Inicia el proceso para eliminar permanentemente la cuenta de un usuario estándar.

* **Para Administradores:**
    * **Eliminar Administrador:** La eliminación de un administrador es una acción de alto riesgo. Por seguridad, el sistema inicia un proceso de "eliminación segura" que puede requerir la confirmación de otro administrador o la contraseña del usuario que realiza la acción.
    * No es posible realizar acciones sobre la propia cuenta. El sistema identificará la cuenta del administrador actual con la etiqueta **(Tu Cuenta)** y deshabilitará los botones de acción.

### 7.2. Gestión de Perfil Personal

Cada usuario puede gestionar su propia información de perfil y sus preferencias de notificación. A esta sección se accede desde el menú desplegable en la esquina inferior izquierda de la barra de navegación, seleccionando la opción **Mi Perfil**.

La página se divide en dos áreas principales:

#### 7.2.1. Información del Perfil y Preferencias

Este formulario permite al usuario:
* **Actualizar Información:** Modificar su **Nombre** y **Apellido**.
* **Configurar Preferencias de Notificación:** Activar o desactivar la recepción de correos electrónicos para diferentes eventos del sistema, tales como:
    * Alertas de estrés hídrico.
    * Nuevas solicitudes de ayuda.
    * Fallos en la aplicación o en dispositivos.
    * Alertas por inactividad de dispositivos.

Para aplicar los cambios, se debe hacer clic en el botón **Guardar Cambios**.

#### 7.2.2. Cambiar Contraseña

Esta sección permite al usuario cambiar su contraseña de acceso de forma segura. Se deben completar los tres campos:
* **Contraseña Actual:** Para verificar la identidad del usuario.
* **Nueva Contraseña:** La nueva clave de acceso.
* **Confirmar Nueva Contraseña:** Se debe reingresar la nueva clave para evitar errores.

Al hacer clic en **Cambiar Contraseña**, la nueva clave se aplicará de inmediato.

## Capítulo 8: Ayuda y Soporte

El sistema Arandano IRT cuenta con canales de soporte para resolver dudas o problemas técnicos. El método para solicitar ayuda varía ligeramente dependiendo de si el usuario ha iniciado sesión o no. Una vez enviada la solicitud, le llegará un correo a los administradores para que puedan atenderla y continuar la comunicación por correo electronico.

### 8.1. Solicitar Ayuda (Usuarios Autenticados)

Los usuarios que han iniciado sesión en el sistema pueden solicitar ayuda directamente desde su perfil.

1.  Hacer clic en el panel de usuario, ubicado en la esquina inferior izquierda de la barra de navegación.
2.  En el menú desplegable, seleccionar la opción **Solicitar Ayuda**.
3.  El sistema presentará un formulario simplificado.

Dado que el sistema ya conoce la identidad del usuario, solo es necesario completar dos campos:
* **Asunto:** Un título breve que resuma la consulta.
* **Mensaje:** Una descripción detallada del problema o la duda.

Al hacer clic en **Enviar Solicitud**, se enviará un ticket al equipo de administradores, quienes se pondrán en contacto a través del correo electrónico registrado en la cuenta del usuario.

### 8.2. Solicitar Ayuda (Público / No Autenticado)

Si un usuario no puede acceder a su cuenta o aún no está registrado, puede utilizar el formulario de ayuda público.

1.  Desde la página de **Inicio de Sesión**, hacer clic en el icono de interrogación (?) ubicado en la esquina superior derecha del formulario.
2.  El sistema redirigirá al "Formulario de Ayuda" público.


En este caso, como el sistema no conoce la identidad del visitante, se deben proporcionar los siguientes datos:
* **Tu Nombre:** Nombre completo de la persona que solicita ayuda.
* **Correo de Contacto:** La dirección de correo electrónico a la cual se enviará la respuesta.
* **Asunto:** El motivo de la consulta.
* **Mensaje:** La descripción detallada del problema.

Tras completar el formulario y la verificación de seguridad, se debe hacer clic en **Enviar Solicitud**. El equipo de soporte recibirá el mensaje y responderá al correo de contacto proporcionado.