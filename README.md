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
