#define BLYNK_TEMPLATE_ID "TMPL6nWns-acG"
#define BLYNK_TEMPLATE_NAME "IoT Home Automation"
#define BLYNK_AUTH_TOKEN "ckpjc3fV3K3Lq0BzzJr9kp48sdxJZgHK"

#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>
#include <NewPing.h>

const char *ssid = "Apiz";
const char *password = "tekalapassnya";
char auth[] = "ckpjc3fV3K3Lq0BzzJr9kp48sdxJZgHK";

const uint8_t lamp1 = 5;
const uint8_t lamp2 = 10;
const uint8_t lamp3 = 4;
const uint8_t fan1 = 13;
const uint8_t TRIGGER_PIN = D5;
const uint8_t ECHO_PIN = D6;

NewPing ultrasonicSensor(TRIGGER_PIN, ECHO_PIN); // Initialize ultrasonic sensor

bool blynkConnected = false;

void checkBlynkConnection() {
    if (!Blynk.connected()) {
        Serial.println("Blynk disconnected, sensor is active...");
        blynkConnected = false;
    } else {
        Serial.println("Blynk connected, sensor is inactive...");
        blynkConnected = true;
    }
}

BLYNK_WRITE(V0) {
    int switchState = param.asInt(); // Get the state of the switch

    // Toggle lamp 1 based on the switch state
    if (switchState == HIGH) {
        digitalWrite(lamp1, LOW); // Turn ON the lamp
    } else {
        digitalWrite(lamp1, HIGH); // Turn OFF the lamp
    }
}

BLYNK_WRITE(V1) {
    int switchState = param.asInt(); // Get the state of the switch

    // Toggle lamp 2 based on the switch state
    if (switchState == HIGH) {
        digitalWrite(lamp2, LOW); // Turn ON the lamp
    } else {
        digitalWrite(lamp2, HIGH); // Turn OFF the lamp
    }
}

BLYNK_WRITE(V2) {
    int switchState = param.asInt(); // Get the state of the switch

    // Toggle lamp 3 based on the switch state
    if (switchState == HIGH) {
        digitalWrite(lamp3, LOW); // Turn ON the lamp
    } else {
        digitalWrite(lamp3, HIGH); // Turn OFF the lamp
    }
}

BLYNK_WRITE(V3) {
    int switchState = param.asInt(); // Get the state of the switch

    // Toggle fan 1 based on the switch state
    if (switchState == HIGH) {
        digitalWrite(fan1, LOW); // Turn ON the fan
    } else {
        digitalWrite(fan1, HIGH); // Turn OFF the fan
    }
}

void setup() {
    Serial.begin(9600);

    pinMode(lamp1, OUTPUT);
    pinMode(lamp2, OUTPUT);
    pinMode(lamp3, OUTPUT);
    pinMode(fan1, OUTPUT);
    pinMode(TRIGGER_PIN, OUTPUT); // Set trigger pin as output

    WiFi.begin(ssid, password);
    while (WiFi.status() != WL_CONNECTED) {
        delay(1000);
        Serial.println("Connecting...");
    }

    Serial.println("\nConnected to WiFi");
    Serial.print("IP Address: ");
    Serial.println(WiFi.localIP());

    Blynk.begin(auth, ssid, password);

    // Check Blynk connection status every 5 seconds
    checkBlynkConnection();
}

void loop() {
    Blynk.run();
    checkBlynkConnection();

    if (!blynkConnected) {
        // Measure distance using ultrasonic sensor
        int distance = ultrasonicSensor.ping_cm();

        if (distance < 30) {
            // Turn on all lamps and fan if object detected within 30cm
            digitalWrite(lamp1, LOW);
            digitalWrite(lamp2, LOW);
            digitalWrite(lamp3, LOW);
            digitalWrite(fan1, LOW);
        } else {
            // Turn off all lamps and fan if no object detected within 30cm
            digitalWrite(lamp1, HIGH);
            digitalWrite(lamp2, HIGH);
            digitalWrite(lamp3, HIGH);
            digitalWrite(fan1, HIGH);
        }
    }
    delay(1000); // Delay to reduce sensor reading frequency
}
