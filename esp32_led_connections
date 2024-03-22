#include <Arduino.h>
#if defined(ESP32)
  #include <WiFi.h>
#elif defined(ESP8266)
  #include <ESP8266WiFi.h>
#endif
#include <Firebase_ESP_Client.h>

// Provide the token generation process info.
#include "addons/TokenHelper.h"
// Provide the RTDB payload printing info and other helper functions.
#include "addons/RTDBHelper.h"

// Insert your network credentials
#define WIFI_SSID "Tech G"
#define WIFI_PASSWORD "cburobotics"

// Insert Firebase project API Key
#define API_KEY " "

// Insert RTDB URL
#define DATABASE_URL " "

// Define Firebase Data object
FirebaseData fbdo;

FirebaseAuth auth;
FirebaseConfig config;

unsigned long sendDataPrevMillis = 0;
int count = 0;
bool signupOK = false;

// Define LED pin
const int ledPin = D1; // Change this to your LED pin

void setup(){
  Serial.begin(115200);
  pinMode(ledPin, OUTPUT); // Initialize the LED pin as an output

  WiFi.begin(WIFI_SSID, WIFI_PASSWORD);
  Serial.print("Connecting to Wi-Fi");
  while (WiFi.status() != WL_CONNECTED){
    Serial.print(".");
    delay(300);
  }
  Serial.println();
  Serial.print("Connected with IP: ");
  Serial.println(WiFi.localIP());
  Serial.println();

  /* Assign the api key (required) */
  config.api_key = API_KEY;

  /* Assign the RTDB URL (required) */
  config.database_url = DATABASE_URL;

  /* Sign up */
  if (Firebase.signUp(&config, &auth, "", "")){
    Serial.println("ok");
    signupOK = true;
  }
  else{
    Serial.printf("%s\n", config.signer.signupError.message.c_str());
  }

  /* Assign the callback function for the long running token generation task */
  config.token_status_callback = tokenStatusCallback; //see addons/TokenHelper.h
  
  Firebase.begin(&config, &auth);
  Firebase.reconnectWiFi(true);
}

void loop(){
  if (Firebase.ready() && signupOK && (millis() - sendDataPrevMillis > 15000 || sendDataPrevMillis == 0)){
    sendDataPrevMillis = millis();

    // Read the LED state from the database path
    if (Firebase.RTDB.getBool(&fbdo, "test/ledState")){
      bool ledState = fbdo.boolData();

      if (ledState) {
        // Turn on the LED
        digitalWrite(ledPin, HIGH);
      } else {
        // Turn off the LED
        digitalWrite(ledPin, LOW);
      }

      Serial.print("LED State: ");
      Serial.println(ledState);
    }
    else {
      Serial.println("Failed to read LED state");
    }
  }
}
