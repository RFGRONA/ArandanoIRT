# Diseño Técnico y Justificación Científica del Módulo de Análisis y Alertas de Estrés Hídrico

**Versión:** 1.1 

**Fecha:** 21 de julio de 2025

**Autores:** Juan Esteban Fuentes Rojas, Gabriel Esteban Martínez Roldán

---

## 1. Justificación del Enfoque Termográfico

La elección de la termografía infrarroja (IRT) como tecnología central para el monitoreo del estado hídrico en arándanos no es arbitraria; se fundamenta en principios fisiológicos robustos y en ventajas demostradas sobre otros métodos de monitoreo. Este enfoque permite una evaluación directa y no invasiva de la respuesta de la planta a su entorno hídrico.

### 1.1. Vulnerabilidad Fisiológica del Arándano (`Vaccinium corymbosum L.`) al Estrés Hídrico

El arándano es un cultivo particularmente sensible a las condiciones hídricas debido a sus características anatómicas inherentes. Su sistema radical está compuesto principalmente por raíces finas y fibrosas que **carecen de pelos radicales** (Morales, 2017), las estructuras epidérmicas que en la mayoría de las plantas maximizan la superficie de absorción de agua y nutrientes. Esta ausencia limita drásticamente su capacidad para explorar el perfil del suelo. Se estima que la mayor parte de su masa radicular se concentra en los primeros **40 a 60 cm de profundidad** (Morales, 2017), lo que lo hace dependiente del agua presente en los estratos más superficiales, los cuales se secan con mayor rapidez.

> "El sistema radical del arándano está compuesto principalmente por raíces finas y fibrosas que carecen de pelos radicales [...] Presentan baja capacidad de absorción y no son capaces de atravesar superficies de suelo compactas; por ende, es muy sensible al déficit o al exceso hídrico" (Morales, 2017, p. 20).

Esta configuración radicular superficial lo hace extremadamente vulnerable al estrés hídrico, tanto por déficit como por exceso (Morales, 2017). Investigaciones confirman que incluso déficits menores de agua pueden afectar negativamente el desarrollo de los frutos (Mingeau et al., 2001, citado en Balbontín & Reyes, 2023). Un estudio específico con la variedad Biloxi demostró que limitar el riego al 33% de la demanda diaria **reduce significativamente la biomasa seca de raíces, tallos, hojas y frutos** (Salgado-Vargas et al., 2018). Esta alta sensibilidad justifica la necesidad imperante de un sistema de monitoreo preciso y de detección temprana.

### 1.2. Principio Fisiológico de la Termografía como Indicador de Estrés

La base de la termografía para la detección de estrés hídrico radica en la estrecha relación entre el estado hídrico de la planta, su proceso de transpiración y su temperatura superficial. En condiciones óptimas de riego, las plantas regulan activamente su temperatura mediante la transpiración, un proceso donde la evaporación de agua a través de los estomas de las hojas disipa una cantidad sustancial de energía térmica (García-Tejero et al., 2015). Este mecanismo, conocido como **enfriamiento por evaporación**, permite a la hoja mantenerse en un rango de temperatura ideal para sus funciones fotosintéticas.

> "Durante el proceso de transpiración de las plantas, la salida de agua en forma de vapor, provoca un descenso de la temperatura foliar con respecto a la temperatura del aire, estando dicho fenómeno directamente relacionado con el nivel de transpiración del cultivo, la disponibilidad de agua y el estado hídrico del mismo" (García-Tejero et al., 2015, p. 5).

Sin embargo, cuando la disponibilidad de agua disminuye, la planta activa un mecanismo de defensa para evitar la deshidratación. Fisiológicamente, las plantas responden al déficit de agua cerrando sus estomas para conservar el agua restante (García-Tejero et al., 2015). La disminución de la transpiración interrumpe el proceso de enfriamiento, provocando un **aumento medible en la temperatura de la superficie foliar** (García-Tejero et al., 2015). Este incremento de temperatura es, por tanto, un indicador fisiológico directo y cuantificable del estrés hídrico que experimenta la planta.

> "[...] en condiciones de estrés se produce un cierre estomático parcial que se traduce en un descenso significativo en los niveles de transpiración, disminuyéndose el proceso de enfriamiento por evaporación, y por lo tanto, un incremento en la temperatura de la cubierta vegetal" (García-Tejero et al., 2015, p. 5).

### 1.3. Ventajas sobre Métodos Alternativos

La principal ventaja de la termografía sobre la inspección visual es su capacidad de **detección temprana y pre-sintomática**. Los signos visibles de estrés, como la marchitez o el enrollamiento de las hojas, solo aparecen cuando la planta ya ha sufrido un daño fisiológico significativo que puede impactar irreversiblemente el rendimiento y la calidad del fruto. La termografía, en cambio, detecta el cambio térmico, que es "un indicador temprano y fiable del estrés hídrico, a menudo detectable antes de que los síntomas visuales [...] sean evidentes" (Pineda et al., 2021).

Frente a los sensores de humedad de suelo, la termografía ofrece un diagnóstico más integral. Los sensores de suelo miden una condición del medio, pero no necesariamente el estado real de la planta. La capacidad de una planta para absorber agua puede verse limitada por otros factores como un sistema radicular dañado, compactación del suelo o salinidad (Morales, 2017). La termografía, por el contrario, mide la **respuesta fisiológica integrada** de la planta a todo su entorno. Esto permite a los agricultores pasar de una gestión reactiva a una **gestión proactiva y de precisión**, aplicando el riego justo cuando la planta comienza a necesitarlo y optimizando así el uso del agua.

---

## 2. Índices Termográficos para la Cuantificación del Estrés

Para traducir las mediciones de temperatura en un diagnóstico accionable sobre el estado hídrico de la planta, es necesario el uso de índices estandarizados. Estos índices permiten normalizar los datos brutos de temperatura, contextualizándolos con las condiciones ambientales para obtener una evaluación más fiable y comparable.

### 2.1. Diferencia de Temperatura (ΔT)

El indicador más directo es la diferencia entre la temperatura de la canopia y la temperatura del aire circundante (Vieira & Ferrarezi, 2021). Provee una primera aproximación al estado de la planta, donde valores positivos indican que la canopia está más caliente que el aire, sugiriendo una reducción en el enfriamiento por transpiración.

Su cálculo se define por la siguiente fórmula:

$$ \Delta T = T_c - T_a $$

Donde:
- **$T_c$**: Es la temperatura promedio de la canopia.
- **$T_a$**: Es la temperatura del aire ambiente.

Aunque es un índice útil, su interpretación puede ser ambigua, ya que está fuertemente influenciada por otros factores ambientales como la humedad relativa, la radiación solar y la velocidad del viento (Pineda et al., 2021).

### 2.2. Índice de Estrés Hídrico del Cultivo (CWSI)

El Índice de Estrés Hídrico del Cultivo (CWSI) es uno de los métodos de normalización más comunes y robustos para las mediciones termográficas, ya que supera los efectos de otros parámetros ambientales que afectan la temperatura de la planta (Pineda et al., 2021). El CWSI normaliza la diferencia de temperatura de la canopia en una escala de 0 a 1, donde un valor cercano a 0 indica una planta sin estrés que transpira a su máximo potencial, y un valor cercano a 1 indica una planta bajo estrés severo con los estomas completamente cerrados (Quezada et al., 2020).

La relación empírica para su cálculo es la siguiente (Jones, 1999, citado en Quezada et al., 2020):

$$ CWSI = \frac{(T_c - T_{min})}{(T_{max} - T_{min})} $$

Donde:
- **$T_c$**: Es la temperatura media de la canopia medida en tiempo real.
- **$T_{min}$**: Es la línea base inferior o la temperatura mínima de la canopia, correspondiente a un cultivo sin estrés hídrico (bien regado).
- **$T_{max}$**: Es la línea base superior o la temperatura máxima de la canopia, correspondiente a un cultivo bajo máximo estrés (no transpira).

Este índice ha demostrado tener una correlación significativa con el potencial hídrico xilemático, el indicador "gold standard" del estado hídrico (Alghory & Yazar, 2019, citado en Quezada et al., 2020).

### 2.3. El Rol del Déficit de Presión de Vapor (VPD)

Para interpretar correctamente los datos térmicos y calcular con precisión la línea base inferior del CWSI, es indispensable considerar el Déficit de Presión de Vapor (VPD). El VPD es la diferencia entre la presión de vapor de agua cuando el aire está completamente saturado y la presión de vapor real en el aire a una temperatura dada (García-Tejero et al., 2015). En términos simples, un VPD alto indica que el aire está "seco" y tiene una gran capacidad para absorber más vapor de agua.

El interior de una hoja sana está casi saturado de vapor de agua, por lo que un VPD alto en la atmósfera exterior crea un fuerte gradiente de presión que es "la principal fuerza motriz que 'tira' del agua fuera de la hoja a través de los estomas, acelerando la tasa de transpiración" (García-Tejero et al., 2015). Por lo tanto, el VPD es un componente obligatorio para calcular la línea base inferior del CWSI, permitiendo así distinguir si un aumento de la temperatura foliar se debe al estrés hídrico o simplemente a un aire muy seco y caliente (Quezada et al., 2020).

---

## 3. Diseño Experimental y Metodología de Medición

Para asegurar la validez y robustez de los datos que alimentarán el módulo de análisis, es imperativo establecer un diseño experimental controlado y un protocolo de medición estandarizado. La calidad de la implementación del software depende directamente de la calidad de los datos recopilados en esta fase.

### 3.1. Configuración del Experimento

El experimento debe llevarse a cabo en condiciones controladas, como un invernadero, para minimizar la variabilidad ambiental no controlada (Vieira & Ferrarezi, 2021).

* **Grupos de Tratamiento:** Es fundamental dividir las plantas en al menos dos grupos para permitir la comparación y el cálculo de los índices de estrés:
    * **Grupo Control (n≥2):** Estas plantas se mantendrán en condiciones de riego óptimo, reponiendo el 100% del agua perdida para asegurar que no experimenten estrés hídrico. Este grupo es indispensable, ya que proporcionará la **línea base húmeda ($T_{wet}$ o $T_{min}$)** necesaria para el cálculo del CWSI (Fuchs et al., 1966, citado en Vieira & Ferrarezi, 2021).
    * **Grupo de Estrés (n≥3):** A estas plantas se les aplicará un régimen de riego deficitario controlado (ej. 50% o 25% de la reposición) para inducir un estado de estrés hídrico progresivo. Este grupo proporcionará los datos de plantas bajo estrés y la **línea base seca ($T_{dry}$ o $T_{max}$)**.

* **Estimación del Riego:** Para determinar la cantidad de agua a reponer en el Grupo Control, se recomienda el **método gravimétrico**. Este consiste en pesar las macetas periódicamente para calcular la evapotranspiración real y ha sido validado en estudios de estrés hídrico en arándanos cultivados en maceta (Balbontín & Reyes, 2023).

### 3.2. Protocolo de Adquisición de Datos

Para que los datos sean comparables y fiables, todas las mediciones deben realizarse bajo un protocolo estricto.

* **Frecuencia y Horario:** Las mediciones termográficas deben realizarse sistemáticamente durante el período de máxima demanda atmosférica, que es cuando las diferencias de temperatura entre plantas estresadas y no estresadas son más pronunciadas. Se recomienda programar la captura de datos **entre las 12:00 y las 15:00 horas** (Quezada et al., 2020). Muchos estudios han demostrado que "el mejor momento del día para tomar lecturas de temperatura robustas y fisiológicamente significativas es al mediodía" (Vieira & Ferrarezi, 2021).

* **Condiciones Ambientales:** Las condiciones ambientales influyen directamente en la temperatura de la canopia. Por ello, el protocolo debe seguir estas directrices:
    * **Luz:** Las mediciones deben realizarse exclusivamente en **días despejados o con poca nubosidad** para asegurar una alta radiación solar que active la transpiración (Vieira & Ferrarezi, 2021).
    * **Viento y Lluvia:** Se deben evitar las mediciones en días con viento fuerte o lluvia, ya que el viento aumenta el enfriamiento convectivo y el agua sobre las hojas altera la temperatura superficial, invalidando la medición (Pineda et al., 2021).

* **Procedimiento de Medición:** La forma en que se captura la imagen térmica es fundamental para la calidad del dato.
    * **Distancia y Ángulo:** La cámara debe posicionarse a una **distancia fija y cercana** a la canopia (ej. 60 cm - 1 m) para asegurar una resolución adecuada y minimizar las pérdidas de precisión (Dong et al., 2024; Quezada et al., 2020). Se recomienda un ángulo de aproximadamente **45 grados hacia abajo** para maximizar la visualización del follaje (Dong et al., 2024; Quezada et al., 2020).
    * **Aislamiento de la Canopia:** Para facilitar el procesamiento de la imagen y la separación de los píxeles de las hojas del fondo, se debe utilizar un panel de contraste térmico. Un panel negro colocado detrás de las plantas "permitió separar los datos de las plantas del ruido del entorno que las rodeaba" (Vieira & Ferrarezi, 2021).

---

## 4. Referencias

Balbontín, C., & Reyes, M. (Eds.). (2023). *Uso de inductores hormonales para incrementar la tolerancia a sequía y calidad de frutos en arándano* (Boletín INIA N° 484). Instituto de Investigaciones Agropecuarias, INIA Raihuén.

Dong, Y., Sloan, G., & Chappuies, J. (2024). Open-source time-lapse thermal imaging camera for canopy temperature monitoring. *Smart Agricultural Technology, 7*, 100430. https://doi.org/10.1016/j.atech.2024.100430

García-Tejero, I. F., Perea-Martos, A. J., Navarro-Ramos, M. J., Hernández-Cotán, A., & Durán-Zuazo, V. H. (2015). *La termografía de infrarrojos para la gestión de los recursos hídricos en la agricultura*. SERVIFAPA.

Morales, C. G. (Ed.). (2017). *Manual de manejo agronómico del arándano* (Boletín INIA N° 371). Instituto de Investigaciones Agropecuarias.

Pineda, M., Barón, M., & Pérez-Bueno, M. L. (2021). Thermal imaging for plant stress detection and phenotyping. *Remote Sensing, 13*(1), 68. https://doi.org/10.3390/rs13010068

Quezada, C., Bastias, R., Quintana, R., Arancibia, R., & Solís, A. (2020). Validación del índice de estrés hídrico de cultivo (CWSI) mediante termografía infrarroja y su incidencia en rendimiento y calidad en manzanas 'Royal Gala'. *Chilean Journal of Agricultural & Animal Sciences, 36*(3), 198-207. https://doi.org/10.29393/CHJAAS36-18VICQ50018

Salgado-Vargas, C., Sánchez-García, P., Volke-Haller, V. H., & Colinas-León, M. T. B. (2018). Respuesta agronómica de arándano (Vaccinium corymbosum L.) al estrés osmótico. *Agrociencia, 52*(2), 231-239.

Vieira, G. H. S., & Ferrarezi, R. S. (2021). Use of thermal imaging to assess water status in citrus plants in greenhouses. *Horticulturae, 7*(8), 249. https://doi.org/10.3390/horticulturae7080249

---

## 5. Alcance y Limitaciones de la Investigación

Para una correcta interpretación de los resultados y del sistema propuesto, es fundamental enmarcar este proyecto dentro de su alcance y reconocer sus limitaciones inherentes.

### 5.1. Enfoque de Prueba de Concepto (PoC)

Esta investigación se plantea como una **prueba de concepto** y no como un estudio de validación a gran escala. El uso de un número reducido de plantas (ej. n=2 para control y n=3 para estrés) es adecuado para demostrar la viabilidad técnica del sistema de bajo costo y para calibrar el algoritmo en un entorno controlado. Sin embargo, los resultados y umbrales específicos obtenidos no deben ser extrapolados directamente a condiciones de campo comerciales sin una validación posterior con un mayor número de réplicas biológicas que otorguen mayor robustez estadística.

### 5.2. Limitaciones Metodológicas

Se asumen ciertas simplificaciones metodológicas, justificadas por el enfoque de bajo costo y las limitaciones del hardware, que deben ser consideradas:

* **Segmentación de la Canopia:** Se optará por un método de **segmentación manual asistida** para aislar los píxeles de la canopia, debido a la dificultad de implementar algoritmos automáticos con la resolución de los sensores seleccionados. Si bien se seguirá un protocolo estricto para minimizar la subjetividad, este método introduce una variable humana y limita la escalabilidad del análisis en comparación con técnicas automatizadas.
* **Determinación de la Línea Base Seca ($T_{dry}$):** Para el cálculo del CWSI, se utilizará un enfoque de **"máximo dinámico"**, definiendo $T_{dry}$ como la temperatura más alta registrada en el grupo de estrés. Esta aproximación es práctica y evita la necesidad de instrumentación "gold standard", pero opera bajo la suposición de que al menos una planta ha alcanzado un nivel de estrés máximo en cada medición, lo cual podría no ser siempre el caso.