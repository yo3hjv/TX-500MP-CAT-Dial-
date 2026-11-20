# CAT Dial - LAB599 TX-500MP & Multi-Transceiver Controller
### High-Precision Autonomous Physical VFO & CAT Interface for M5Stack Dial

**Firmware Revision:** `0.6.0`  
**Author:** Adrian (All rights reserved)  
**Document Date:** 19.11.2026 

---
# NOTE: V.0.6.0 for beta testing with LINK500MP on Physical Serial connection.

Do not connect PORT A directly to 5V I/O, only to 3V3!

**To use it with wired connection to Lab599 TX-500 series transceivers, the CAT Dial must provide 3V3 TO THE RADIO!!!**

The binary must be uploaded (flashed)  on M5 Dial V.1.1 from address 0x0000 


---
## 1. Scope of the Project

The **CAT Dial** project provides an autonomous, high-precision physical VFO dial and CAT control console for amateur radio transceivers, implemented on the compact **M5Stack Dial v1.1** smart rotary device. 

It delivers an authentic analog tuning experience with mechanical detents, rich visual feedback powered by LVGL 9, and dynamic profile-driven adaptation to the exact capabilities of multiple radio transceivers:
* **Lab599 Discovery TX-500 / TX-500MP** (Strict protocol compliance, ASCII)
* **Icom IC-7100, IC-7000, IC-703** (Bidirectional half-duplex binary CI-V bus)
* **Kenwood TS-2000** (Standard Kenwood ASCII syntax)

---

## 2. Hardware Architecture

* **Host Platform:** M5Stack Dial v1.1
* **Microcontroller:** Espressif ESP32-S3 (Dual-Core Xtensa @ 240 MHz, Wi-Fi & BLE 5.0)
* **Display:** 1.28-inch circular IPS TFT LCD (240x240 pixels, GC9A01 driver, RGB565)
* **Touch Panel:** Capacitive multi-touch FT3267 controller
* **Rotary Encoder:** Optical/magnetic rotary ring with tactile detents (GPIO 40 / GPIO 41)
* **Physical Push Button:** Central under-screen tactile push button (BtnA / GPIO 42)
* **Audio Transducer:** On-board acoustic buzzer / speaker with NS4168 I2S power amplifier (GPIO 3)
* **Hardware CAT Interface:** Port A (Grove Red socket: RX = GPIO 13, TX = GPIO 15) supporting both full-duplex UART and single-wire half-duplex CI-V with echo suppression
* **Wireless CAT Interface:** Low-overhead Bluetooth Low Energy (NimBLE-Arduino)
* **Power Management:** Internal rechargeable Li-Ion battery with USB-C charging; zero-standby hardware latch power cutoff ($0\ \mu\text{A}$) on GPIO 46 (`POWER_HOLD`), plus USB Light Sleep with button wakeup

---

## 3. Main Features

* **Dual VFO Architecture (VFO A / VFO B):** Independent operating frequencies, modulation modes, and active decade steps maintained in distinct memory blocks.
* **Clarifier / RIT / XIT:** Fine offset adjustment ($\pm 9.99\text{ kHz}$) with rapid 2-second hold-to-zero reset (`CLARIFIER_RESET_HOLD_MS = 2000`).
* **6-Screen Touch Carousel:** Navigable via horizontal swipe gestures:
  * **Frequency (Home Screen)**
  * **Audio Gain (0..100% Volume, 0.6s hold for Mute)**
  * **RF Gain (Profile-adapted scales, tap toggles ATT / PRE)**
  * **TX RF Power (0.5..10.0W decimal for 10W rigs, 0..100W integer for 100W rigs)**
  * **Mic Gain (0..100%)**
  * **Speech Compressor (Level & ON/OFF toggle)**
* **11 IARU Region 1 Amateur Bands:** 160m through 6m (including 60m WRC-15) with mid-band calling defaults and automatic modulation selection (LSB below 10 MHz, USB for 10 MHz and above).
* **Frequency Limit Modes:** Strict amateur band boundaries or full general coverage (0 to 55 MHz) with configurable wrap-around or clamp behaviors.
* **Dual CAT Connectivity:** Simultaneous or selectable physical serial CAT (Port A UART) and wireless Bluetooth BLE CAT.
* **Multi-Vendor Transparent BLE Support (NimBLE):**
  * **JDY-33 / HM-10:** Standard transparent UART service (`0xFFE0` / `0xFFE1`).
  * **Nordic UART Service (NUS):** UUID `6E400001-B5A3-F393-E0A9-E50E24DCCA9E`.
  * **Microchip RN4678 / ISSC:** Transparent UART UUID `49535343-FE7D-4AE5-8FA9-9FAFD205E455` for native, unauthenticated ("Just Works") wireless connection to the **Lab599 Link-500 MP** interface.
* **Deterministic Boot Discovery (`dial_update`):** Transactional Stop-and-Wait query engine with bitmask validation that interrogates the radio state at boot and reconciles NVS settings without noisy background polling loops.
* **Real-Time Connection Status Indicator:** Colored bar beneath the radio model readout:
  * 🟢 **Green:** Physical wired Serial CAT (Port A)
  * 🔵 **Navy Blue:** Wireless Bluetooth BLE
* **Intelligent Encoder Ballistics:** Dynamic acceleration curves (1x, 3x, 10x) allowing fine 1 Hz resolution on slow tuning and rapid band traversal on quick rotations.
* **Safety Multi-Click Power Off:** 5 rapid clicks within 500 ms trigger an 8-second countdown screen with warning beeps on the final 3 seconds.
* **Single Merged Binary Deployment:** Automated batch script (`make_merged_bin.bat`) assembling bootloader, partition table, and compiled firmware into `CAT_Dial_FULL_MERGED.bin` (8MB, DIO, 80MHz) flashable at single offset `0x0000`.

---

## 4. Operating Instructions (Simplified User Manual)

### 4.1 Operating Screens Carousel (Normal Mode)
Swipe horizontally across the touch display (left/right) to switch screens:

* **Frequency Screen (Home Screen, Order 0):**
  * **Rotary Dial:** Tunes active VFO frequency by the selected decade step.
  * **Arrow Buttons (`<` / `>`):** Shift active decade step left or right (1 Hz up to 10 MHz). An illuminated LED indicates the active decade. *(Note: Frequency digits are display readouts only and are non-clickable).*
  * **VFO Label:** Tap switches between VFO A and VFO B.
  * **Mode Label:** Tap cycles operating modulation: `LSB` $\rightarrow$ `USB` $\rightarrow$ `CW` $\rightarrow$ `AM` $\rightarrow$ `FM` $\rightarrow$ `DIG`.
  * **B-UP / B-DN:** Jumps to the adjacent amateur band default frequency and assigns standard LSB/USB modulation.
  * **SCAN- / SCAN+:** Starts/stops continuous frequency scanning.
  * **Clarifier Area:** Tap (0.2s) activates Clarifier / RIT; hold for 2 seconds resets offset to 0 Hz.
  * **Status Bar:** Shows Green (Cable) or Navy Blue (Bluetooth).

* **Auxiliary Parameter Screens (Swipe Left / Right):**
  * **Audio Gain (Order +1):** Dial adjusts volume (0..100%); 0.6s touch hold mutes audio.
  * **RF Gain (Order +2):** Dial adjusts receiver gain; short tap toggles ATT / PRE / NORMAL.
  * **TX RF Power (Order -1):** Dial adjusts transmit wattage (0.5..10W or 0..100W).
  * **Mic Gain (Order -2):** Dial adjusts microphone preamp level (0..100%).
  * **Speech Compressor (Order -3):** Dial adjusts compression level; short tap toggles ON/OFF.

### 4.2 DSP Settings Menu
* **Entry:** Press and hold the physical central button for **EXACTLY 1 SECOND** (release at the first beep).
* **Controls:** 
  * `FILTER`: Cycles IF filter bandwidth (NAR, MID, WIDE).
  * `ATT / PRE`: Cycles front-end RF mode (NORMAL, PRE, ATT).
  * `AGC`: Cycles AGC speeds (SLOW, MID, FAST, OFF).
  * `NB` / `NR`: Toggles Noise Blanker / Noise Reduction ON/OFF; hold opens fine-tuning subpages (1..100).
  * `AUTO-NOTCH`: Toggles automatic heterodyne notch filter.
* **Exit:** Press and hold the physical button for 1 second.

### 4.3 System Setup Menu
* **Entry:** Press and hold the physical central button for **EXACTLY 5 SECONDS** (emits countdown beeps on seconds 1..4, long tone at second 5).
* **Navigation:** Rotate dial to select parameter subpage:
  * **TRX Model:** Select active transceiver model (auto-saved after 0.5s pause).
  * **CAT Baud Rate:** Select serial rate (4800 to 115200 bps; default 9600 for TX-500, 19200 for IC-7100).
  * **LIGHT & BEEP:** Real-time sliders for LCD brightness and audio feedback volume.
  * **BLUETOOTH:** 10-second scan, discovery list, and one-tap connection (JDY-33, HM-10, Lab599 Link-500 MP / RN4678).
  * **BT DISCONNECT:** Instant one-tap disconnect for active wireless links.
* **Exit:** Press and hold the physical button for 5 seconds (or 1 second).

### 4.4 Power Off Sequence
* **Trigger:** 5 rapid presses on the physical button within 500 ms.
* **Countdown:** 8-second visual countdown with warning beeps during the final 3 seconds.
* **Execution:** Tap the red **"POWER OFF"** button to commit state to NVS and shut down completely.
* **Cancel:** Leave untouched for 8 seconds to automatically abort and return.

---

## 5. Compilation & Deployment Options


---

## 6. Project Modules & Drivers

---

## 7. Software Architecture (Orchestrator Pattern)



---

## 8. Features to be Added

* Dedicated Antenna Tuner (ATU) page: ATU ON, OFF, and AutoTune trigger upon frequency changes.
* Additional transceiver profiles (Elecraft KX2/KX3, Yaesu FT-817/818/891).

---

## 9. Author & License

* **Author:** Adrian
* **Rights:** All rights reserved.

---

## 10. Document Revision History

* **Revision:** `0.6.0`
* **Date:** 19.11.2026 / 08.00
