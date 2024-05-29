# ApostilaEcosniff
// Autenticações
#define BLYNK_PRINT Serial
#define BLYNK_TEMPLATE_ID "Template"
#define BLYNK_TEMPLATE_NAME "Nome"
#define BLYNK_AUTH_TOKEN "Token"

// Bibliotecas
#include <WiFi.h>
#include <WiFiClient.h>
#include <BlynkSimpleEsp32.h>
#include "DHTesp.h"

#define DHT_PIN 4 // Pino D2 (GPIO2) da ESP para o sensor DHT
const int smokeSensor = 34; // Pino ao qual o sensor MQ135 está conectado//alteração do pino

DHTesp dhtSensor; // Instancia um objeto para podermos usar as funções da biblioteca DHT

char auth[] = BLYNK_AUTH_TOKEN; // Armazena o token
char ssid[] = "Rede";   // Nome da rede Wi-Fi.
char pass[] = "Senha";  // Senha da rede Wi-Fi.

BlynkTimer timer; // Cria uma instância da classe BlynkTimer chamada timer para agendar a leitura e envio dos dados.

void sendSensor()
{
  int valorMonCarbono = analogRead(smokeSensor); // Lê o valor do sensor de monóxido de carbono

  TempAndHumidity data = dhtSensor.getTempAndHumidity();
  Serial.println("Temp: " + String(data.temperature, 2) + "°C");
  Serial.println("Humidity: " + String(data.humidity, 2) + "%");
  Serial.println("Monóxido de Carbono: " + String(valorMonCarbono) + "PPM");
  Serial.println("---");

  Blynk.virtualWrite(V0, data.temperature); // Envie os dados para os pinos virtuais no Blynk
  Blynk.virtualWrite(V1, data.humidity);    // Envie os dados para os pinos virtuais no Blynk
  Blynk.virtualWrite(V2, valorMonCarbono);  // Envie os dados para os pinos virtuais no Blynk
}

void setup()
{
  Serial.begin(9600);
  pinMode(smokeSensor, INPUT); // Define o pino do sensor MQ135 como entrada
  dhtSensor.setup(DHT_PIN, DHTesp::DHT22);
  Blynk.begin(auth, ssid, pass);
  timer.setInterval(1000, sendSensor);
}

void loop()
{
  Blynk.run();
  timer.run();
}
