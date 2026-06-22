# 💓 Live Pulse Detector

> A portable, real-time heart rate monitoring system with GSM-based emergency SMS alerts — built with Arduino, SIM800L, and a custom PCB.

---

## 🧠 What It Does

The **Live Pulse Detector** is a standalone embedded system that:

- Reads heart rate in real time using an analog pulse sensor
- Displays BPM live on a **16×2 LCD display**
- **Triggers a buzzer** when abnormal pulse rates are detected (too high or too low)
- **Sends an SMS alert** via GPRS (SIM800L module) to a caregiver's number
- Suggests emergency medication on-screen (Propranolol for high pulse, Atropine for low pulse)
- Operates on dual **3.7V Li-ion batteries** — fully portable, no wall power needed

---

## 🔧 Hardware Components

| Component | Description |
|---|---|
| **Microcontroller** | Arduino Nano (ATmega328P) |
| **Pulse Sensor** | Analog heartbeat sensor (pin A0) |
| **GSM Module** | SIM800L (SoftwareSerial on pins 2, 3) |
| **Display** | 16×2 LCD with I2C backpack (address `0x27`) |
| **Buzzer** | Active buzzer on pin 4 |
| **Safety Switch** | Digital input switch on pin 5 |
| **Power** | 2× INR18650 Li-ion cells (3.7V each) |
| **DC Converters** | Step-up/step-down modules: 4V (for GSM), 5V (for logic) |
| **Antenna** | GSM antenna for SIM800L |
| **PCB** | Custom perf board layout |

---

## ⚙️ System Architecture

```
[Pulse Sensor] ──analog──► [Arduino Nano]
                                │
              ┌─────────────────┼──────────────────┐
              ▼                 ▼                   ▼
         [16x2 LCD]         [Buzzer]          [SIM800L]
        (BPM display)    (alert sound)     (SMS to caregiver)
              
[Battery Pack] → [DC 5V Converter] → Logic
              → [DC 4V Converter] → GSM Module
```

---

## 📟 How It Works — Algorithm

```
1. Read analog value from pulse sensor every 20ms
2. If value > threshold (515):
      → Mark heartbeat peak detected
      → Calculate time between two consecutive peaks
      → BPM = 60000 / timeDifference
3. Validate: 10 < BPM < 200 (filter noise)
4. If safety switch is ON:
      → Display BPM on LCD
      → If BPM > 100 (High Pulse):
            safetyCount++
            If safetyCount == 3: send SMS + buzz + show "Take Propranolol"
      → If BPM < 50 (Low Pulse):
            safetyCount++
            If safetyCount == 3: send SMS + buzz + show "Take Atropine"
      → Else: display "Normal Rate", reset safetyCount
5. If safety switch is OFF:
      → Display raw BPM without alert logic
```

---

## 📁 Project Structure

```
live-pulse-detector/
│
├── src/
│   └── pulse_detector.ino      # Main Arduino sketch
│
├── hardware/
│   └── pulse_detector.jpg      # Photo of assembled hardware
│
├── docs/
│   └── CODE_OF_LIVE_PULSE_DETECTOR.pdf   # Original source reference
│
└── README.md
```

---

## 🚀 Getting Started

### Prerequisites

- [Arduino IDE](https://www.arduino.cc/en/software) (v1.8+ or v2.x)
- Libraries (install via Library Manager):
  - `LiquidCrystal_I2C` by Frank de Brabander
  - `Wire` (built-in)
  - `SoftwareSerial` (built-in)

### Setup

1. **Clone this repository**
   ```bash
   git clone https://github.com/kaursunainapreet-lgtm/live-pulse-detector.git
   cd live-pulse-detector
   ```

2. **Open the sketch** in Arduino IDE:
   ```
   src/pulse_detector.ino
   ```

3. **Update the SMS recipient number** in `sendSMS()`:
   ```cpp
   Sim800L.println("AT+CMGS=\"+91XXXXXXXXXX\"");
   ```

4. **Install libraries** via Arduino IDE → Sketch → Include Library → Manage Libraries

5. **Upload** to your Arduino Nano (Board: `Arduino Nano`, Processor: `ATmega328P`)

---

## 📊 Pulse Classification

| BPM Range | Status | System Response |
|---|---|---|
| < 50 | 🔴 Low (Bradycardia) | Buzzer + SMS + "Take Atropine" |
| 50 – 100 | 🟢 Normal | LCD shows "Normal Rate" |
| > 100 | 🟠 High (Tachycardia) | Buzzer + SMS + "Take Propranolol" |

> ⚠️ **Medical Disclaimer:** Medication suggestions (Atropine, Propranolol) are displayed for demonstration/academic purposes only. This device is **not a certified medical device** and should not be used as a substitute for professional medical advice.

---

## 📡 SMS Alert Format

**High Pulse:**
```
Patient is having [BPM] pulse rate!!. Please check on the patient
immediately and seek medical advice if the condition continues.
```

**Low Pulse:**
```
Patient is having [BPM] pulse rate!!. Immediate attention is advised.
Please check the patient and consult a healthcare professional.
```

---

## 🔌 Pin Configuration

| Pin | Connected To |
|---|---|
| `A0` | Pulse Sensor (analog) |
| `2` | SIM800L TX |
| `3` | SIM800L RX |
| `4` | Buzzer |
| `5` | Safety Input Switch |
| `13` | LED indicator |
| `SDA/SCL` | LCD I2C |

---

## 🛠️ Known Issues / Future Improvements

- [ ] Add data logging to SD card for pulse history
- [ ] Replace SIM800L with ESP32 for Wi-Fi/cloud dashboard
- [ ] Calibrate threshold dynamically per user
- [ ] Add OLED display for better readability
- [ ] Package in a wearable enclosure
- [ ] Add low battery warning

---

## 👩‍💻 Author

**Sunainapreet Kaur**  
Final Year AI and Data Science Student | AI & Embedded Systems Enthusiast  
📍 Punjab, India  
🔗 [GitHub](https://github.com/kaursunainapreet-lgtm)

---

## 📄 License

This project is open-source under the [MIT License](LICENSE).

---

> *Built as part of my hardware + embedded systems portfolio. Combines real-time signal processing, GSM communication, and IoT alerting in a compact portable form.*
