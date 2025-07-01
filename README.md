#define BLYNK_TEMPLATE_ID "TMPL3Tk4MhUc1"
#define BLYNK_TEMPLATE_NAME "Home Automatiom"
#define BLYNK_AUTH_TOKEN "5DtiCmLSjmjbH-6aQTNNCR5HfFhDI8DF"

#define BLYNK_PRINT Serial

#include <ESP8266WiFi.h>
#include <WiFiClient.h>
#include <BlynkSimpleEsp8266.h>

char auth[] = BLYNK_AUTH_TOKEN;
char ssid[] = "Sohail";
char pass[] = "123456789";

const int ledPin = D4;

void connectToWiFi() {
  Serial.print("Connecting to ");
  Serial.println(ssid);

  WiFi.begin(ssid, pass);
  
  int wifi_attempts = 0;
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
    wifi_attempts++;

    if (wifi_attempts > 60) {
      Serial.println("\nRestarting module due to failed WiFi connection...");
      ESP.restart();
    }
  }
  
  Serial.println("\nWiFi connected!");
  Serial.print("IP address: ");
  Serial.println(WiFi.localIP());
}

void setup() {
  Serial.begin(9600);
  pinMode(ledPin, OUTPUT);

  connectToWiFi();
  
  Blynk.config(auth);

  while (Blynk.connect() == false) {
    Serial.print(".");
    delay(500);
  }

  Serial.println("\nConnected to Blynk server!");
}

BLYNK_WRITE(V2) {
  int pinValue = param.asInt(); 
  digitalWrite(ledPin, pinValue); 
}

void loop() {
  if (WiFi.status() != WL_CONNECTED) {
    Serial.println("WiFi disconnected. Reconnecting...");
    connectToWiFi();
  }
  
  Blynk.run();
}
