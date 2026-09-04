# 🔬 Take-Home Labs: Plataforma de Control con Arduino Mega (ATmega2560)

![C++](https://img.shields.io/badge/C++-00599C?style=for-the-badge&logo=c%2B%2B&logoColor=white)
![Arduino](https://img.shields.io/badge/Arduino_Mega-00979D?style=for-the-badge&logo=arduino&logoColor=white)
![KiCad](https://img.shields.io/badge/KiCad-FFFFFF?style=for-the-badge&logo=kicad&logoColor=black)
![Control Systems](https://img.shields.io/badge/Control_Systems-8A2BE2?style=for-the-badge)

Este repositorio contiene el desarrollo, documentación y código fuente de una serie de laboratorios experimentales centrados en la arquitectura del microcontrolador ATmega2560. El proyecto se articula en torno a una **Shield personalizada diseñada en KiCad**, la cual integra tres plantas físicas intercambiables orientadas a la validación de estrategias de adquisición de señales, instrumentación y control en lazo cerrado.

## 🎯 Objetivo General

Desarrollar competencias avanzadas en la programación de sistemas embebidos priorizando el control directo del hardware a bajo nivel. El proyecto cierra la brecha entre el diseño electrónico (esquemáticos y PCB) y el desarrollo de firmware, validando iterativamente plantas físicas reales.

## 🛠️ Arquitectura de Hardware (KiCad PCB Shield)

La placa base fue diseñada con un enfoque altamente modular. La distribución de señales PWM, analógicas y digitales se realiza a través de **pin sockets modulares (como el conector principal J2 y los puertos dedicados J10, J13, J15)**, permitiendo acoplar diferentes plantas de manera dinámica. 

El sistema cuenta con una topología de potencia robusta:

*   **Gestión de Energía:** Entrada principal de 15V (vía Barrel Jack o PD Trigger) acondicionada a 12V mediante un regulador ajustable **LM350 (TO-220)**. 
*   **Regulación en Cascada:** Etapas secundarias aisladas a 5V (L7805) y 9V (L7809) provistas de capacitores de desacoplo para garantizar rizado mínimo en las etapas analógicas.
*   **Interfaz Humano-Máquina:** Integración en placa de un Encoder Rotativo con filtrado de hardware anti-rebote (redes RC de 100nF) para navegación de menús.

## 🕹️ Descripción Técnica de las Plantas

### 1. Planta de Enrutamiento y Dinámica (CD4066)

*   **Arquitectura:** Matriz de conmutación de señales analógicas basada en arreglos de interruptores bilaterales **CD4066BE**.
*   **Comportamiento Dinámico:** Integra tres redes RC en cascada (1 MΩ y 1 µF) controladas digitalmente con resistencias de *pulldown* (10k). Permite modificar la función de transferencia del sistema en tiempo real, ajustando la respuesta transitoria según las etapas activadas por el microcontrolador.

### 2. Planta Electromecánica (Motor DC N20)

*   **Actuación:** Modulación de velocidad mediante un MOSFET de canal N (Q1) excitado por señal PWM, con una resistencia de *pulldown* (10k) en la puerta y un diodo de giro libre (Flyback D7) para suprimir picos inductivos del motor a 12V.
*   **Telemetría:** Integración directa de un encoder de cuadratura que proporciona datos de posición y velocidad (conectado al header J10), habilitando la implementación de algoritmos PID en lazo cerrado.

### 3. Planta de Visualización e Instrumentación (LM3914N)

*   **Interfaz Visual:** Vúmetro analógico basado en el driver **LM3914N** alimentado a 9V, que excita una barra de 10 segmentos LED (HDSP-4830) de forma lineal.
*   **Acondicionamiento de Señal:** Para visualizar la salida digital del Arduino, incorpora un filtro pasa-bajas RC (resistencia de 4.7 kΩ y capacitor de 10 µF) que convierte el tren de pulsos PWM en una señal DC estable, garantizando una lectura continua sin parpadeos visuales. El rango de referencia es calibrable mediante un potenciómetro de 5k.

*   # 🔌 Documentación de Hardware: Pinout y Conexiones (Take-Home Labs)

Este documento detalla el mapa de conexiones de la placa principal hacia los diferentes módulos (plantas) y periféricos, diseñado para facilitar el desarrollo del firmware y la programación de control. 

La tarjeta está diseñada como un shield para la arquitectura de Arduino Mega, integrando acondicionamiento de potencia y ruteo específico para cada planta de laboratorio.

## 1. Módulos de Control (Plantas)

### J10 - Planta Motor DC
Conector dedicado para el control de un Motor DC con retroalimentación por encoder.
* **Pin 1:** Señal PWM (Pin 9)
* **Pin 2:** ENCODER_A (Pin 18 / TX1) - *Soporta interrupciones*
* **Pin 3:** ENCODER_B (Pin 19 / RX1) - *Soporta interrupciones*
* **Pin 4:** GND
* **Pin 5:** 5V (Lógica)
* **Pin 6:** 12V (Potencia motor)

### J8 - Planta RC
Interfaz para la planta de respuesta RC (Resistiva-Capacitiva) / Señales análogas y digitales.
* **Pin 1:** A12 (Lectura analógica)
* **Pin 2:** A13 (Lectura analógica)
* **Pin 3:** A14 (Lectura analógica)
* **Pin 4:** A15 (Lectura analógica)
* **Pin 5:** D7 (Digital)
* **Pin 6:** D6 (Digital)
* **Pin 7:** D5 (Digital)
* **Pin 8:** GND
* **Pin 9:** +5V

### J15 - Planta LED
* **Pin 1:** 9V (Potencia)
* **Pin 2:** GND
* **Pin 3:** Señal PWM (Pin 9)
* **Pin 4:** A7 (Lectura analógica)

---

## 2. Periféricos e Interfaz de Usuario

### J9 - Display (I2C)
Conexión directa para pantalla LCD/OLED vía I2C.
* **Pin 1:** SCL (Pin 21)
* **Pin 2:** SDA (Pin 20)
* **Pin 3:** +5V
* **Pin 4:** GND

### SW1 - Rotary Encoder (Interfaz de Menú)
* **Terminal A:** Pin 9 (PWM / Digital)
* **Terminal Común:** GND
* **Terminal B:** Pin 8 (Digital)
*(Nota: Incluye capacitores de filtrado/debounce de 100nF en hardware).*

---

## 3. Puertos de Expansión (Sensores y Actuadores Extra)

* **J12 (ADC):** GND, A10, A11, A12.
* **J13 (PWM):** GND, D6, D5, D4, D3.
* **J14 (PIR / Trigger):** Entrada para sensor PIR, conectado a PD_ y GND.

---

## 4. Sistema de Potencia y Puntos de Prueba (Test Points)
La placa cuenta con un sistema de regulación escalonada alimentada desde un Barrel Jack (15V nominales). Al momento de ensamblar, verificar voltajes en los siguientes Test Points (TP):

* **TP1:** 5V_Arduino (Regulado por L7805 U3)
* **TP2:** 5V General (Regulado por L7805 U5)
* **TP3:** 9V (Regulado por L7809 U6)
* **TP4:** 12V (Regulado por LM350 U1 ajustable)
* **TP5:** GND (Tierra común)

