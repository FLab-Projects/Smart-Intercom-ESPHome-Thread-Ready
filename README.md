# 🏠 Smart Intercom with ESP32‑C6 and Thread

[![ESPHome](https://img.shields.io/badge/ESPHome-2024.6%2B-blue?logo=esphome)](https://esphome.io)
[![Thread](https://img.shields.io/badge/Thread-FTD-0099FF?logo=thread)](https://openthread.io)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)

An **ESPHome** project that turns a regular analog intercom into a **Home Assistant**‑controlled smart device.

[🇷🇺 Русская версия](README_ru.md)

## ✨ Features

- **Incoming call detection** via an optocoupler (GPIO3 with pull‑up).
- **Two switching optocouplers**:
  - «Standby» mode (GPIO1) – simulates the handset resting on the hook.
  - «Answer / Door open» (GPIO4) – simulates picking up the handset and pressing the door‑open button.

- **Four operation modes** selectable from Home Assistant:
  - `Off` – fully manual control.
  - `Once` – answer and open the door on the next call, then switch to `Off`.
  - `Always open` – automatically answer and open the door on every call.
  - `Reject` – always drop the incoming call.
- **Manual buttons** in the HA interface to open the door or reject a call.
- **Adjustable timing parameters** for different intercom models.
- **Thread support** (OpenThread) and IPv6 – ready for Matter.
- **OTA updates** and encrypted API.
---
## 📦 Hardware Requirements

| Component                | Purpose                                                                      |
|--------------------------|------------------------------------------------------------------------------|
| ESP32‑C6 DevKitC‑1       | Wi‑Fi 6, Bluetooth 5, Thread controller.                                     |
| KAQY212S optocoupler     | Solid‑state relays (normally open) for galvanic isolation.                   |
| 220Ω resistors           | Current limiting for the optocoupler LEDs (depends on GPIO voltage).         |
| 5 V power supply         | Powers the ESP32‑C6.                                                         |

> 💡 **Why KAQY212S?**  
> This chip contains a bidirectional MOSFET switch, rated up to **60 V / 400 mA**, making it perfect for analog intercom signals (both AC and DC). Full galvanic isolation eliminates noise and protects the ESP.

---

## 🔌 Wiring Diagram

The ESPHome configuration uses the following GPIO pins (customizable in the `substitutions` section):

| ESP32‑C6 GPIO | Function                          | Connection                                                                                                        |
|---------------|-----------------------------------|-------------------------------------------------------------------------------------------------------------------|
| **GPIO1**     | «Standby» optocoupler control     | LED anode → resistor (220–470 Ω) → GPIO1, LED cathode → GND. Output pins (4 & 6) in parallel with the handset hook. |
| **GPIO3**     | Call detection input              | Optocoupler output pins (4 & 6) in parallel with the call buzzer. The optocoupler LED is driven by the call signal through a resistor. GPIO3 has internal `INPUT_PULLUP`. |
| **GPIO4**     | «Answer» optocoupler control      | LED anode → resistor → GPIO4, LED cathode → GND. Output pins (4 & 6) in parallel with the door‑open button / answer contact. |

### Output Logic

- **«Standby» optocoupler (GPIO1)** – Normally **ON** (`output.turn_on`), its contacts are closed (handset on hook). It briefly turns OFF during answer or reject.
- **«Answer» optocoupler (GPIO4)** – Normally **OFF**. Turns ON to simulate lifting the handset or pressing the door‑open button.

### Call Detection

The third optocoupler is wired across the call buzzer line. When a call signal (AC or DC voltage) appears, the LED lights up, closing the output contacts and pulling GPIO3 to GND. The firmware uses `inverted: True`, so a closed contact is treated as a **press** (`on_press`).

---

## 🏡 Home Assistant Integration

The following entities will be created in HA:

- **Select «Mode»** – switch between automation modes.
- **Binary Sensor «Incoming call»** – indicates an active call.
- **Buttons «Open door»** and **Reject»** – manual triggers.

All controls are available on the ESPHome device card.

---

## ⚙️ Timing Parameters

Adjust the values in the `substitutions` section to match your intercom:

| Parameter                | Default     | Description                                                                                 |
|--------------------------|-------------|---------------------------------------------------------------------------------------------|
| `call_end_detect_delay`  | `3000ms`    | Minimum call pulse duration for reliable detection.                                          |
| `before_answer_delay`    | `10ms`      | Delay before starting the answer sequence (allows line stabilization).                       |
| `answer_on_time`         | `1500ms`    | Duration the handset is «lifted».                                                            |
| `open_on_time`           | `600ms`     | Duration of the door‑open button press.                                                      |
| `after_open_delay`       | `500ms`     | Extra pause after opening before returning to standby.                                       |

---

## 🐞 Troubleshooting

- **Call not detected**:
  - Ensure `inverted: True` is set for GPIO3 (active low).
- **Door does not open**:
  - Increase `open_on_time` (e.g., to `800ms`).
  - Check with a multimeter that the «Answer» optocoupler actually closes the button contacts.
- **Modes switch but automation does not trigger**:
  - Enable ESPHome logs and confirm that `incoming_call` changes to `on` during a call.
  - Verify the selected mode matches the expected behavior (especially «Once»).

---

## 🧵 Thread & IPv6

The device is configured as a **Full Thread Device** (FTD) and will attempt to join an existing Thread network using the TLV from `secrets.yaml`.

---

### 📸 Screenshots

![Dashboard](main.png)
![Dashboard](modes.png)

## 📄 License

MIT © 2026 [F-Lab]
Made with ❤️ by f1x6r
