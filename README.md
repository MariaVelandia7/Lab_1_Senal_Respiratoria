# Monitoreo del Patrón y Frecuencia Respiratoria con Sensor MQ135

Instrumentación Biomédica y Biosensores, Ingeniería Biomédica, UMNG (Semestre VII).

## Integrantes
- María José Peña Velandia - 5600876
- Antonia Garzón Vanegas - 5600843
- Ana Sofia Conde Porras - 5600770

## Objetivo general
Evaluar la influencia del habla o verbalización sobre el patrón respiratorio, mediante un sistema de adquisición basado en el sensor de gas MQ135.

## Objetivos específicos
- Reconocer las variables físicas involucradas en el proceso respiratorio.
- Desarrollar un sistema que extraiga el patrón y la frecuencia respiratoria a partir de las variaciones de CO₂ exhalado.
- Identificar la verbalización a partir del patrón de frecuencia respiratoria.

## Resumen de la práctica 
Se implementó un sensor MQ135 (sensible a NH3, alcohol, benceno y CO₂)  [1] ubicado dentro de una mascarilla de oxígeno, para concentrar el CO₂ exhalado cerca del sensor y mejorar la señal. Alimentado a 5V DC, digitalizado con el ADC del Arduino UNO. Visualización en tiempo real con Serial Plotter, captura temporizada, filtrado y análisis espectral (FFT) en MATLAB.

## Estructura del repositorio
- Parte A = Adquisición de la señal: sensor, montaje, código Arduino, capturas
- Parte B = MATLAB:  captura temporizada, filtrado, datos .mat, FFT
- Parte C = Análisis:  análisis, preguntas de discusión, conclusiones

## Conclusión general
En este laboratorio se logró capturar las señales requeridas implementando el sensor de gases MQ135 que fue previamente integrado a una mascarilla, esto permitió adquirir mejor la señal y redujo la influencia de otros factores (ruidos) que se puedan capturar del ambiente. Se registraron las señales en tiempo real correspondientes tanto a respiraciones normales como a periodos de habla, evidenciando diferencias en el comportamiento de la señal según la actividad realizada.
Esta señal fue analizada en el dominio de la frecuencia mediante la transformada rápida de Fourier (FFT) para ver su espectro.

# Parte A — Selección del sensor y sistema de adquisición

## 1. Revisión de literatura - Variables físicas del proceso respiratorio

La respiración es el proceso fisiológico mediante el cual se inhala aire con oxígeno hacia los pulmones y se exhala CO₂ producto del intercambio gaseoso en la membrana alveolocapilar [2]. Para entender la ruta anatómica del ingreso de CO₂ en el cuerpo debemos hacer un trazado del mismo. El aire ingresa por las cavidades nasales en donde se filtra, se calienta y se humedece, después de pasar por la faringe y la laringe, se dirige a la tráquea que es el "cartílago" que guía el aire al tórax, en donde pasa por los bronquios, bronquiolos y conductos alveolares para finalmente llegar a los alvéolos. 
El intercambio de gases ocurre aquí, el oxígeno del alveolo pasa a la sangre y el CO₂ de la sangre pasa al alveolo, esto es por medio de difusión pasiva.

Las principales variables físicas que pueden medirse para caracterizarla son:

- **Movimiento torácico o abdominal** (expansión de la caja torácica durante la inhalación).
- **Temperatura del aire exhalado** (El aire inhalado es mas frío que el exhalado).
- **Flujo y presión de aire** en la vía respiratoria (nariz/boca).
- **Concentración de CO2 en el aire exhalado**, la cual fue la variable seleccionada para nuestro registro.

La frecuencia respiratoria (RR) es un signo vital clínicamente relevante, se ha demostrado que sus alteraciones predicen eventos graves como paro cardiorrespiratorio o ingreso a UCI, incluso mejor que el pulso o la presión arterial [3].

## 2. Selección del sensor: MQ135

Se seleccionó el sensor de gas MQ135, un sensor semiconductor basado en SnO₂ (dióxido de estaño) cuya resistencia disminuye al aumentar la concentración de gases como NH₃, alcohol, benceno y CO2 en el ambiente que lo rodea [4]. Dado que el CO₂ es uno de los principales componentes que aumenta su concentración en el aire exhalado respecto al inhalado, el MQ135 permite inferir indirectamente el ciclo respiratorio a partir de las variaciones periódicas de su señal analógica.
- Es un sensor pasivo, de bajo costo y alimentación compatible con Arduino (+5 VDC).
- Ya se había manejado en otros laboratorios por lo que ya estábamos familiarizados con el tema
- No requiere una caracterización previa.
- Su salida analógica es directamente compatible con el ADC de 10 bits del Arduino.

**Adaptación al sujeto de prueba:** para minimizar la interferencia y mejorar la relación señal/ruido, el sensor se ubicó dentro de una mascarilla de oxígeno colocada sobre nariz y boca del sujeto de prueba. Esto concentra el CO₂ exhalado en un volumen reducido y cercano al elemento sensor, en lugar de dispersarse en el ambiente, lo que produce una señal periódica más marcada y sincronizada con cada ciclo de inhalación y exhalación.

## 3. Circuito de acondicionamiento y digitalización


- **Alimentación:** +5 VDC (pin 5V del Arduino) al pin VCC del módulo MQ135.
- **Salida analógica (AOUT):** conectada a la entrada analógica A0 del Arduino.
- **Tierra común** entre el módulo MQ135 y el Arduino.
- **Digitalización:** se empleó el ADC de 10 bits integrado en el Arduino (`analogRead()`), obteniendo valores entre 0 y 1023 proporcionales al voltaje de salida del sensor (0–5 V).
- **Precalentamiento:** se dejó estabilizar el sensor antes de la toma de datos (recomendado por el fabricante, 20–30 s), lo cual es relevante porque explica la deriva ascendente observada al inicio de las señales capturadas.

## 4. Capturas del Serial Plotter

| Condición | Descripción | Gráfica | Conteo y FFT |
|---|---|---|---|
| Reposo | Paciente respirando normalmente, sin hablar. Conteo manual: 12 respiraciones en 60 s | <img width="764" height="482" alt="reposo" src="https://github.com/user-attachments/assets/c18c7347-1366-458a-bbbf-637506b58dcb" />|<img width="750" height="450" alt="reposofft" src="https://github.com/user-attachments/assets/d871d371-4c7b-4e38-94c8-75b44f776e3d" />
| Habla/lectura | Paciente leyendo un texto en voz alta durante la captura, 9 respiraciones en 60 s | <img width="760" height="504" alt="habla" src="https://github.com/user-attachments/assets/7d029b3c-2f33-4628-a1bc-eda9f1f11648" />|<img width="763" height="479" alt="hablafft" src="https://github.com/user-attachments/assets/9a3b06f2-2090-4160-9634-83757917075c" />


# Parte B — Explicación código de MATLAB y ARDUINO UNO
## 1. Configuración de la comunicación serial
``` puerto = "COM3"; ```
``` baudrate = 9600;``` 

s = serialport(puerto, baudrate);
configureTerminator(s,"LF");
Explicación

Se establece la comunicación serial entre MATLAB y el Arduino Uno mediante el puerto USB. El Arduino envía continuamente las lecturas del sensor MQ-135 a una velocidad de 9600 baudios.

## 2. Configuración del tiempo de adquisición
respuesta = inputdlg(...)

```  T = str2double(respuesta{1});``` 

``` Fs = 25;```
```N = Fs*T;```

Explicación

El usuario define el tiempo durante el cual se adquirirá la señal respiratoria.

La frecuencia de muestreo es de 25 Hz, lo que significa que se registran 25 muestras por segundo.

El número total de muestras se calcula mediante:

N=Fs×T
## 3. Adquisición de la señal
```for k=1:N```

    dato = str2double(readline(s));

    senal(k)=dato;

Explicación

MATLAB recibe los datos enviados por el Arduino a través del puerto serial y los almacena en un vector llamado senal.

Mientras se reciben los datos, la señal también se muestra en tiempo real.

## 4. Almacenamiento de los datos
save('senal_respiratoria.mat',...)

writetable(...)
Explicación

La señal adquirida se guarda en dos formatos:

MAT, para reutilizar los datos en MATLAB.
CSV, para analizarlos en Excel u otros programas.
## 5. Filtrado de la señal
```senal_f = movmean(senal,5);```
Explicación

Se aplica un filtro de media móvil para reducir el ruido presente en la señal respiratoria y obtener una forma de onda más suave, facilitando el análisis posterior.

## 6. Detección de respiraciones
```[picos,locs] = findpeaks(...)```
Explicación

Se identifican automáticamente los máximos de la señal filtrada.

Cada pico corresponde a una respiración realizada por el usuario.

## 7. Frecuencia respiratoria
```frecuencia_resp = respiraciones/T*60;```
Explicación

La frecuencia respiratoria se calcula contando el número de respiraciones detectadas durante el tiempo de adquisición y convirtiéndolas a respiraciones por minuto (rpm).

La ecuación utilizada es:

```FR=Tiempo/Respiraciones ×60```

## 8. Análisis espectral (FFT)
Y = fft(senal_fft);
Explicación

Se aplica la Transformada Rápida de Fourier (FFT) para transformar la señal del dominio del tiempo al dominio de la frecuencia.

Este análisis permite identificar la frecuencia respiratoria dominante.

## 9. Visualización de resultados
subplot(3,1,1)
subplot(3,1,2)
subplot(3,1,3)
Explicación

El programa genera tres gráficas:

Señal original: muestra los datos obtenidos directamente del sensor.
Señal filtrada: muestra la señal suavizada y las respiraciones detectadas.
Espectro FFT: presenta el contenido frecuencial de la señal y la frecuencia dominante.
10. Cierre de la comunicación
clear s
Explicación

Finalmente, se cierra la comunicación serial con el Arduino para liberar el puerto COM y permitir futuras adquisiciones sin conflictos.

# Parte C — Análisis, discusión y conclusiones

## Análisis 1 — Semejanzas y diferencias reposo vs. verbalización
Gracias al uso de la mascarilla se mejoró la acumulación del aire exhalado, permitiendo identificar el cambio en el comportamiento periódico de la respiración por medio del sensor de CO2. 

Durante el reposo se evidenció un patrón estable, con ciclos regulares de incremento y disminución de concentración de CO2 exhalado, y separación uniforme entre los picos. Por medio de la función 'findpeaks' se encontró una frecuencia respiratoria de 12 por minuto, lo cual es totalmente compatible con la fisiología normal de un adulto, estando en el rango de 12-20 rpm.

Durante el habla se observó una disminución en la cantidad de picos, pues solo alcanzó 9 respiraciones por minuto. En este caso la señal mostró menor periodicidad, con variaciones en las amplitudes y los tiempos entre respiraciones. Esto se debe a que durante la verbalización se prolonga la espiración para permitir la fonación, y las imspiraciones suceden rapidamente entre frases. Como resultado, los ciclos respiratorios son más demorados y se disminuye el número de respiraciones por minuto. 

El análisis en frencuencia también ayudo a evidenciar la diferencia entre ambos casos, en reposo la mayor parte de la energía se concentró en una única frecuencia dominante, mientras que al hablar se presentaron varios picos de menor amplitud y distribuidos en un mayor rango de frecuencias. Además se comparó el valor de picos contados, con el cálculo de la mayor frecuencia multiplicada por 60. 

En conjunto, el análisis temporal y frecuencial demostraron que durante el habla se reduce la cantidad de respiraciones por minuto, pues cada espiración es más lenta y el patrón es más irregular dependiendo de la cadencia y las pausas entre frases.

## Análisis 2 — Alcance y limitaciones para detectar patologías
Alcance:
- Permite estimar la frecuencia respiratoria de forma no invasiva y ver cualitativamente el patrón (regular/irregular) a partir de las variaciones en concentración de CO2.
- La mascarilla ayuda a concentrar el gas exhalado, mejorando la amplitud de la señal y reducir la influencia del ambiente.

Limitaciones:
- Tiempo de respuesta lento y proceso de precalentamiento → deriva de línea base, limita resolución temporal de eventos rápidos.
- No selectivo: responde a varios gases, o a artefactos ambientales como la humedad, temperatura, etc.
- Sensible a fugas de aire en el sello mascarilla-rostro.
- No mide variables mecánicas (volumen/presión) → no permite diagnosticar patologías obstructivas/restrictivas específicas; solo detecta alteraciones generales de frecuencia/regularidad, útil como indicador general, no como herramienta diagnóstica clínica.

## Preguntas para la discusión

**P1: ¿Son los patrones y frecuencias respiratorias iguales o diferentes en cada caso? ¿A qué se debe?**
Los patrones no son iguales. En reposo el patrón es regular y con mayor número de rpm; en el habla se vuelve irregular y la frecuencia tiende a disminuir. Esto se debe a que al hablar la respiración deja de depender solo de los centros automáticos del tallo cerebral y se coordina con la producción de voz: inhalaciones rápidas y exhalaciones prolongadas/controladas para sostener la fonación. En reposo fueron 12 rpm, mientras que al hablar se redujo a 9 rpm. 

**P2: ¿Ventajas y desventajas de usar múltiples sensores? ¿Razones?**
Ventajas: información complementaria sobre otros factores del proceso respiratorio, como la concentración de CO2, el movimiento torácico o la temperatura del aire exhalado. Esta integración incrementa la confiabilidad de las mediciones, reduciendo la posibilidad de hacer detecciones erroneas que puedan llevar a un diagnostico poco preciso.

Desventajas: mayor complejidad de acondicionamiento y sincronización, ya que requiere la convergencia de diferentes señales. Además al necesitan mayor procesamiento de datos se aumenta el costo y consumo energético. Por esto, la selección del número y los tipos de sensores depende del nivel de precisión requerido y la aplicación clínica. 

## Conclusiones
- La concentración de CO₂ exhalado resultó ser una variable adecuada para diferenciar la respiración en reposo de la respiración durante la verbalización, evidenciando cambios tanto en el dominio del tiempo como en el dominio de la frecuencia.
- Se implementó adecuadamente un sistema de adquisición de la señal respiratoria utilizando el sensor MQ135, un Arduino Uno y MATLAB, permitiendo registrar el patrón respiratorio y calcular la frecuencia respiratoria de forma no invasiva.
- El procesamiento digital mediante filtrado, detección de picos y análisis espectral con Fourier permitió  caracterizar el comportamiento de la señal y btener una estimación confiable de la frecuencia respiratoria.

# Bibliografía
[1] “Tutorial sensores de gas MQ2, MQ3, MQ7 y MQ135,” Naylamp Mechatronics - Perú. https://naylampmechatronics.com/blog/42_tutorial-sensores-de-gas-mq2-mq3-mq7-y-mq135.html 

[2] C. G. Lausted y A. T. Johnson, "Respiratory System," en Biomedical Engineering Fundamentals, J. D. Bronzino, Ed. Boca Raton, FL, USA: CRC Press, 2006. https://doi.org/10.1201/9781420003857

[3] J. F. Fieselmann, M. S. Hendryx, C. M. Helms y D. S. Wakefield, "Respiratory rate predicts cardiopulmonary arrest for internal medicine inpatients," Journal of General Internal Medicine, vol. 8, no. 7, pp. 354–360, Jul. 1993. https://doi.org/10.1007/BF02600071

[4] Components101, "MQ135 Gas Sensor," [Online]. Disponible en: https://components101.com/sensors/mq135-gas-sensor-for-air-quality

