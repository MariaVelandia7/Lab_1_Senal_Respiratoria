# Monitoreo del Patrón y Frecuencia Respiratoria con Sensor MQ135

Instrumentación Biomédica y Biosensores, Ingeniería Biomédica, UMNG (Semestre VII).

## Integrantes
- María José Peña Velandia - 5600876
- Antonia Garzón Vanegas - 5600843
- Ana Sofia Conde Porras 

## Objetivo general
Evaluar la influencia del habla o verbalización sobre el patrón respiratorio, mediante un sistema de adquisición basado en el sensor de gas MQ135.

## Objetivos específicos
- Reconocer las variables físicas involucradas en el proceso respiratorio.
- Desarrollar un sistema que extraiga el patrón y la frecuencia respiratoria a partir de las variaciones de CO2 exhalado.
- Identificar la verbalización a partir del patrón de frecuencia respiratoria.

## Resumen de la práctica 
Se implementó un sensor MQ135 (sensible a NH3, alcohol, benceno y CO2)  [1] ubicado dentro de una mascarilla de oxígeno, para concentrar el CO2 exhalado cerca del sensor y mejorar la señal. Alimentado a 5V DC, digitalizado con el ADC del Arduino UNO. Visualización en tiempo real con Serial Plotter, captura temporizada, filtrado y análisis espectral (FFT) en MATLAB.

## Estructura del repositorio
- Parte A = Adquisición de la señal: sensor, montaje, código Arduino, capturas
- Parte B = MATLAB:  captura temporizada, filtrado, datos .mat, FFT
- Parte C = Análisis:  análisis, preguntas de discusión, conclusiones

## Conclusión general
En este laboratorio se logró capturar las señales requeridas implementando el sensor de gases MQ135 que fue previamente integrado a una mascarilla, esto permitió adquirir mejor la señal y redujo la influencia de otros factores (ruidos) que se puedan capturar del ambiente. Se registraron las señales en tiempo real correspondientes tanto a respiraciones normales como a periodos de habla, evidenciando diferencias en el comportamiento de la señal según la actividad realizada.
Esta señal fue analizada en el dominio de la frecuencia mediante la transformada rápida de Fourier (FFT) para ver su espectro.

# Parte A — Selección del sensor y sistema de adquisición

## 1. Revisión de literatura - Variables físicas del proceso respiratorio

La respiración es el proceso fisiológico mediante el cual se inhala aire con oxígeno hacia los pulmones y se exhala CO₂ producto del intercambio gaseoso en la membrana alveolocapilar [2]. Para entender la ruta anatómica del ingreso de O2 en el cuerpo debemos hacer un trazado del mismo. El aire ingresa por las cavidades nasales en donde se filtra, se calienta y se humedece, después de pasar por la faringe y la laringe, se dirige a la tráquea que es el "cartílago" que guía el aire al tórax, en donde pasa por los bronquios, bronquiolos y conductos alveolares para finalmente llegar a los alvéolos. 
El intercambio de gases ocurre aquí, el oxígeno del alveolo pasa a la sangre y el CO2 de la sangre pasa al alveolo, esto es por medio de difusión pasiva.

Las principales variables físicas que pueden medirse para caracterizarla son:

- **Movimiento torácico o abdominal** (expansión de la caja torácica durante la inhalación).
- **Temperatura del aire exhalado** (El aire inhalado es mas frío que el exhalado).
- **Flujo y presión de aire** en la vía respiratoria (nariz/boca).
- **Concentración de CO2 en el aire exhalado**, variable seleccionada para esta práctica.

La frecuencia respiratoria (RR) es un signo vital clínicamente relevante: se ha demostrado que sus alteraciones predicen eventos graves como paro cardiorrespiratorio o ingreso a UCI, incluso mejor que el pulso o la presión arterial [3].

## 2. Selección del sensor: MQ135

Se seleccionó el sensor de gas **MQ135**, un sensor semiconductor basado en SnO2 (dióxido de estaño) cuya resistencia disminuye al aumentar la concentración de gases como NH₃, alcohol, benceno y CO2 en el ambiente que lo rodea [4]. Dado que el CO2 es uno de los principales componentes que aumenta su concentración en el aire exhalado respecto al inhalado, el MQ135 permite inferir indirectamente el ciclo respiratorio a partir de las variaciones periódicas de su señal analógica.
- Es un sensor pasivo, de bajo costo y alimentación compatible con Arduino (+5 VDC).
- Ya se había manejado en otros laboratorios por lo que ya estábamos familiarizados con el tema
- No requiere una caracterización previa.
- Su salida analógica es directamente compatible con el ADC de 10 bits del Arduino.

**Adaptación al sujeto de prueba:** para minimizar la interferencia y mejorar la relación señal/ruido, el sensor se ubicó dentro de una mascarilla de oxígeno colocada sobre nariz y boca del sujeto de prueba. Esto concentra el CO₂ exhalado en un volumen reducido y cercano al elemento sensor, en lugar de dispersarse en el ambiente, lo que produce una señal periódica más marcada y sincronizada con cada ciclo de inhalación y exhalación.

## 3. Circuito de acondicionamiento y digitalización

POR REVISAR

- **Alimentación:** +5 VDC (pin 5V del Arduino) al pin VCC del módulo MQ135.
- **Salida analógica (AOUT):** conectada a la entrada analógica A0 del Arduino.
- **Tierra común** entre el módulo MQ135 y el Arduino.
- **Digitalización:** se empleó el ADC de 10 bits integrado en el Arduino (`analogRead()`), obteniendo valores entre 0 y 1023 proporcionales al voltaje de salida del sensor (0–5 V).
- **Precalentamiento:** se dejó estabilizar el sensor antes de la toma de datos (recomendado por el fabricante, 20–30 s), lo cual es relevante porque explica la deriva ascendente observada al inicio de las señales capturadas.

## 4. Código de adquisición (Arduino)

CIDIGO 

## 5. Capturas del Serial Plotter

| Condición | Descripción | Archivo |
|---|---|---|
| Reposo | Sujeto respirando normalmente, sin hablar. Conteo manual: [completar] respiraciones en 60 s | [`capturas/reposo_serial_plotter.png`](./capturas/reposo_serial_plotter.png) |
| Habla/lectura | Sujeto leyendo un texto en voz alta durante la captura | [`capturas/habla_serial_plotter.png`](./capturas/habla_serial_plotter.png) |

# Parte B — Captura temporizada, filtrado y análisis en frecuencia
# Parte C — Análisis, discusión y conclusiones

# Bibliografía
[1] “Tutorial sensores de gas MQ2, MQ3, MQ7 y MQ135,” Naylamp Mechatronics - Perú. https://naylampmechatronics.com/blog/42_tutorial-sensores-de-gas-mq2-mq3-mq7-y-mq135.html 

[2] C. G. Lausted y A. T. Johnson, "Respiratory System," en Biomedical Engineering Fundamentals, J. D. Bronzino, Ed. Boca Raton, FL, USA: CRC Press, 2006. https://doi.org/10.1201/9781420003857

[3] J. F. Fieselmann, M. S. Hendryx, C. M. Helms y D. S. Wakefield, "Respiratory rate predicts cardiopulmonary arrest for internal medicine inpatients," Journal of General Internal Medicine, vol. 8, no. 7, pp. 354–360, Jul. 1993. https://doi.org/10.1007/BF02600071

[4] Components101, "MQ135 Gas Sensor," [Online]. Disponible en: https://components101.com/sensors/mq135-gas-sensor-for-air-quality

