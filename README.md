Integrantes del grupo: Marek Joaquin, Quiroga Roman, Sagania ignacio, Priscoglia Tiziana, Gonzalez Camila.


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
## 🚀 Ejemplo básico ESP32 (Arduino IDE)

```cpp
#include <WiFi.h>
#include <HTTPClient.h>

// 🔧 Configuración de WiFi
const char* ssid = "TU_WIFI";        // 👉 Reemplazar con tu SSID
const char* password = "CONTRASEÑA"; // 👉 Reemplazar con tu contraseña

// 🌐 Dirección de tu backend
const char* servidor = "http://tu-backend.com/estado";

// ⚡ Pin del relé (cambia si usás otro pin en el ESP32)
const int relePin = 5;

void setup() {
  // Configuración del pin
  pinMode(relePin, OUTPUT);
  digitalWrite(relePin, LOW);

  // Conexión a WiFi
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
  }
}

void loop() {
  if (WiFi.status() == WL_CONNECTED) {
    HTTPClient http;

    // Conexión al servidor
    http.begin(servidor);
    int httpCode = http.GET();

    if (httpCode == 200) {  // Si la respuesta es OK
      String payload = http.getString();

      // ⚙️ Verifica si el backend indica "despachar"
      if (payload == "despachar") {
        digitalWrite(relePin, HIGH);   // Activa el relé
        delay(2000);                   // Mantiene encendido 2 segundos
        digitalWrite(relePin, LOW);    // Lo apaga
      }
    }

    http.end(); // Finaliza la conexión
  }

  delay(5000); // Espera 5 segundos antes de consultar otra vez
}
```
__________________________
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


 <img width="589" height="331" alt="image" src="https://github.com/user-attachments/assets/4e68aabe-a155-45fb-906f-d954628c2a0d" />
<img width="467" height="623" alt="image" src="https://github.com/user-attachments/assets/d3b4ff6f-bc4c-4e85-a91e-cbda78f01a3e" />
<img width="589" height="331" alt="image" src="https://github.com/user-attachments/assets/40b3d973-0f90-43ff-86ec-69cfa6eeebbb" />

https://descubrearduino.com/vending/ - maquina expendedora arduino mega
https://www.instructables.com/search/?q=dispenser&projects=featured – 
https://www.instructables.com/Craft-Your-Own-Desktop-Sized-Cardboard-Vending-Mac/  - 
dispensador arduino card only.
 
🛠️ 1. MECÁNICA (el cuerpo de la máquina)

Hacé una cajita de cartón, MDF o acrílico de unos 20x20 cm (suficiente para caramelos).

Dentro, poné una rampa inclinada con los caramelos.

Al final de la rampa, una compuerta con un servo (ej: SG90 o MG90S).

En reposo → compuerta cerrada.

Cuando se aprueba un pago → el servo gira unos 60° → deja caer 1 caramelo → vuelve a su posición.

👉 Para caramelos alcanza un servo barato (SG90), no hace falta motor DC + relé.

⚡ 2. ELECTRÓNICA (cómo conectar todo)

ESP32 (se conecta por WiFi).

Servo SG90/MG90S:

Cable naranja → pin GPIO (ej: D5 = GPIO5).

Cable rojo → 5V.

Cable marrón/negro → GND.

Fuente: el ESP32 se alimenta por USB (5V). El servo puede alimentarse del mismo 5V, pero si ves que se reinicia, poné una fuente externa de 5V 2A compartiendo GND.

👉 IMPORTANTE: unir la tierra (GND) de ESP32 y la fuente externa si usás otra fuente.

🌐 3. SOFTWARE BACKEND (servidor con Mercado Pago)

El backend hace 3 cosas:

Genera un QR/link de pago con Mercado Pago SDK.

Recibe webhook de Mercado Pago cuando alguien paga.

Expone un endpoint /estado para que el ESP32 consulte si hay pago aprobado.

Ejemplo (simplificado) en Flask (Python):

from flask import Flask, request

app = Flask(__name__)

pago_pendiente = False

@app.route("/webhook", methods=["POST"])
def webhook():
    global pago_pendiente
    data = request.get_json()
    if data and data.get("status") == "approved":
        pago_pendiente = True
    return "ok", 200

@app.route("/estado", methods=["GET"])
def estado():
    global pago_pendiente
    if pago_pendiente:
        pago_pendiente = False
        return "despachar", 200
    return "no", 200

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)


👉 Cuando Mercado Pago aprueba el pago → pago_pendiente = True.
👉 El ESP32 consulta /estado → si recibe "despachar" abre el servo y cae un caramelo.

📟 4. ESP32 (código Arduino)

Este código conecta al WiFi, consulta el backend cada 5 segundos y mueve el servo si hay pago aprobado.

#include <WiFi.h>
#include <HTTPClient.h>
#include <Servo.h>

const char* ssid = "TU_WIFI";
const char* password = "TU_PASS";
const char* servidor = "http://TU_IP:5000/estado";

Servo servo;
const int servoPin = 5;

void setup() {
  Serial.begin(115200);
  servo.attach(servoPin);
  servo.write(0); // posición inicial: compuerta cerrada

  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("WiFi conectado!");
}

void loop() {
  if (WiFi.status() == WL_CONNECTED) {
    HTTPClient http;
    http.begin(servidor);
    int httpCode = http.GET();

    if (httpCode == 200) {
      String payload = http.getString();
      if (payload == "despachar") {
        Serial.println("Pago aprobado -> abriendo compuerta");
        servo.write(60);  // abre
        delay(1500);      // espera 1.5s
        servo.write(0);   // cierra
        delay(500);
      }
    }
    http.end();
  }
  delay(5000); // consulta cada 5s
}

🚀 RESUMEN DEL FLUJO COMPLETO

Cliente escanea el QR → paga en Mercado Pago.

Mercado Pago manda un webhook a tu backend → el backend guarda que hay un pago aprobado.

El ESP32 hace GET a /estado cada 5 segundos.

Si recibe "despachar" → mueve el servo → cae un caramelo → vuelve a cerrarse.

El backend marca el pago como usado → no se repite.

👉 Así ya tenés todo el sistema funcionando:

Carcasa simple en cartón o MDF.

Servo controlado por ESP32.

Backend Flask con Mercado Pago.

ESP32 con WiFi consultando al backend.

⚙️ 1. Código del Backend (Flask en Python)

Este corre en una PC o servidor (puede ser tu compu, una Raspberry o la nube).
Sirve de “puente” entre Mercado Pago y el ESP32.

from flask import Flask, request

app = Flask(__name__)

pago_pendiente = False   # variable que guarda si hay un pago aprobado

@app.route("/webhook", methods=["POST"])
def webhook():
    global pago_pendiente
    data = request.get_json()
    if data and data.get("status") == "approved":
        pago_pendiente = True   # marca que hay un pago para despachar
    return "ok", 200

@app.route("/estado", methods=["GET"])
def estado():
    global pago_pendiente
    if pago_pendiente:
        pago_pendiente = False   # se usa una sola vez, luego se apaga
        return "despachar", 200  # respuesta que espera el ESP32
    return "no", 200             # no hay pago pendiente

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)

Explicación línea por línea:

pago_pendiente = False → empieza sin pagos aprobados.

/webhook → recibe la notificación de Mercado Pago. Si el status es "approved", guarda pago_pendiente = True.

/estado → lo consulta el ESP32.

Si hay un pago pendiente → responde "despachar" y lo resetea a False (para que no se repita).

Si no hay nada → responde "no".

👉 En resumen: este código guarda si alguien pagó y le avisa al ESP32 cuándo tiene que soltar un caramelo.

📟 2. Código del ESP32 (Arduino IDE)

Este corre en la placa ESP32 y es el que controla el servo que libera el caramelo.

#include <WiFi.h>
#include <HTTPClient.h>
#include <Servo.h>

const char* ssid = "TU_WIFI";
const char* password = "TU_PASS";
const char* servidor = "http://TU_IP:5000/estado";

Servo servo;
const int servoPin = 5;

void setup() {
  Serial.begin(115200);
  servo.attach(servoPin);
  servo.write(0); // posición inicial: compuerta cerrada

  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("WiFi conectado!");
}

void loop() {
  if (WiFi.status() == WL_CONNECTED) {
    HTTPClient http;
    http.begin(servidor);
    int httpCode = http.GET();

    if (httpCode == 200) {
      String payload = http.getString();
      if (payload == "despachar") {
        Serial.println("Pago aprobado -> abriendo compuerta");
        servo.write(60);  // abre la compuerta (60°)
        delay(1500);      // espera que caiga el caramelo
        servo.write(0);   // vuelve a cerrar (0°)
        delay(500);
      }
    }
    http.end();
  }
  delay(5000); // consulta cada 5s
}

Explicación línea por línea:

#include <WiFi.h> → permite al ESP32 conectarse a WiFi.

#include <HTTPClient.h> → sirve para hacer peticiones HTTP al servidor.

#include <Servo.h> → librería para controlar un servo.

const char* ssid y password → datos de tu WiFi.

const char* servidor → dirección del backend (/estado).

Servo servo; → objeto para manejar el servo.

servo.attach(servoPin) → conecta el servo al pin GPIO5.

servo.write(0) → pone el servo en la posición inicial (compuerta cerrada).

En el loop:

Se conecta al backend y hace un GET a /estado.

Si el servidor responde "despachar", abre la compuerta (servo a 60°), espera 1,5 segundos, y la vuelve a cerrar.

Luego espera 5 segundos antes de volver a consultar.
