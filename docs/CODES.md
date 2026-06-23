# Smart Air Quality Monitoring System - Source Code

## Overview

This page contains the source code for the Smart Air Quality Monitoring System. The system uses an ESP32 microcontroller to collect sensor data and transmit it to Adafruit IO for cloud-based monitoring.

---

## Main Arduino/ESP32 Firmware

The main firmware handles:
- WiFi connectivity
- MQTT communication with Adafruit IO
- Serial communication with sensor modules
- Data parsing and publishing

### Code

```cpp
#include <WiFi.h>
#include <PubSubClient.h>
#include "Adafruit_MQTT.h"
#include "Adafruit_MQTT_Client.h"

#define WIFI_SSID "xxxxxxxxxx"
#define WIFI_PASS "xxxxxxxxxx"

#define AIO_SERVER      "io.adafruit.com"
#define AIO_SERVERPORT  1883
#define AIO_USERNAME    "xxxxx"
#define AIO_KEY         "xxxxxxx"

// Define Serial2 pins
#define RXD2 16
#define TXD2 17
HardwareSerial Serial2(2);  // UART2

// Create WiFi client and MQTT client
WiFiClient client;
Adafruit_MQTT_Client mqtt(&client, AIO_SERVER, AIO_SERVERPORT, AIO_USERNAME, AIO_KEY);

// Feeds
Adafruit_MQTT_Publish co2_feed     = Adafruit_MQTT_Publish(&mqtt, AIO_USERNAME "/feeds/co2");
Adafruit_MQTT_Publish pm_feed      = Adafruit_MQTT_Publish(&mqtt, AIO_USERNAME "/feeds/pm25");
Adafruit_MQTT_Publish temp_feed    = Adafruit_MQTT_Publish(&mqtt, AIO_USERNAME "/feeds/temp");
Adafruit_MQTT_Publish humidity_feed= Adafruit_MQTT_Publish(&mqtt, AIO_USERNAME "/feeds/humidity");

void setup() {
  Serial.begin(9600);
  Serial2.begin(9600, SERIAL_8N1, RXD2, TXD2);

  delay(2000);
  Serial.println("Connecting to WiFi...");

  WiFi.begin(WIFI_SSID, WIFI_PASS);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("\nWiFi Connected!");
}

void loop() {
  MQTT_connect();

  if (Serial2.available()) {
    String line = Serial2.readStringUntil('\n');
    line.trim();

    if (line.length() > 0) {
      Serial.print("Received: ");
      Serial.println(line);

      float co2 = getValue(line, "CO2=");
      float pm = getValue(line, "PM=");
      float temp = getValue(line, "TEMP=");
      float hum = getValue(line, "HUM=");

      co2_feed.publish(co2);
      pm_feed.publish(pm);
      temp_feed.publish(temp);
      humidity_feed.publish(hum);
    }
  }
}

// ---------- Helper Functions ----------
float getValue(String data, String key) {
  int index = data.indexOf(key);
  if (index == -1) return 0;
  int end = data.indexOf(';', index);
  String val = (end == -1) ? data.substring(index + key.length()) : data.substring(index + key.length(), end);
  return val.toFloat();
}

void MQTT_connect() {
  int8_t ret;
  if (mqtt.connected()) return;

  Serial.print("Connecting to Adafruit IO... ");
  while ((ret = mqtt.connect()) != 0) {
    Serial.println(mqtt.connectErrorString(ret));
    Serial.println("Retrying MQTT connection in 5 seconds...");
    mqtt.disconnect();
    delay(5000);
  }
  Serial.println("MQTT Connected!");
}
```

---

## Code Explanation

### **Libraries Used**
- `WiFi.h` - WiFi connectivity for ESP32
- `PubSubClient.h` - MQTT client library
- `Adafruit_MQTT.h` & `Adafruit_MQTT_Client.h` - Adafruit IO integration

### **Configuration**
- **WIFI_SSID & WIFI_PASS** - Your WiFi network credentials
- **AIO_SERVER** - Adafruit IO MQTT server address
- **AIO_USERNAME & AIO_KEY** - Adafruit IO account credentials
- **Serial2 Pins** - GPIO 16 (RX) and GPIO 17 (TX) for sensor communication

### **MQTT Feeds**
The system publishes sensor data to these Adafruit IO feeds:
- `co2` - CO2 levels (ppm)
- `pm25` - Particulate Matter 2.5 (μg/m³)
- `temp` - Temperature (°C)
- `humidity` - Relative Humidity (%)

### **Main Functions**

#### **setup()**
- Initializes serial communication (9600 baud)
- Connects to WiFi network
- Waits for successful connection

#### **loop()**
- Maintains MQTT connection
- Reads data from Serial2 (sensor module)
- Parses sensor values (CO2, PM2.5, Temperature, Humidity)
- Publishes data to Adafruit IO feeds

#### **getValue()**
- Helper function to extract sensor values from serial string
- Looks for key-value pairs in format: `KEY=value;`
- Returns parsed float value

#### **MQTT_connect()**
- Establishes connection with Adafruit IO
- Implements retry mechanism
- Handles connection errors gracefully

---

## Setup Instructions

### **1. Required Libraries**
Install the following libraries in Arduino IDE:
- Adafruit MQTT Library
- ESP32 Core Library
- WiFi Library (built-in)

### **2. Configuration**
Update the following in the code:
```cpp
#define WIFI_SSID "your_wifi_name"
#define WIFI_PASS "your_wifi_password"
#define AIO_USERNAME "your_adafruit_username"
#define AIO_KEY "your_adafruit_key"
```

### **3. Hardware Connections**
- GPIO 16 → Sensor Module RX
- GPIO 17 → Sensor Module TX
- GND → Common Ground

### **4. Upload to ESP32**
1. Connect ESP32 to computer via USB
2. Select ESP32 board in Arduino IDE
3. Choose correct COM port
4. Click Upload

---

## Data Format

The sensor module sends data in this format:
```
CO2=450;PM=25.5;TEMP=28.3;HUM=65.2;
```

The code parses each value and publishes to Adafruit IO.

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| WiFi connection fails | Check SSID, password, and WiFi signal |
| MQTT connection fails | Verify Adafruit IO credentials and internet connection |
| No sensor data | Check Serial2 connections and sensor module |
| Data not updating | Ensure sensor module is sending data correctly |

---

**For more help, contact us using the information on the [Contact Page](CONTACT.md)**
