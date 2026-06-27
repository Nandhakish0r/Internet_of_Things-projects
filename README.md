<div align="center">

# 🚦 2-Way Railway Crossing Traffic Signal Mechanism

### An Arduino-based Automatic Traffic Control System for Railway Level Crossings

[![Arduino](https://img.shields.io/badge/Arduino-UNO-00979D?style=for-the-badge&logo=arduino&logoColor=white)](https://www.arduino.cc/)
[![Language](https://img.shields.io/badge/Language-C%2FC%2B%2B-blue?style=for-the-badge&logo=c%2B%2B&logoColor=white)](https://www.arduino.cc/reference/en/)
[![Platform](https://img.shields.io/badge/Simulated%20on-Tinkercad-F16831?style=for-the-badge)](https://www.tinkercad.com/)
[![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=for-the-badge)](#)
[![Repo](https://img.shields.io/badge/GitHub-Internet__of__Things--projects-181717?style=for-the-badge&logo=github)](https://github.com/Nandhakish0r/Internet_of_Things-projects/tree/main/Railway_Crossing_Traffic_Signal_mechanism)

</div>

---

## 📌 Overview

This project simulates a **2-Way Railway Level Crossing Traffic Signal** using an **Arduino UNO**. The system automatically controls road-side traffic signals at a railway level crossing — halting vehicles when a train is passing and releasing them when the track is clear.

It models real-world railway gate logic using **5 LEDs** (Red, Yellow, Green for road + Green, Red for train) controlled in a timed cyclic sequence, making the system safe, predictable, and fully automatic.

---

## 🧠 Concept & Working Principle

At a **level crossing**, road vehicles and trains share the same intersection point. The core challenge:

- 🚗 **Stop road traffic** when a train is approaching or passing
- ✅ **Allow road traffic** to move when the crossing is clear
- 🔄 **Cycle continuously** to keep the intersection safe at all times

### State Machine Logic

The system runs through **4 states** in a loop, each lasting **2 seconds**:

```
┌─────────┬─────────────────┬──────────────────┬──────────────────────────────────┐
│  State  │   Road Signal   │  Train Signal    │  Meaning                         │
├─────────┼─────────────────┼──────────────────┼──────────────────────────────────┤
│    1    │  🔴 RED         │  🟢 GREEN        │  Train passing — road blocked    │
│    2    │  🟡 YELLOW      │  🟢 GREEN        │  Train clearing — road preparing │
│    3    │  🟢 GREEN       │  🔴 RED          │  Crossing clear — road open      │
│    4    │  🟢 GREEN       │  🔴 RED          │  Road about to close             │
└─────────┴─────────────────┴──────────────────┴──────────────────────────────────┘
                              ↓ Repeat from State 1
```

> ⚠️ **Note:** No physical sensor is used in this version. The system runs on a **timed loop** to demonstrate the signal cycling logic. In a real deployment, IR sensors or reed switches would detect the train and trigger state transitions dynamically.

---

## 🔌 Circuit Diagram (Tinkercad Simulation)

> The circuit was first designed and tested on **Tinkercad Circuits** before building the physical prototype.

| View 1 | View 2 | View 3 |
|:------:|:------:|:------:|
| ![Tinkercad 1](Railway%20_Crossing_Traffic_Signal_mechanism/t1.png) | ![Tinkercad 2](Railway%20_Crossing_Traffic_Signal_mechanism/t2.png) | ![Tinkercad 3](Railway%20_Crossing_Traffic_Signal_mechanism/t3.png) |

*Fig 1 — Tinkercad simulation: Arduino UNO driving Traffic Light LEDs and Train Signal LEDs via breadboard*

### 📍 Pin Configuration

| Pin | LED Color | Signal Group | Function |
|:---:|:---------:|:------------:|:---------|
| `11` | 🔴 Red | ROAD | Road — Stop |
| `12` | 🟡 Yellow | ROAD | Road — Caution |
| `10` | 🟢 Green | ROAD | Road — Go |
| `9` | 🟢 Green | TRAIN | Train — Track Clear |
| `8` | 🔴 Red | TRAIN | Train — Approaching |

---

## 🛠️ Hardware Setup

> Physical build using **Arduino UNO**, **5 LEDs**, **220Ω resistors**, and a **breadboard**.

| View 1 | View 2 | View 3 |
|:------:|:------:|:------:|
| ![Hardware 1](Railway%20_Crossing_Traffic_Signal_mechanism/hw1.png) | ![Hardware 2](Railway%20_Crossing_Traffic_Signal_mechanism/hw2.png) | ![Hardware 3](Railway%20_Crossing_Traffic_Signal_mechanism/hw3.png) |

*Fig 2 — Physical hardware: Arduino UNO with road + train LED signal circuit on breadboard*

---

## 🧰 Components Required

| Component | Qty | Purpose |
|-----------|:---:|---------|
| Arduino UNO | 1 | Main microcontroller |
| Red LED | 2 | Road Red + Train Red |
| Yellow LED | 1 | Road Yellow (caution) |
| Green LED | 2 | Road Green + Train Green |
| 220Ω Resistor | 5 | Current limiting per LED |
| Breadboard | 1 | Prototyping base |
| Jumper Wires | ~15 | Connections |
| USB Type-B Cable | 1 | Power + code upload |

---

## 💻 Arduino Code

```cpp
void setup() {
  // Road Signal Pins
  pinMode(11, OUTPUT);  // RED
  pinMode(12, OUTPUT);  // YELLOW
  pinMode(10, OUTPUT);  // GREEN

  // Train Signal Pins
  pinMode(9, OUTPUT);   // GREEN (track clear)
  pinMode(8, OUTPUT);   // RED   (train approaching)
}

void loop() {

  // ── STATE 1: Train Passing | Road → STOP ────────────────────
  digitalWrite(11, HIGH);  // ROAD  RED    → ON
  digitalWrite(12, LOW);   // ROAD  YELLOW → OFF
  digitalWrite(10, LOW);   // ROAD  GREEN  → OFF
  digitalWrite(9,  HIGH);  // TRAIN GREEN  → ON  (clear to pass)
  digitalWrite(8,  LOW);   // TRAIN RED    → OFF
  delay(2000);

  // ── STATE 2: Train Clearing | Road → WAIT ───────────────────
  digitalWrite(11, LOW);   // ROAD  RED    → OFF
  digitalWrite(12, HIGH);  // ROAD  YELLOW → ON
  digitalWrite(10, LOW);   // ROAD  GREEN  → OFF
  digitalWrite(9,  HIGH);  // TRAIN GREEN  → ON  (still moving)
  digitalWrite(8,  LOW);   // TRAIN RED    → OFF
  delay(2000);

  // ── STATE 3: Crossing Clear | Road → GO ─────────────────────
  digitalWrite(11, LOW);   // ROAD  RED    → OFF
  digitalWrite(12, LOW);   // ROAD  YELLOW → OFF
  digitalWrite(10, HIGH);  // ROAD  GREEN  → ON
  digitalWrite(9,  LOW);   // TRAIN GREEN  → OFF
  digitalWrite(8,  HIGH);  // TRAIN RED    → ON  (no train)
  delay(2000);

  // ── STATE 4: Road Closing Soon | Road → PREPARE ─────────────
  digitalWrite(11, LOW);   // ROAD  RED    → OFF
  digitalWrite(12, LOW);   // ROAD  YELLOW → OFF
  digitalWrite(10, HIGH);  // ROAD  GREEN  → ON  (last chance)
  digitalWrite(9,  LOW);   // TRAIN GREEN  → OFF
  digitalWrite(8,  HIGH);  // TRAIN RED    → ON  (train incoming)
  delay(2000);

  // → Loops back to STATE 1
}
```

---

## 🚀 How to Run

### Option A — Simulate on Tinkercad *(No hardware needed)*

1. Go to [tinkercad.com](https://www.tinkercad.com) → **Circuits** → **Create new Circuit**
2. Drag in **Arduino UNO** + **5 LEDs** + **5 × 220Ω resistors** onto the breadboard
3. Wire each LED anode → resistor → Arduino pins `8, 9, 10, 11, 12`; cathodes to GND
4. Click **Code** → paste the Arduino code above
5. Click **Start Simulation** — watch signals cycle

### Option B — Upload to Physical Arduino

1. Install [Arduino IDE](https://www.arduino.cc/en/software)
2. Build the circuit on breadboard per the pin config table above
3. Connect Arduino UNO via USB
4. Open Arduino IDE → paste the code
5. **Tools → Board:** `Arduino UNO` | **Tools → Port:** select correct COM/tty port
6. Click **Upload** (`Ctrl+U`)
7. LEDs begin cycling through the 4 signal states automatically

---

## 📊 Signal State Summary

| State | Road | Train | Situation | Duration |
|:-----:|:----:|:-----:|-----------|:--------:|
| 1 | 🔴 RED | 🟢 GREEN | Train passing — road fully blocked | 2 sec |
| 2 | 🟡 YELLOW | 🟢 GREEN | Train clearing — road about to open | 2 sec |
| 3 | 🟢 GREEN | 🔴 RED | Crossing clear — vehicles may pass | 2 sec |
| 4 | 🟢 GREEN | 🔴 RED | Road closing soon — train incoming | 2 sec |

---

## 🔍 Limitations & Future Scope

**Current Limitations**
- Timer-based only — no real train detection
- Fixed 2-second delays regardless of actual train speed
- No physical gate barrier mechanism

**Future Enhancements**
- 🔭 **IR / Reed switch sensors** — detect actual train presence and trigger state change dynamically
- 🚧 **Servo motor gate** — physical barrier arm that lifts/drops automatically
- 🔔 **Buzzer alarm** — audio warning when train is approaching
- 🖥️ **LCD display** — "TRAIN APPROACHING" / "SAFE TO CROSS" messages
- 📡 **ESP8266/ESP32** — IoT integration for remote monitoring and alerts

---

## 👤 Author

**Nandakishor** — Electronics & Communication Engineering
IIIT Kottayam | VLSI & Embedded Systems

[![GitHub](https://img.shields.io/badge/GitHub-Nandhakish0r-181717?style=flat-square&logo=github&logoColor=white)](https://github.com/Nandhakish0r)
[![Repo](https://img.shields.io/badge/Repo-Internet__of__Things--projects-blue?style=flat-square&logo=github)](https://github.com/Nandhakish0r/Internet_of_Things-projects)

---

<div align="center">

*Built with Arduino UNO · Part of the IoT Projects series*

</div>
<div align="center">

<div align="center">

<div align="center">

# 🌡️ Temperature Monitoring System with LCD Display

### An Arduino-based Real-Time Temperature & Humidity Monitor using DHT11 Sensor and 16×2 LCD

[![Arduino](https://img.shields.io/badge/Arduino-UNO-00979D?style=for-the-badge&logo=arduino&logoColor=white)](https://www.arduino.cc/)
[![Language](https://img.shields.io/badge/Language-C%2FC%2B%2B-blue?style=for-the-badge&logo=c%2B%2B&logoColor=white)](https://www.arduino.cc/reference/en/)
[![Sensor](https://img.shields.io/badge/Sensor-DHT11-red?style=for-the-badge)](#)
[![Platform](https://img.shields.io/badge/Simulated%20on-Tinkercad-F16831?style=for-the-badge)](https://www.tinkercad.com/)
[![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=for-the-badge)](#)
[![Repo](https://img.shields.io/badge/GitHub-Internet__of__Things--projects-181717?style=for-the-badge&logo=github)](https://github.com/Nandhakish0r/Internet_of_Things-projects)

</div>

---

## 📌 Overview

This project builds a **real-time temperature and humidity monitoring system** using an **Arduino UNO**, a **DHT11 digital sensor**, and a **16×2 LCD display**. The system continuously reads ambient temperature in both **Celsius** and **Fahrenheit** alongside **relative humidity** — updating every 2 seconds.

It demonstrates core IoT concepts: **digital sensor communication**, **single-wire protocol**, **library-based sensor interfacing**, and **real-time LCD output**.

---

## 🧠 Concept & Working Principle

The **DHT11** is a digital temperature and humidity sensor that uses a **single-wire serial protocol** to send calibrated data to the microcontroller. Unlike analog sensors, no ADC conversion is needed — the Arduino receives ready-to-use digital values via the `DHT` library.

### How DHT11 Works

```
┌──────────────┐   Single-Wire    ┌────────────────────┐   DHT library   ┌──────────────────┐
│  DHT11       │  Digital Signal  │   Arduino UNO      │ ──────────────► │  tempC, tempF,   │
│  Sensor      │ ───────────────► │   (Digital Pin)    │                 │  humidity values │
│  (Temp+Hum)  │                  │                    │                 └────────┬─────────┘
└──────────────┘                  └────────────────────┘                          │
                                                                                  ▼
                                                                       ┌──────────────────────┐
                                                                       │   16×2 LCD Display   │
                                                                       │  Row 0: T:25.0C 77F  │
                                                                       │  Row 1: H: 60%       │
                                                                       └──────────────────────┘
```

### DHT11 Sensor Specs

| Parameter | Value |
|-----------|-------|
| Interface | Single-wire digital |
| Temperature Range | 0°C to 50°C |
| Temperature Accuracy | ±2°C |
| Humidity Range | 20% to 90% RH |
| Humidity Accuracy | ±5% RH |
| Sampling Rate | 1 reading per second (use ≥ 2s delay) |
| Operating Voltage | 3.3V – 5V |

### LCD Display Output

```
┌─────────────────┐
│ T:25.0C  77.0F  │   ← Row 0: Temperature (°C and °F)
│ H: 60%          │   ← Row 1: Relative Humidity
└─────────────────┘
  (Updates every 2 seconds)
```

---

## 🔌 Circuit Diagram (Tinkercad Simulation)

> The circuit was first designed and tested on **Tinkercad Circuits** before physical implementation.

<div align="center">

![Tinkercad Simulation](lcd_temp/tinkerW.png)

*Fig 1 — Tinkercad simulation: Arduino UNO + DHT11 sensor + 16×2 LCD on breadboard*

</div>

### 📍 Pin Configuration

| Arduino Pin | Connected To | Purpose |
|:-----------:|:------------:|:--------|
| `7` | DHT11 Data | Digital temperature & humidity data |
| `5V` | DHT11 VCC | Sensor power supply |
| `GND` | DHT11 GND | Sensor ground |
| `12` | LCD RS | Register Select |
| `11` | LCD E | Enable |
| `5` | LCD D4 | Data bit 4 |
| `4` | LCD D5 | Data bit 5 |
| `3` | LCD D6 | Data bit 6 |
| `2` | LCD D7 | Data bit 7 |
| `5V` | LCD VCC | LCD power supply |
| `GND` | LCD GND | LCD ground |
| `10kΩ pot` | LCD V0 | LCD contrast control |

> **Note:** A **10kΩ pull-up resistor** between DHT11 Data pin and 5V is recommended for stable readings.

---

## 🛠️ Hardware Setup

> Physical build using **Arduino UNO**, **DHT11 sensor**, **16×2 LCD**, **10kΩ potentiometer**, and a **breadboard**.

<div align="center">

![Hardware Setup](lcd_temp/hw.png)

*Fig 2 — Physical hardware: Arduino UNO with DHT11 temperature & humidity sensor and 16×2 LCD display*

</div>

---

## 🧰 Components Required

| Component | Qty | Purpose |
|-----------|:---:|---------|
| Arduino UNO | 1 | Main microcontroller |
| DHT11 Sensor | 1 | Digital temperature & humidity sensing |
| 16×2 LCD Display | 1 | Display temperature and humidity |
| 10kΩ Potentiometer | 1 | LCD contrast adjustment |
| 10kΩ Resistor | 1 | Pull-up for DHT11 data line |
| Breadboard | 1 | Prototyping base |
| Jumper Wires | ~20 | Connections |
| USB Type-B Cable | 1 | Power + code upload |

---

## 💻 Arduino Code

> Requires the **DHT sensor library** by Adafruit. Install via Arduino IDE: `Sketch → Include Library → Manage Libraries → search "DHT sensor library"`.

```cpp
#include <LiquidCrystal.h>
#include <DHT.h>

// LCD pin mapping: RS, E, D4, D5, D6, D7
LiquidCrystal lcd(12, 11, 5, 4, 3, 2);

#define DHTPIN  7         // DHT11 data pin connected to digital pin 7
#define DHTTYPE DHT11     // Sensor type: DHT11

DHT dht(DHTPIN, DHTTYPE); // Initialize DHT sensor object

void setup() {
  lcd.begin(16, 2);           // Initialize 16-column, 2-row LCD
  dht.begin();                // Start DHT11 sensor
  lcd.print("Temp & Humidity"); // Splash screen
  delay(2000);
  lcd.clear();
}

void loop() {
  float humidity = dht.readHumidity();         // Read humidity (%)
  float tempC    = dht.readTemperature();      // Read temperature in Celsius
  float tempF    = dht.readTemperature(true);  // Read temperature in Fahrenheit

  // Check for failed readings
  if (isnan(humidity) || isnan(tempC) || isnan(tempF)) {
    lcd.setCursor(0, 0);
    lcd.print("Sensor Error!   ");
    lcd.setCursor(0, 1);
    lcd.print("Check wiring    ");
    delay(2000);
    return;
  }

  // Row 0: Temperature (Celsius and Fahrenheit)
  lcd.setCursor(0, 0);
  lcd.print("T:");
  lcd.print(tempC, 1);        // 1 decimal place
  lcd.print((char)223);       // Degree ° symbol (ASCII 223)
  lcd.print("C ");
  lcd.print(tempF, 1);
  lcd.print((char)223);
  lcd.print("F");

  // Row 1: Humidity
  lcd.setCursor(0, 1);
  lcd.print("H: ");
  lcd.print(humidity, 1);     // 1 decimal place
  lcd.print("%       ");      // Trailing spaces clear old digits

  delay(2000);                // DHT11 needs minimum 2s between reads
}
```

---

## 🚀 How to Run

### Option A — Simulate on Tinkercad *(No hardware needed)*

1. Go to [tinkercad.com](https://www.tinkercad.com) → **Circuits** → **Create new Circuit**
2. Add **Arduino UNO** + **DHT11 sensor** + **16×2 LCD** + **10kΩ potentiometer**
3. Wire per the pin configuration table above; add 10kΩ pull-up on DHT11 data pin
4. Click **Code** → switch to **Text** mode → paste the Arduino code
5. Click **Start Simulation** → adjust potentiometer for LCD contrast
6. Click on DHT11 in simulation → adjust temperature/humidity sliders to see live readings

### Option B — Upload to Physical Arduino

1. Install [Arduino IDE](https://www.arduino.cc/en/software)
2. Install DHT library: **Sketch → Include Library → Manage Libraries** → search `DHT sensor library` by Adafruit → Install (also install `Adafruit Unified Sensor` when prompted)
3. Build the circuit on breadboard per pin config table
4. Connect Arduino UNO via USB
5. Open Arduino IDE → paste the code
6. **Tools → Board:** `Arduino UNO` | **Tools → Port:** select correct COM/tty port
7. Click **Upload** (`Ctrl+U`)
8. LCD shows splash "Temp & Humidity" for 2 seconds, then live readings begin

---

## 📊 DHT11 Reading Reference

| Condition | Temp (°C) | Temp (°F) | Humidity (%) |
|-----------|:---------:|:---------:|:------------:|
| Cold room | 15.0 | 59.0 | 40% |
| Room temperature | 25.0 | 77.0 | 55% |
| Warm room | 35.0 | 95.0 | 70% |
| Hot & humid | 45.0 | 113.0 | 85% |

> **DHT11 vs DHT22:** DHT11 is lower cost with ±2°C accuracy. DHT22 offers wider range (−40°C to +80°C) and ±0.5°C accuracy — a drop-in upgrade for this project.

---

## 🔍 Limitations & Future Scope

**Current Limitations**
- DHT11 limited to 0°C – 50°C range with ±2°C accuracy
- No data logging — readings are display-only
- Minimum 2-second sampling interval (sensor hardware constraint)
- No threshold-based alert or alarm

**Future Enhancements**
- 🔔 **Buzzer alert** — trigger when temperature or humidity exceeds threshold
- 📊 **Serial plotter** — visualize trends via Arduino Serial Monitor
- 💾 **SD card module** — log timestamped readings locally
- 📡 **ESP8266/ESP32** — push data to cloud (ThingSpeak, Blynk) for remote monitoring
- 🌡️ **DHT22 upgrade** — wider range and higher accuracy drop-in replacement

---

## 👤 Author

**Nandakishor** — Electronics & Communication Engineering  
IIIT Kottayam | VLSI & Embedded Systems

[![GitHub](https://img.shields.io/badge/GitHub-Nandhakish0r-181717?style=flat-square&logo=github&logoColor=white)](https://github.com/Nandhakish0r)
[![Repo](https://img.shields.io/badge/Repo-Internet__of__Things--projects-blue?style=flat-square&logo=github)](https://github.com/Nandhakish0r/Internet_of_Things-projects)

---

<div align="center">

*Built with Arduino UNO · Part of the IoT Projects series*

</div>
