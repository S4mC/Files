# ESP32-S3

El ESP32-S3 es un microcontrolador que ejecuta un solo programa en bucle dedicado a controlar hardware.
![alt](https://naylampmechatronics.com/img/cms/001206/Pinout%20ESP32-S3-DevKitC-1.jpg)

## La GPIO Matrix
En los microcontroladores tradicionales (como el Arduino Uno), los pines están conectados de forma fija. Por ejemplo, los pines para la comunicación I2C o UART son fijos y no se pueden cambiar. En cambio, en un ESP32-S3 la GPIO Matrix te **permite configurar mediante código que cualquier pin físico sea de entrada o de salida** (con algunas excepciones)(?=excepciones-pines-gpio)

:::float-excepciones-pines-gpio
    Aunque la **GPIO Matrix** del ESP32-S3 permite mapear casi cualquier periférico a cualquier pin digital por software, existen **cuatro excepciones críticas** dictadas por el hardware:

    ### 1. Señales de Alta Velocidad (> 40 MHz)
    Las señales que superan los 40 MHz (como pantallas de alta tasa de refresco o tarjetas SD a 80 MHz) no pueden usar la GPIO Matrix porque esta añade un retraso de un ciclo de reloj.
    * **Restricción**: Usa los pines fijos dedicados del **IO MUX** (pines por defecto como GPIO 11, 12 y 13) para señales de alta velocidad.

    ### 2. Pines de Memoria Interna (Flash y PSRAM)
    El chip utiliza un bus interno fijo para comunicarse con sus memorias integradas de ejecución y almacenamiento.
    * **Restricción**: No uses los pines **GPIO 26 al 32** (usados para la Flash integrada) ni **GPIO 33 al 37** (en placas con memoria Octal PSRAM). Remapearlos interrumpe el flujo de datos del sistema y provoca que el chip crashee.

    ### 3. Pines de Arranque (Strapping Pins)
    Son pines que el procesador lee durante los primeros milisegundos del encendido para determinar el modo de ejecución o voltajes internos.
    * **Restricción**: No uses los pines **GPIO 0, 3, 45 y 46**. No se les deben conectar componentes externos que fuercen un estado eléctrico alto o bajo durante el arranque, ya que impedirán que el ESP32-S3 inicie correctamente.

    ### 4. Periféricos Analógicos y USB Nativo
    La GPIO Matrix maneja exclusivamente señales digitales binarias.
    * **Restricción**: No uses las líneas diferenciales del puerto **USB Nativo (GPIO 19 y 20)** para evitar perder la comunicación del chip. Además, las entradas/salidas analógicas (como el ADC o el DAC) se saltarán la matriz automáticamente al estar configuradas como analógicas.

    ---

    ### Conclusión: Pines seguros de usar en el ESP32-S3
    - GPIO 1, 2, 4 ... 18, 38, 39, 43, 44, 47 y 48
:::

Además de conectar rutas, la GPIO Matrix del ESP32-S3 permite aplicar filtros de ruido, sincronizar reloj o invertir la polaridad de una señal directamente en el hardware, sin consumir ciclos de la CPU.

### Configurar un pin GPIO como entrada o salida digital
Usa c`pinMode(numero_pin, tipo_pin)` para configurar un número de pin con alguno de estos tipos de pin:
:::connector
    #### INPUT
        El pin sirve como entrada y puede leer un valor digital o analógico, pero si el pin está al aire puede saltar entre LOW y HIGH en la lectura digital:
        ```c
            const int pin = 5;

            void setup() {
                Serial.begin(115200);
                pinMode(pin, INPUT);
            }

            void loop() {
                int estado = digitalRead(pin); // Lee un valor de 0 o 1 (0 = LOW, 1 = HIGH)
                Serial.println(estado);

                int estado_a = analogRead(pin); // Lee un valor entre 0 a 4096 (mayor voltaje da mayor valor con límite de 3.3V), porque la resolución en el esp32-s3 es 12 bits

                Serial.println(estado_a);

                delay(500);
            }
        ```
    #### OUTPUT
        El pin sirve como salida y puede generar un valor digital o analógico, pero el valor analógico no es un voltaje fijo sino que es una ilusión que se logra al prender y apagar el pin digital miles de veces por segundo:
        ```c
            const int pin = 5;

            void setup() {
                pinMode(pin, OUTPUT);
            }

            void loop() {
                digitalWrite(pin, HIGH); // Genera un voltaje de 0V o 3.3V (0V = LOW, 3.3V = HIGH)

                analogWrite(pin, valor); // Genera un voltaje entre 0V y 3.3V (mayor valor da mayor voltaje, con valor hasta 255)
            }
        ```
    #### INPUT_PULLDOWN
        El pin sirve como entrada y puede leer **solo un valor digital** (porque analogRead puede fallar). Además activa internamente una resistencia pull-down conectada a GND **(El valor leído es LOW por defecto)**.
        ```c
            const int pin = 5;

            void setup() {
                Serial.begin(115200);
                pinMode(pin, INPUT_PULLDOWN); // LOW por defecto
            }

            void loop() {
                int estado = digitalRead(pin); // Lee un valor de 0 o 1 (0 = LOW, 1 = HIGH)
                Serial.println(estado);

                delay(500);
            }
        ```
    #### INPUT_PULLUP
        El pin sirve como entrada y puede leer **solo un valor digital** (porque analogRead puede fallar). Además activa internamente una resistencia pull-up conectada a 3.3V **(El valor leído es HIGH por defecto)**.
        ```c
            const int pin = 5;

            void setup() {
                Serial.begin(115200);
                pinMode(pin, INPUT_PULLUP); // HIGH por defecto
            }

            void loop() {
                int estado = digitalRead(pin); // Lee un valor de 0 o 1 (0 = LOW, 1 = HIGH)
                Serial.println(estado);

                delay(500);
            }
        ```
:::

