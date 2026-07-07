## Smart City Infrastructure Proposal for Jammu



##### 1\. Executive Summary

This proposal outlines an optimized, cost-effective, and scalable Internet of Things (IoT) infrastructure designed for the Jammu Smart City initiative. To address urban challenges unique to Jammu's topography and seasonal population fluctuations, this system focuses on three core domains: Smart Street Lighting, Automated Waste Bin Monitoring, and Real-time Air Quality Index (AQI) Monitoring.

##### 2\. Selected Domains \& System Architecture

###### Domain 1: Smart Street Lighting

Sensors Needed: LDR (Light Dependent Resistor) for ambient light sensing; PIR (Passive Infrared) Motion Sensor for pedestrian/vehicle detection.

Microcontroller \& Connectivity: ESP32 Development Board (integrated Wi-Fi/Bluetooth) or ESP32 with a LoRaWAN module (SX1278) for long-range municipal deployment.

Data Flow: Sensor (Analog/Digital signals) → Microcontroller (ESP32) → MQTT Protocol (over Wi-Fi/LoRaWAN) → AWS IoT Core / ThingsBoard Cloud → Web-based Centralized Command Dashboard.

Approximate Cost per Node: ₹650 INR (ESP32: ₹350, LDR \& PIR: ₹100, Relay Module \& Enclosure: ₹200).

###### Domain 2: Waste Bin Fill Levels

Sensors Needed: HC-SR04 Ultrasonic Distance Sensor (ruggedized/waterproof variant like JSN-SR04T).

Microcontroller \& Connectivity: Arduino Nano coupled with an SX1278 LoRa Transceiver module (ideal for low-power, deep-indoor, or street-level penetration).

Data Flow: Ultrasonic Sensor (Pulse Echo) → Arduino →LoRaWAN Protocol →LoRa Gateway →The Things Network (TTN) Cloud → Municipal Sanitation Routing Dashboard.

Approximate Cost per Node: ₹850 INR (Arduino Nano: ₹250, JSN-SR04T: ₹300, LoRa Module: ₹200, Battery \& Casing: ₹100).

###### Domain 3: Air Quality Monitoring

Sensors Needed: PMS5003 (Particulate Matter PM2.5/PM10) and MQ-135 (Gas sensor for NH\_3, NO\_x, CO\_2).

Microcontroller \& Connectivity: ESP32 DevKit V1 (utilizing its dual-core processing for heavy sensor sampling).

Data Flow: Sensors (UART/Analog) → ESP32 →HTTP POST / MQTT → ThingSpeak or InfluxDB Cloud →Public Awareness Mobile App / City Dashboard.

Approximate Cost per Node: ₹1,450 INR (ESP32: ₹350, PMS5003: ₹800, MQ-135: ₹150, Power Adapter \& PCB: ₹150).

##### 3\. Firmware Implementations

###### Code 1: Smart Street Lighting (ESP32 + MQTT)

\#include <WiFi.h>

\#include <PubSubClient.h>





\#define LDR\_PIN 34

\#define PIR\_PIN 27

\#define RELAY\_PIN 14





const char\* ssid = "Jammu\_Smart\_City\_WiFi";

const char\* password = "SecurePassword123";

const char\* mqtt\_server = "192.168.1.100";





WiFiClient espClient;

PubSubClient client(espClient);





void setup() {

&#x20; pinMode(LDR\_PIN, INPUT);

&#x20; pinMode(PIR\_PIN, INPUT);

&#x20; pinMode(RELAY\_PIN, OUTPUT);

&#x20; Serial.begin(115200);

&#x20;

&#x20; WiFi.begin(ssid, password);

&#x20; while (WiFi.status() != WL\_CONNECTED) delay(500);

&#x20; client.setServer(mqtt\_server, 1883);

}





void loop() {

&#x20; if (!client.connected()) {

&#x20;   while (!client.connected()) {

&#x20;     if (client.connect("StreetLightNode\_01")) {} else { delay(5000); }

&#x20;   }

&#x20; }

&#x20; client.loop();





&#x20; int ambientLight = analogRead(LDR\_PIN);

&#x20; int motionDetected = digitalRead(PIR\_PIN);





&#x20; // If dark (LDR low value) and motion detected -> Turn on full brightness

&#x20; if (ambientLight < 1500 \&\& motionDetected == HIGH) {

&#x20;   digitalWrite(RELAY\_PIN, HIGH);

&#x20;   client.publish("city/streetlights/status", "ON\_BRIGHT");

&#x20; } else if (ambientLight < 1500 \&\& motionDetected == LOW) {

&#x20;   digitalWrite(RELAY\_PIN, HIGH); // Keep dim/on for safety

&#x20;   client.publish("city/streetlights/status", "ON\_DIM");

&#x20; } else {

&#x20;   digitalWrite(RELAY\_PIN, LOW);

&#x20;   client.publish("city/streetlights/status", "OFF");

&#x20; }

&#x20; delay(10000); // Sample every 10 seconds

}



}

###### Code 2: Waste Bin Fill Levels (Arduino + LoRa)

\#include <SPI.h>

\#include <LoRa.h>





\#define TRIG\_PIN 9

\#define ECHO\_PIN 8

\#define BIN\_HEIGHT\_CM 100 // Total height of the dustbin





void setup() {

&#x20; Serial.begin(9600);

&#x20; pinMode(TRIG\_PIN, OUTPUT);

&#x20; pinMode(ECHO\_PIN, INPUT);





&#x20; if (!LoRa.begin(433E6)) { // 433 MHz band for India region LoRa standard

&#x20;   Serial.println("Starting LoRa failed!");

&#x20;   while (1);

&#x20; }

}





void loop() {

&#x20; long duration;

&#x20; int distance;

&#x20;

&#x20; digitalWrite(TRIG\_PIN, LOW);

&#x20; delayMicroseconds(2);

&#x20; digitalWrite(TRIG\_PIN, HIGH);

&#x20; delayMicroseconds(10);

&#x20; digitalWrite(TRIG\_PIN, LOW);

&#x20;

&#x20; duration = pulseIn(ECHO\_PIN, HIGH);

&#x20; distance = duration \* 0.034 / 2;





&#x20; // Calculate percentage fill level

&#x20; int fillLevel = 100 - ((distance \* 100) / BIN\_HEIGHT\_CM);

&#x20; if(fillLevel < 0) fillLevel = 0;

&#x20; if(fillLevel > 100) fillLevel = 100;





&#x20; // Transmit over LoRa packet

&#x20; LoRa.beginPacket();

&#x20; LoRa.print("BinID:048,Fill:");

&#x20; LoRa.print(fillLevel);

&#x20; LoRa.endPacket();





&#x20; delay(60000); // Send data once every minute to conserve battery

}







###### Code 3: Air Quality Monitoring Node (ESP32 + HTTP)



\#include <WiFi.h>

\#include <HTTPClient.h>





\#define MQ135\_PIN 32

const char\* ssid = "Jammu\_Smart\_City\_WiFi";

const char\* password = "SecurePassword123";

const char\* serverEndpoint = "http://api.jammusmartcity.jk.gov.in/aqi";





void setup() {

&#x20; Serial.begin(115200);

&#x20; pinMode(MQ135\_PIN, INPUT);

&#x20; WiFi.begin(ssid, password);

&#x20;

&#x20; while (WiFi.status() != WL\_CONNECTED) {

&#x20;   delay(1000);

&#x20;   Serial.println("Connecting to WiFi...");

&#x20; }

}





void loop() {

&#x20; if (WiFi.status() == WL\_CONNECTED) {

&#x20;   HTTPClient http;

&#x20;   http.begin(serverEndpoint);

&#x20;   http.addHeader("Content-Type", "application/json");





&#x20;   int rawGasVal = analogRead(MQ135\_PIN);

&#x20;   // Simplified scaling for demonstration purposes

&#x20;   float airQualityIndex = (rawGasVal / 4095.0) \* 500.0;





&#x20;   String jsonPayload = "{\\"node\_id\\":\\"AQI\_JAMMU\_048\\",\\"aqi\\":" + String(airQualityIndex) + "}";

&#x20;  

&#x20;   int httpResponseCode = http.POST(jsonPayload);

&#x20;   Serial.print("HTTP Response code: ");

&#x20;   Serial.println(httpResponseCode);

&#x20;  

&#x20;   http.end();

&#x20; }

&#x20; delay(30000); // Stream AQI metrics every 30 seconds

}















