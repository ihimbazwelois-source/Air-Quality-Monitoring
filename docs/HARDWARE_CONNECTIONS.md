# Smart Air Quality Monitoring System - Hardware Connections

## Physical Hardware Setup

This image shows the **hardware connections** of our Smart Air Quality Monitoring System prototype:

### Components Visible:

1. **ESP8266 or NodeMCU Microcontroller** (top right board)
   - WiFi-enabled microcontroller
   - Acts as the central processing unit
   - Handles data collection and transmission

2. **MQ135 Air Quality Sensor** (bottom left with cylindrical sensor head)
   - Measures air pollution levels
   - Detects various gases (CO2, NH3, NOx, alcohol, benzene)
   - Analog sensor output to microcontroller

3. **DHT11/DHT22 Temperature & Humidity Sensor** (blue board, left side)
   - Measures ambient temperature
   - Measures relative humidity
   - Digital output to microcontroller

4. **Relay Module** (blue board, center-left)
   - Controls alert mechanisms
   - Switches high-power circuits
   - Triggered by microcontroller based on air quality thresholds

5. **Power Supply** (connected via wires)
   - Provides necessary voltage for all components
   - Battery or AC adapter connection

6. **Breadboard & Prototyping Board** (center)
   - Houses the microcontroller
   - Provides connection points for sensors
   - Enables easy modifications and testing

7. **Jumper Wires (Various Colors)**
   - Purple/Blue: Power connections
   - Green: Data signal lines
   - Orange/Red: Ground and signal connections
   - Yellow: Additional connections

### Connection Architecture:

```
MQ135 Sensor → ADC Input (Microcontroller)
     ↓
DHT Sensor → Digital Input (Microcontroller)
     ↓
Relay Module → Digital Output (Microcontroller)
     ↓
Alert/Output System
```

### Key Features of This Setup:

✅ **Modular Design** - Easy to add or remove components
✅ **Prototype Testing** - Breadboard allows for quick modifications
✅ **Wireless Capable** - ESP8266/NodeMCU has built-in WiFi
✅ **Real-time Monitoring** - Direct sensor input to microcontroller
✅ **Alert System** - Relay activation on threshold detection

---

**Note**: This is a prototype/testing setup. The production version will be PCB-mounted and integrated into a single enclosure for deployment.

