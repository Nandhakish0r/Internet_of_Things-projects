<div align="center">

# 🚦 2-Way Railway Crossing Traffic Signal Mechanism

### An Arduino-based Automatic Traffic Control System for Railway Level Crossings

![Arduino](https://img.shields.io/badge/Arduino-UNO-00979D?style=for-the-badge&logo=arduino&logoColor=white)
![Language](https://img.shields.io/badge/Language-C%2FC%2B%2B-blue?style=for-the-badge&logo=c%2B%2B&logoColor=white)
![Platform](https://img.shields.io/badge/Platform-Tinkercad-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=for-the-badge)

</div>

---

## 📌 Overview

This project simulates a **2-way Railway Level Crossing Traffic Signal** using an Arduino UNO. The system automatically controls road traffic signals at a railway crossing — stopping vehicles when a train is approaching and allowing them to pass after the train clears.

It models real-world railway gate logic using **LED traffic lights** controlled in a cyclic sequence, making it safe, predictable, and automatic.

---

## 🧠 Concept & Working Principle

At a **level crossing**, road vehicles and trains share the same intersection. The challenge is to:
- **Stop road traffic** when a train is approaching or passing
- **Allow road traffic** to move when the crossing is clear

### How It Works (State Machine Logic)

The system cycles through **4 states** with a 2-second delay between each:

```
State 1 → ROAD: RED ON    | TRAIN: GREEN ON   (Train passing, road blocked)
State 2 → ROAD: YELLOW ON | TRAIN: GREEN ON   (Train still moving, road ready to open)
State 3 → ROAD: GREEN ON  | TRAIN: RED ON     (Road open, no train)
State 4 → ROAD: YELLOW ON | TRAIN: RED ON     (Road about to close, prepare to stop)
   ↓
[Repeat from State 1]
```

> **No sensor is used** — the system runs on a timed loop to demonstrate signal cycling. In a real deployment, IR or proximity sensors would trigger state changes.

---

## 🔌 Circuit Diagram (Tinkercad Simulation)

> The circuit was designed and simulated on **Tinkercad Circuits** before physical implementation.

<div align="center">

<table>
  <tr>
    <td align="center"><img src="t1.png" width="280"/><br/><sub><b>Tinkercad View 1</b></sub></td>
    <td align="center"><img src="t2.png" width="280"/><br/><sub><b>Tinkercad View 2</b></sub></td>
    <td align="center"><img src="t3.png" width="280"/><br/><sub><b>Tinkercad View 3</b></sub></td>
  </tr>
</table>

*Fig 1: Tinkercad simulation — Arduino UNO with Traffic Light LEDs and Train Signal LEDs on breadboard*

</div>

### Pin Configuration

| Pin | Color | Signal | Role |
|-----|-------|--------|------|
| `11` | 🔴 Red | ROAD | Road Red Light |
| `12` | 🟡 Yellow | ROAD | Road Yellow Light |
| `10` | 🟢 Green | ROAD | Road Green Light |
| `9` | 🟢 Green | TRAIN | Train Green (Clear) |
| `8` | 🔴 Red | TRAIN | Train Red (Approaching) |

---

## 🛠️ Hardware Setup

> Physical implementation using Arduino UNO, LEDs, and resistors on a breadboard.

<div align="center">

<table>
  <tr>
    <td align="center"><img src="hw1.png" width="280"/><br/><sub><b>Hardware View 1</b></sub></td>
    <td align="center"><img src="hw2.png" width="280"/><br/><sub><b>Hardware View 2</b></sub></td>
    <td align="center"><img src="hw3.png" width="280"/><br/><sub><b>Hardware View 3</b></sub></td>
  </tr>
</table>

*Fig 2: Physical hardware setup — Arduino UNO with LED traffic signal circuit*

</div>

---

## 🧰 Components Required

| Component | Quantity | Purpose |
|-----------|----------|---------|
| Arduino UNO | 1 | Microcontroller / Brain |
| Red LED | 2 | Road Red + Train Red |
| Yellow LED | 1 | Road Yellow |
| Green LED | 2 | Road Green + Train Green |
| 220Ω Resistor | 5 | Current limiting for each LED |
| Breadboard | 1 | Circuit assembly |
| Jumper Wires | ~15 | Connections |
| USB Cable | 1 | Power + programming |

---

## 💻 Arduino Code

```cpp
void setup() {
  // Road Signal Pins
  pinMode(11, OUTPUT);  // RED
  pinMode(12, OUTPUT);  // YELLOW
  pinMode(10, OUTPUT);  // GREEN

  // Train Signal Pins
  pinMode(9, OUTPUT);   // GREEN
  pinMode(8, OUTPUT);   // RED
}

void loop() {
  // ── STATE 1: Train Passing | Road STOP ──────────────────
  // ROAD: RED ON
  digitalWrite(11, HIGH);  // RED   → ON
  digitalWrite(12, LOW);   // YELLOW → OFF
  digitalWrite(10, LOW);   // GREEN  → OFF
  // TRAIN: GREEN (Clear to go)
  digitalWrite(9, HIGH);   // GREEN → ON
  digitalWrite(8, LOW);    // RED   → OFF
  delay(2000);

  // ── STATE 2: Train Still Moving | Road WAIT ─────────────
  // ROAD: YELLOW ON
  digitalWrite(11, LOW);   // RED    → OFF
  digitalWrite(12, HIGH);  // YELLOW → ON
  digitalWrite(10, LOW);   // GREEN  → OFF
  // TRAIN: GREEN (still clear)
  digitalWrite(9, HIGH);   // GREEN → ON
  digitalWrite(8, LOW);    // RED   → OFF
  delay(2000);

  // ── STATE 3: Crossing Clear | Road GO ───────────────────
  // ROAD: GREEN ON
  digitalWrite(11, LOW);   // RED    → OFF
  digitalWrite(12, LOW);   // YELLOW → OFF
  digitalWrite(10, HIGH);  // GREEN  → ON
  // TRAIN: RED (no train)
  digitalWrite(9, LOW);    // GREEN → OFF
  digitalWrite(8, HIGH);   // RED   → ON
  delay(2000);

  // ── STATE 4: Road About to Close | WARN ─────────────────
  // ROAD: YELLOW ON
  digitalWrite(11, LOW);   // RED    → OFF
  digitalWrite(12, LOW);   // YELLOW → OFF (note: preparing to close)
  digitalWrite(10, HIGH);  // GREEN  → ON
  // TRAIN: RED (approaching)
  digitalWrite(9, LOW);    // GREEN → OFF
  digitalWrite(8, HIGH);   // RED   → ON
  delay(2000);
}
```

---

## 🚀 How to Run

### 1. Simulate on Tinkercad (No Hardware Needed)
1. Go to [tinkercad.com](https://www.tinkercad.com) → **Circuits**
2. Add **Arduino UNO** + **5 LEDs** + **5 resistors** on breadboard
3. Wire LEDs to pins `8, 9, 10, 11, 12` with 220Ω resistors to GND
4. Paste the code in the code editor
5. Click **"Start Simulation"**

### 2. Physical Hardware Upload
1. Install [Arduino IDE](https://www.arduino.cc/en/software)
2. Connect Arduino UNO via USB
3. Open Arduino IDE → paste the code
4. Select **Board:** `Arduino UNO` and correct **Port**
5. Click **Upload** (`Ctrl+U`)
6. Observe LEDs cycling through signal states

---

## 📊 Signal State Table

| State | Road Signal | Train Signal | Meaning | Duration |
|-------|------------|--------------|---------|----------|
| 1 | 🔴 RED | 🟢 GREEN | Train passing, road blocked | 2 sec |
| 2 | 🟡 YELLOW | 🟢 GREEN | Train clearing, road preparing | 2 sec |
| 3 | 🟢 GREEN | 🔴 RED | Crossing clear, road open | 2 sec |
| 4 | 🟢 GREEN | 🔴 RED | Road about to close again | 2 sec |

---

## 🔍 Limitations & Future Scope

**Current Limitations:**
- Timed loop only — no actual train detection
- Single crossing, no multi-gate coordination
- No buzzer/alarm for approaching train warning

**Future Enhancements:**
- Add **IR sensors** or **reed switches** to detect actual train presence
- Add **servo motor** to control physical crossing gate barrier
- Include **buzzer** for audio alert when train approaches
- **LCD display** showing "TRAIN APPROACHING" / "SAFE TO CROSS"
- IoT integration — **ESP8266/ESP32** for remote monitoring

---

## 👤 Author

**Nandakishor** — Electronics & Communication Engineering  
IIIT Kottayam | VLSI & Embedded Systems  
[![GitHub](https://img.shields.io/badge/GitHub-Nandhakish0r-181717?style=flat&logo=github)](https://github.com/Nandhakish0r/Internet_of_Things-projects)

---

<div align="center">

*Built with ❤️ using Arduino UNO*

</div>
