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

### Evitar el efecto rebote en un pulsador
El efecto rebote se da cuando se pulsa un botón o pulsador y por culpa de defectos físicos se activa varias veces al pulsarlo o al soltarlo, para evitar esto desde software se puede poner un pequeño retrazo de 10ms luego de detectar o soltar el pulsador:
```c
    // Versión robusta de filtro al efecto rebote
    const int boton = 5;   // Pin del pulsador

    bool estadoBoton = LOW;

    void setup() {
        Serial.begin(115200);
        pinMode(boton, INPUT_PULLDOWN);
    }

    void loop() {
        // Lee el estado del botón
        estadoBoton = digitalRead(boton);

        // Pequeño delay para evitar rebotes
        delay(10);

        // Vuelve a leer para confirmar
        if (estadoBoton == digitalRead(boton)) {
            // Si el botón está presionado
            if (estadoBoton == HIGH) {
                Serial.println("Botón presionado");
            } else {
                Serial.println("Botón no presionado");
            }
        }
    }
```


### Comunicación UART
Usa c`Serial.begin(baudios, configuracion, pin_rx, pin_tx)` para configurar la velocidad y asignar los pines físicos dentro de la GPIO Matrix *(configuracion, pin_rx y pin_tx son opcionales; si no se ponen entonces se conecta con UART desde el cable tipo C)*. Usa purple`Serial` para el USB (Monitor Serie) y purple`Serial1` y purple`Serial2` para mapear a pines:
:::connector
    #### Inicializar puertos UART
        Puedes usar el canal principal para la PC y mapear los otros canales a los pines que prefieras para comunicarte con módulos externos:
        ```c
            const int rx_pin = 16;
            const int tx_pin = 17;

            void setup() {
                // UART0: Comunicación con la PC por USB
                Serial.begin(115200);

                // UART1: Comunicación con otro dispositivo (ej. GPS, Bluetooth, sensor)
                Serial1.begin(9600, SERIAL_8N1, rx_pin, tx_pin); // SERIAL_8N1 significa: 8 bits de datos, sin paridad (None), 1 bit de parada
            }

            void loop() {
                // Código principal
            }    
        ```
    #### Comprobar y leer datos recibidos (byte a byte)
        Usa c`Serial.available()` para saber cuántos bytes han llegado al buffer de recepción y c`Serial.read()` para extraerlos uno por uno y procesarlos:
        ```c
            void setup() {
                Serial.begin(115200);
            }

            void loop() {
                // Verifica si hay 1 o más bytes esperando en el buffer
                if (Serial.available() > 0) {
                    // Lee el primer byte disponible y lo saca de la memoria temporal
                    char dato = Serial.read();
                    
                    Serial.print("Carácter recibido en ESP: ");
                    Serial.println(dato);
                }
            }
        ```
    #### Leer cadenas completas de texto
        Usa c`Serial.readString()` para leer varios bytes juntos hasta que pase el tiempo de espera configurado o c`Serial.readStringUntil(caracter)` para leer hasta que se detecte un carácter específico (como un salto de línea):
        ```c
            void setup() {
                Serial.begin(115200);
                Serial.setTimeout(1000); // Define el tiempo máximo de espera a 1 segundo (1000 ms)
            }

            void loop() {
                if (Serial.available() > 0) {
                    // Lee todo el texto que llega hasta encontrar un salto de línea ('\n')
                    String mensaje = Serial.readStringUntil('\n');
                    
                    Serial.print("Mensaje completo recibido: ");
                    Serial.println(mensaje);
                }
            }
        ```
    #### Enviar datos en formato de texto legible
        Usa c`Serial.print(dato)` para enviar valores convertidos a texto (ASCII) o c`Serial.println(dato)` para enviarlos y terminar con purple`\n`:
        ```c
            int contador = 0;

            void setup() {
                Serial.begin(115200);
            }

            void loop() {
                Serial.print("El valor actual es: ");
                Serial.println(contador); // Envía texto desde el ESP32 a la PC por UART
                
                contador++;
                delay(1000);
            }
        ```
    #### Enviar datos en formato binario crudo
        Usa c`Serial.write(dato)` para enviar un byte al UART o c`Serial.write(buffer, tamaño)` para enviar varios bytes:
        ```c
            const int pin_rx = 4;
            const int pin_tx = 5;

            void setup() {
                Serial2.begin(115200, SERIAL_8N1, pin_rx, pin_tx);
            }

            void loop() {
                byte comando = 0xA5; // Un byte en formato hexadecimal (165 en decimal)
                
                // Envía el byte 165 tal cual, NO envía el texto "165"
                Serial2.write(comando);
                
                // Enviar una trama o arreglo de bytes completos
                byte trama[] = {0x01, 0x02, 0x03, 0x04};
                Serial2.write(trama, 4); // Envía el arreglo de 4 bytes
                
                delay(2000);
            }
        ```
:::
