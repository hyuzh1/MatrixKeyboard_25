# MatrixKeyboard_25

[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)

> **Status:** Work in Progress (WIP) – Hardware v1.0 is functional but far from perfect.  
> The BOM cost is high, and several features are still broken. But hey, the RGB works. (

---

## What?

MatrixKeyboard_25 is a 25-key (5x5 matrix) keyboard powered by a **CH58x MCU**. It's designed as a compact input device to experiment with 9-key Pinyin input, RGB lighting, and environmental sensing.

## Why?

I wanted to build a simple keyboard that could handle **9-key Pinyin input** – something between a full keyboard and a phone keypad. This project is my excuse to learn:

- Bluetooth & USB HID firmware development
- PCB design with matrix scanning
- RGB LED control (WS2812B)
- Sensor integration (temperature & humidity)

## How?

The project has four main functional areas:

### 1. Key Input
- 5x5 matrix scanned by a **TCA8418RTWR** (I²C keypad controller with hardware debouncing)
- Supports multi-key rollover (6KRO via USB HID)
- Hot-swappable MX-compatible switch sockets

### 2. RGB Lighting Effects
- **WS2812B** addressable LEDs (one per key, placed under the switch's light guide)
- Multiple modes: static, breathing, rainbow wave, chase
- **Known issue:** Level mismatch (3.3V MCU → 5V WS2812) – a level shifter was bodged in v1.0

### 3. Temperature & Humidity Display
- **HDC2010YPAR** sensor (I²C, 0–100% RH, -40 to 125°C)
- Data intended for a 0.91" OLED (128×32, I²C)
- **Known issue:** Software I²C + blocking display updates = broken key scanning. OLED is currently unused.

### 4. Brightness & Volume Control
- Analog potentiometer (ADC input) – planned for brightness/volume adjustment
- **Known issue:** CH583M's ADC performance is poor – DMA still blocks the main loop. Feature disabled in v1.0.

---

## Hardware

### Current BOM

| Component | Model | Interface | Notes |
|:---|:---|:---|:---|
| MCU | CH583M | – | Bluetooth 5.0 + USB 2.0, 60MHz, 3.3V |
| Key scanner | TCA8418RTWR | I²C | 80-key max, hardware debounce |
| Temp/Hum sensor | HDC2010YPAR | I²C | 0–100% RH, -40–125°C |
| Display | 0.91" OLED | I²C | 128×32, SSD1306-compatible |
| RGB LEDs | WS2812B | 1-wire | 5V, one per key |
| Power | AMS1117-3.3 | – | 5V USB → 3.3V LDO |
| ESD protection | USBLC6-2 | – | For USB D+/D- |

### Planned Additions (v2.0)
- Lithium battery + charging management (TP4056)
- WiFi module for tri-mode (may need MCU upgrade)
- Rotary encoder (instead of the cheap potentiometer)

---

## Mechanical Design

### v1.0
#### Feature
- 2-layer PCB (cost-effective, but a bit ugly)
- No enclosure – bare PCB with acrylic backplate
- 5×5 regular grid – keys like Space and Enter are comically small
- Flat keycaps (no ergonomics)
- MX-compatible hot-swap sockets
#### Appreciation


### Planned (v2.0)
- 4-layer PCB (better signal integrity)
- 3D-printed enclosure
- Non-standard matrix – wider Space & Enter keys
- Ergonomic keycaps + sculpted layout
- PCB cutouts for better flex/feel

---

## Software

### Toolchain
- **IDE:** MounRiver Studio (or Keil)
- **SDK:** Official CH58x SDK (C language)
- **Debug:** SWD (pads reserved on PCB), UART1 for `printf`

### Implemented Features
- ✅ USB HID keyboard (6KRO, standard boot protocol)
- ✅ TCA8418 matrix scanning with interrupt support
- ✅ WS2812B RGB control (rainbow, static, breathing)
- ✅ UART debug output
- ❌ Bluetooth (antenna design failed – no signal)
- ❌ OLED display (software I²C blocks the main loop)
- ❌ Temperature/Humidity display (sensor works, but no display)
- ❌ Volume/Brightness control (ADC blocks the main loop)

### Code Structure
