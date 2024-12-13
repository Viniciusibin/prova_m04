# prova_m04
### Listagem do estado do sistema:
1) Os leds iniciam apagados

2) Quando o sensor LDR detectar que estiver escuro, o protótipo ativa o modo noturno e pisca o led amarelo a cada segundo

3) Quando está claro, segundo a leitura analógica do sensor LDR, o protótipo ativa o modo convencional e faz a temporização alternando entre verde (3 segundos), amarelo (2 segundos) e vermelho (5 segundos)

4) Quando está claro, segundo a leitura analógica do sensor LDR, e o semáforo estiver no estado fechado (somente led vermelho aceso) e o botão for pressionado, o semáforo deve abrir 1 segundo após o pressionamento do botão

5) Quando está claro, segundo a leitura analógica do sensor LDR, e o semáforo estiver no estado fechado (somente led vermelho aceso) e o botão for pressionado 3 vezes, o sistema envia uma requisição HTTP para implementar um alerta

### Código do projeto:
```

#include <WiFi.h>
#include <HTTPClient.h>


#define led_green 2 // Pin used to control the green LED
#define led_red 40 // Pin used to control the red LED
#define led_yellow 9 // Pin used to control the yellow LED
const int buttonPin = 18;  // the number of the pushbutton pin
#define LDR_PIND 4 // LDR port number
#define LDR_PINA 5 // LDR port number
int buttonState = 0;  // variable for reading the pushbutton status

const int ldrPin = 4;  // the number of the pushbutton pin
int threshold=600;

void setup() {

  // Initial configuration of the pins to control the LEDs as OUTPUTs (outputs) of the ESP32

  pinMode(led_green,OUTPUT);
  pinMode(led_red,OUTPUT);
  pinMode(led_yellow,OUTPUT);

  // Initial configuration of LDR
  pinMode(LDR_PIND, INPUT);
  pinMode(LDR_PINA, INPUT);

  // Inicialização das entradas
  pinMode(buttonPin, INPUT); // Initialize the pushbutton pin as an input


  digitalWrite(led_green, LOW);
  digitalWrite(led_red, LOW);
  digitalWrite(led_yellow, LOW);

  Serial.begin(9600); // Configuration for debugging via serial interface between ESP and computer with baud rate of 9600

  WiFi.begin("Wokwi-GUEST", ""); // Connecting to open WiFi network with SSID Wokwi-GUEST

  while (WiFi.status() != WL_CONNECT_FAILED) {
    delay(100);
    Serial.print(".");
  }
  Serial.println("Connected to WiFi successfully!"); // Considering that you exited the loop above, the ESP32 is now connected to WiFi (another option is to place this command inside the if below)

  // Check button status
  buttonState = digitalRead(buttonPin);
  if (buttonState == HIGH) {

    Serial.println("Button pressed!”);
  } else {
    Serial.println("Button not pressed!");
  }

  if(WiFi.status() == WL_CONNECTED){ // If the ESP32 is connected to the Internet
    HTTPClient http;

    String serverPath = "http://www.google.com.br/"; // Endpoint da requisição HTTP

    http.begin(serverPath.c_str());

    int httpResponseCode = http.GET(); // HTTP Request Result Code

    if (httpResponseCode>0) {
      Serial.print("HTTP Response code: ");
      Serial.println(httpResponseCode);
      String payload = http.getString();
      Serial.println(payload);
      }
    else {
      Serial.print("Error code: ");
      Serial.println(httpResponseCode);
      }
      http.end();
    }

  else {
    Serial.println("WiFi Disconnected");
  }
}

void loop() {
  int ldrstatus=analogRead(ldrPin);
  int buttonState = digitalRead(BUTTON_PIN)

  if (buttonState == HIGH && digitalWrite(led_red, HIGH) && ldrstatus<=threshold) {
    if ((millis() - lastDebounceTime) > debounceDelay) {
      countClicks++;
    }
    lastDebounceTime = millis();
    
    delay(1000);
    digitalWrite(led_green, HIGH);
  } else {
    digitalWrite(led_red, HIGH);
  }

  if(ldrstatus<=threshold){
    Serial.print("its dark turn on led");
    Serial.println(ldrstatus);
    // making the yellow LED blink
    digitalWrite(led_yellow, HIGH);
    delay(1000);
    digitalWrite(led_yellow, LOW);

  }else{

    // timing alternating between green (3 seconds), yellow (2 seconds) and red (5 seconds) (up to 2.0 points); 

    Serial.print("its bright turn off light");
    Serial.println(ldrstatus);
    digitalWrite(led_green, HIGH);
    delay(3000);
    digitalWrite(led_green, LOW);
    digitalWrite(led_yellow, HIGH);
    delay(2000);
    digitalWrite(led_yellow, LOW);
    digitalWrite(led_red, HIGH);
    delay(5000);
    digitalWrite(led_red, LOW)

  }


  int valor_digital = digitalRead(LDR_PIND);
  int valor_analogico = analogRead(LDR_PINA);
  Serial.println(valor_digital);
  Serial.println(valor_analogico);
  delay(1000);
}
```

### Arquivo JSON de conexões
```

{
  "version": 1,
  "author": "Vinicius Ibiapina",
  "editor": "wokwi",
  "parts": [
    { "type": "board-esp32-s3-devkitc-1", "id": "esp", "top": 0, "left": 0, "attrs": {} },
    {
      "type": "wokwi-led",
      "id": "led1",
      "top": 118.23,
      "left": 202.09,
      "attrs": { "color": "red" }
    },
    {
      "type": "wokwi-resistor",
      "id": "r1",
      "top": 210.86,
      "left": 132.83,
      "attrs": { "value": "1000" }
    },
    {
      "type": "wokwi-resistor",
      "id": "r2",
      "top": 219.34,
      "left": 205.56,
      "attrs": { "value": "1000" }
    },
    {
      "type": "wokwi-resistor",
      "id": "r3",
      "top": 221.06,
      "left": -129.72,
      "attrs": { "value": "1000" }
    },
    {
      "type": "wokwi-led",
      "id": "led2",
      "top": 186.32,
      "left": 271.67,
      "attrs": { "color": "green" }
    },
    {
      "type": "wokwi-led",
      "id": "led3",
      "top": 121.2,
      "left": -169,
      "attrs": { "color": "yellow" }
    },
    {
      "type": "wokwi-pushbutton",
      "id": "btn1",
      "top": 63.8,
      "left": -182.4,
      "attrs": { "color": "grey" }
    },
    {
      "type": "wokwi-resistor",
      "id": "r4",
      "top": 128.75,
      "left": -259.2,
      "rotate": 270,
      "attrs": { "value": "1000" }
    },
    {
      "type": "wokwi-photoresistor-sensor",
      "id": "ldr1",
      "top": 12.8,
      "left": -440.8,
      "attrs": {}
    }
  ],
  "connections": [
    [ "esp:TX", "$serialMonitor:RX", "", [] ],
    [ "esp:RX", "$serialMonitor:TX", "", [] ],
    [ "led3:C", "r3:1", "black", [ "h-28.32", "v66.1" ] ],
    [ "esp:9", "led3:A", "yellow", [ "h0" ] ],
    [ "r2:1", "esp:GND.4", "black", [ "v0" ] ],
    [ "r2:2", "led2:C", "black", [ "v0" ] ],
    [ "r1:2", "led1:C", "black", [ "v0" ] ],
    [ "led1:A", "esp:40", "red", [ "h48.1", "v-64.05" ] ],
    [ "btn1:2.l", "r4:2", "yellow", [ "h0" ] ],
    [ "r4:1", "esp:GND.1", "black", [ "h-0.85", "v95.45", "h192", "v-28.8" ] ],
    [ "btn1:2.r", "esp:18", "gray", [ "h38.6", "v29" ] ],
    [ "ldr1:GND", "esp:GND.1", "black", [ "h19.2", "v239.6", "h211.2", "v-48" ] ],
    [ "led2:A", "esp:2", "green", [ "v2.08", "h29.73", "v-163.2" ] ],
    [ "btn1:1.r", "esp:RST", "red", [ "v0", "h57.8", "v-28.8" ] ],
    [ "ldr1:DO", "esp:4", "orange", [ "h172.8", "v9" ] ],
    [ "r1:1", "esp:GND.3", "green", [ "v4.29", "h-36.83" ] ],
    [ "r3:2", "esp:GND.1", "green", [ "v3.69", "h70.92" ] ],
    [ "ldr1:VCC", "esp:3V3.1", "red", [ "h268.8" ] ],
    [ "ldr1:AO", "esp:5", "green", [ "h268.8", "v-0.7" ] ]
  ],
  "dependencies": {}
}

```
