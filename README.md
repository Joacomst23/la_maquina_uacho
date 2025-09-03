🎯 Objetivo:
Construir una máquina expendedora con un solo producto, un solo motor, y un sistema de pago por Mercado Pago que habilite el dispensado automático tras la confirmación del pago.
________________________________________
🧩 RESUMEN DEL FUNCIONAMIENTO
1.	El cliente escanea un QR dinámico generado por la máquina (o fijo si es más simple).
2.	Paga a través de Mercado Pago.
3.	El backend recibe la confirmación del pago.
4.	El backend le indica al ESP32 (controlador) que active el motor.
5.	El motor gira y dispensa un producto.
________________________________________
🧱 PASO A PASO – CON UN SOLO PRODUCTO Y MOTOR
________________________________________
1. 🧰 MATERIALES NECESARIOS
Componente	Descripción
ESP32	Microcontrolador con WiFi
Motor DC o servo	Para mover una compuerta o mecanismo
Módulo relé	Para controlar el motor DC
Fuente 5V/12V	Para alimentar el motor
Gabinete	MDF, acrílico o impresión 3D
Servidor simple	Backend en Flask o Node.js (para pagos)
Cuenta Mercado Pago	Modo desarrollador con claves API
________________________________________
2. 🏗️ CONSTRUCCIÓN FÍSICA
Diseño básico:
•	Una rampa inclinada donde se colocan los productos (por ejemplo: botellas, snacks).
•	Al final de la rampa, una compuerta o "puerta giratoria" controlada por un motor.
•	Al recibir el pago, el motor abre brevemente la compuerta y deja caer 1 producto.
¿Cómo puede ser la compuerta?
•	Una paleta giratoria con un servo motor (fácil de controlar).
•	Una compuerta deslizante que se abre con motor DC + relé.
________________________________________
3. 🔌 ESQUEMA DE CONEXIÓN DEL SISTEMA
ESP32 + motor DC + relé:
lua
CopiarEditar
+----------------+      +-------------+      +-----------+
|     ESP32      | ---> |   Relé      | ---> | Motor DC  |
+----------------+      +-------------+      +-----------+
         |                    |
         +---------------------> Fuente de 12V
Alimentación:
•	El ESP32 se alimenta por USB o fuente 5V.
•	El motor se alimenta por una fuente externa (12V, por ejemplo).
________________________________________
4. 🧠 LÓGICA DE FUNCIONAMIENTO
ESP32 (en pseudocódigo):
cpp
CopiarEditar
void loop() {
  if (pagoAprobadoEnServidor()) {
    activarMotor(); // gira por 2 segundos, por ejemplo
    resetearEstadoDePago();
  }
  delay(5000); // esperar antes de chequear de nuevo
}
Backend:
•	Genera un QR dinámico de pago.
•	Espera la confirmación de Mercado Pago (webhook).
•	Guarda el estado del pago.
•	Cuando el ESP32 consulta, responde "sí" o "no".
________________________________________
5. 💳 INTEGRACIÓN CON MERCADO PAGO
En el servidor:
1.	Usás el SDK oficial de Mercado Pago para generar el link de pago o QR.
2.	Cuando se aprueba el pago, se activa una variable en el servidor que indica “pago aprobado”.
3.	El ESP32 consulta ese estado y actúa.
Flujo:
•	/crear_pago: genera un pago.
•	/webhook: recibe confirmación de pago.
•	/estado: responde "1" si el pago está aprobado y no usado.
________________________________________
6. 🧪 EJEMPLO DE CÓDIGO
ESP32 (Arduino IDE - básico):
cpp
CopiarEditar
#include <WiFi.h>
#include <HTTPClient.h>

const char* ssid = "TU_WIFI";
const char* password = "CONTRASEÑA";
const char* servidor = "http://tu-backend.com/estado";
const int relePin = 5;

void setup() {
  pinMode(relePin, OUTPUT);
  digitalWrite(relePin, LOW);

  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
  }
}

void loop() {
  if (WiFi.status() == WL_CONNECTED) {
    HTTPClient http;
    http.begin(servidor);
    int httpCode = http.GET();

    if (httpCode == 200) {
      String payload = http.getString();
      if (payload == "despachar") {
        digitalWrite(relePin, HIGH);
        delay(2000); // activa motor 2 segundos
        digitalWrite(relePin, LOW);
      }
    }
    http.end();
  }
  delay(5000);
}
________________________________________
7. ✅ VENTAJAS DE UN SOLO PRODUCTO Y MOTOR
•	Menor complejidad mecánica.
•	Solo necesitás un solo canal de pago.
•	Costos más bajos en electrónica y estructura.
•	Muy útil para vender: botellas, snacks, tickets, souvenirs.

Área	Material / Componente
Estructura	MDF 3mm o acrílico 3mm (láser o caladora)
Producto	Botellas pequeñas, latas, snacks
Motor	Servo MG90S
Electrónica	ESP32 + cables + fuente 5V
Software	Backend con Mercado Pago + control ESP32



📐 DISEÑO SUGERIDO (ESQUEMA)
+-----------------------------+
|       Frente acrílico       |

|                             |
|                             |
|    [ Comp. de producto ]    |
|      (con rampa inclinada) |
|                             |
| -- Comp. electrónica -------|
|  [ESP32] [servo] [fuente]   |
|  [Puerto de salida abajo]   |
+-----------------------------+
        30 cm x 30 cm x 25 cm


        

 <img width="589" height="331" alt="image" src="https://github.com/user-attachments/assets/4e68aabe-a155-45fb-906f-d954628c2a0d" />
<img width="467" height="623" alt="image" src="https://github.com/user-attachments/assets/d3b4ff6f-bc4c-4e85-a91e-cbda78f01a3e" />
<img width="589" height="331" alt="image" src="https://github.com/user-attachments/assets/40b3d973-0f90-43ff-86ec-69cfa6eeebbb" />

https://descubrearduino.com/vending/ - maquina expendedora arduino mega
https://www.instructables.com/search/?q=dispenser&projects=featured – 
https://www.instructables.com/Craft-Your-Own-Desktop-Sized-Cardboard-Vending-Mac/  - 
dispensador arduino card only.
 


