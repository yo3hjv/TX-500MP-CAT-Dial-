# CAT DIAL USER MANUAL
### Practical User Guide Focused on Your Operating Experience

* **Hardware Platform:** M5Stack Dial v1.1 (ESP32-S3)
* **Firmware Version:** `0.6.0`
* **Compatible Transceivers:** Lab599 TX-500 / TX-500MP, Icom IC-7100 / IC-7000 / IC-703, Kenwood TS-2000

---

## 1. Powering On, Startup Sequence & Transceiver Sync

### A. How to Power It On
* **Battery Operation:** Briefly press the physical power button located on the side of the M5Stack Dial enclosure.
* **External Power:** When connected via a USB-C cable or an external 12V/5V DC supply, the device powers on automatically as soon as DC voltage is applied.

### B. What You See at Startup (Welcome Screen)
During the first moments after power-on:
1. The round color display presents the welcome screen (**Splash Screen**) showing the **CAT DIAL** emblem and the active firmware version (e.g., `v0.6.0`).
2. The controller immediately engages the communication bus:
   * It executes a deterministic **Boot Discovery** sequence with the connected transceiver to retrieve live parameters (frequencies, modulation, filter bandwidths, RF power, gains, and DSP settings).
   * If the transceiver is offline or disconnected, CAT Dial automatically recalls the last known valid parameters from its onboard non-volatile flash memory (NVS).

### C. Transition to the Main Frequency Screen
As soon as the initial data is synchronized from the transceiver or loaded from NVS:
* A pleasant confirmation tone sounds from the internal speaker.
* The welcome screen fades out and automatically transitions to the **Main Frequency Screen (Home Screen)**.

On this home screen, you immediately see:
* **Transceiver Model & Connection Bar:** Your configured radio model at the top, underlined by a colored status bar (**Green** for wired serial CAT, **Navy Blue** for wireless Bluetooth BLE).
* **Active VFO:** `VFO A` or `VFO B` displayed in the top-left area.
* **Operating Frequency:** High-contrast frequency display (e.g., `14.195.000`).
* **Active Modulation:** Current mode in the top-right area (e.g., `USB`).
* **Active Tuning Decade Indicator:** An illuminated red LED point directly aligned with the currently selected decade step (1 Hz up to 10 MHz).

At this stage, CAT Dial is fully synchronized with your transceiver in real time.

---

## 2. Main Frequency Screen (Home Screen) Operations

```
                   +-----------------------+
                   |  IC-7100  /  TX-500   | <--- Radio Model
                   |  ===================  | <--- Status Bar (Green / Blue)
                   | VFO A             USB | <--- VFO & Modulation
                   |   14.195.000          | <--- Primary Frequency
                   |        *              | <--- Active Decade LED
                   | [<]      CLAR     [>] | <--- Step Arrows & Clarifier
                   | [B-DN] [SCAN]  [B-UP] | <--- Band & Scan Buttons
                   +-----------------------+
```

### A. Switching Between VFO A and VFO B
* **Location:** Upper-left corner of the display (`VFO A` / `VFO B`).
* **Action:** Briefly tap (0.2-second touch) the VFO label.
* **Feedback:** A short audio beep sounds, the label toggles to the alternate VFO, and the transceiver instantly switches its active frequency, mode, and tuning step to match the selected VFO.
* **Independent Memory:** Each VFO maintains its own independent frequency, modulation, and tuning step.
> **Note:** A red dot to the left of the frequency rows indicates which specific row is currently active for rotary encoder tuning.

### B. Changing Modulation (LSB, USB, CW, AM, FM, DIG)
* **Location:** Upper-right corner of the display (e.g., `USB`).
* **Action:** Briefly tap the mode label.
* **Feedback:** Each short tap cycles through supported modes in sequence:
  $$\text{LSB} \longrightarrow \text{USB} \longrightarrow \text{CW} \longrightarrow \text{AM} \longrightarrow \text{FM} \longrightarrow \text{DIG} \longrightarrow (\text{back to LSB})$$
  The radio immediately acknowledges the new modulation mode accompanied by a short confirmation beep.

### C. Frequency Tuning (The Rotary Dial)
* **Action:** Turn the large outer aluminum rotary ring.
  * **Slow Rotation:** Adjusts frequency notch-by-notch with precision corresponding to the selected decade step.
  * **Rapid Rotation:** Dynamic intelligent ballistics automatically engage medium ($3\times$) or fast ($10\times$) acceleration, enabling swift traversal across entire amateur bands in just two or three quick wrist turns.

### D. Changing the Tuning Step (Active Decade)
* **Visual Cue:** The active tuning step is indicated by an illuminated LED directly beneath the active digit (1 Hz, 10 Hz, 100 Hz, 1 kHz, 10 kHz, 100 kHz, 1 MHz, or 10 MHz).
* **How to Change:** Decade selection is performed **EXCLUSIVELY** via the tactile on-screen arrow buttons:
  * **Left Arrow (`<`):** Moves the active decade to the left towards coarser steps (e.g., $100\text{ Hz} \rightarrow 1\text{ kHz} \rightarrow 10\text{ kHz}$).
  * **Right Arrow (`>`):** Moves the active decade to the right towards finer steps (e.g., $10\text{ kHz} \rightarrow 1\text{ kHz} \rightarrow 100\text{ Hz}$).
* Each tap emits a crisp beep, and the LED instantly moves under the new active decade.
> **Important:** The frequency digits themselves are high-contrast display readouts only; they do not respond to direct touch tapping.

### E. Changing Amateur Radio Bands (B-UP / B-DN)
* **Location:** Lower touch area buttons labeled **B-UP** (Band Up) and **B-DN** (Band Down).
* **Action:** Tap `B-UP` to jump to the next higher amateur band (e.g., 40m $\rightarrow$ 30m $\rightarrow$ 20m $\rightarrow$ 17m). Tap `B-DN` to descend to the lower band.
* **Automatic Radio Configuration:**
  * Jumps straight to the standard calling frequency of the target amateur band.
  * Automatically sets the correct standard modulation: **LSB** for bands below 10 MHz (160m, 80m, 40m) and **USB** for bands above 10 MHz (including 60m / 5 MHz).

### F. Adjusting and Resetting the Clarifier / RIT (Receiver Incremental Tuning)
* **Location:** Central lower area of the display.
* **Clarifier Activation / Split:**
  * Short tap (0.2s) activates Clarifier / RIT mode.
  * Long tap (1.0s) activates SPLIT operation mode.
* **Tuning the Offset:** When active, rotate the dial to adjust receiver offset within $\pm 9.99\text{ kHz}$.
* **Quick Zero Reset (0 Hz):** Press and hold the Clarifier area for **EXACTLY 2 SECONDS**. A confirmation tone sounds, and the offset instantly resets to `0.00 kHz`.

### G. Automatic Band Scanning (SCAN)
* **Action:** Tap `SCAN+` or `SCAN-` to initiate continuous upward or downward frequency scanning using the active decade step.
* **To Stop:** Slightly turn the rotary dial or tap anywhere on the screen to instantly halt scanning.

---

## 3. Connection Indicator: Wired Cable vs. Bluetooth Wireless

Directly beneath the radio model label at the top of the display is an active status bar:

| Indicator Bar Color | Active Connection Mode | Physical Interface |
| :--- | :--- | :--- |
| 🟢 **Green** | **Wired Serial CAT** | Physical cable plugged into Port A (Grove Red: GPIO13/15) |
| 🔵 **Navy Blue** | **Wireless Bluetooth BLE** | Wireless CAT link via JDY-33, HM-10, or Lab599 Link-500 MP (RN4678) |

---

## 4. Parameter Adjustment Screens (Horizontal Swipe Carousel)

Swipe horizontally across the touch display from the home screen to access dedicated parameter adjustment pages. Rotate the physical dial to fine-tune the selected parameter:

```
[Compressor] <-- [Mic Gain] <-- [TX Power] <== [FREQUENCY] ==> [Audio Gain] --> [RF Gain]
  (Swipe Right)   (Swipe Right)  (Swipe Right)    (HOME)       (Swipe Left)   (Swipe Left)
```

### Swipe Left (Audio & Receiver Gain)
1. **AUDIO GAIN (Volume):**
   * Rotate dial to set speaker/headphone volume ($0\% \dots 100\%$).
   * Press and hold the screen for **0.6 seconds** to toggle **AUDIO MUTE**.
2. **RF GAIN:**
   * Rotate dial to adjust front-end sensitivity and noise threshold.
   * Briefly tap the screen to toggle front-end states (**ATT / PRE / NORMAL**).

### Swipe Right (Transmitter Controls)
1. **TX RF POWER:**
   * Rotate dial to adjust output wattage.
   * Scaled automatically to your radio: fine 0.5W steps for 10W QRP transceivers (Lab599 TX-500) or 1W steps for 100W rigs (IC-7100 / TS-2000).
2. **MIC GAIN:**
   * Rotate dial to adjust microphone preamplifier gain ($0\% \dots 100\%$).
3. **SPEECH COMPRESSOR:**
   * Briefly tap the screen to toggle the speech processor **ON / OFF**.
   * Rotate dial to set compression depth according to radio scale.

*To return to the main screen, simply swipe in the opposite direction.*

---

## 5. DSP & Receiver Filters Menu

Access digital signal processing controls directly on transceiver models that support DSP features.

* **How to Enter:** Press and hold the **central under-screen physical button** for **EXACTLY 1 SECOND** (release as soon as the first beep sounds).
* **Available Controls:**
  * **FILTER:** Tap to cycle IF filter bandwidths: `WIDE` $\rightarrow$ `MID` $\rightarrow$ `NAR`.
  * **ATT / PRE:** Tap to toggle receiver front-end: `NORMAL` $\rightarrow$ `PRE` (Preamplifier) $\rightarrow$ `ATT` (Attenuator).
  * **AGC:** Tap to change AGC decay rate: `SLOW` (SSB), `MID`, `FAST` (CW), or `OFF`.
  * **NB (Noise Blanker):** Tap to toggle ON/OFF; hold to adjust noise threshold level ($1 \dots 100$).
  * **NR (Noise Reduction):** Tap to toggle ON/OFF; hold to adjust DSP noise reduction intensity ($1 \dots 100$).
  * **AUTO-NOTCH:** Tap to engage automatic notch filtering for heterodyne carrier suppression.
* **How to Exit:** Press and hold the central physical button for **1 SECOND** to return to the operating screen.

---

## 6. System Setup Menu (Radio Model, Bluetooth & Settings)

Configure system-level parameters, switch transceiver drivers, or pair Bluetooth adapters.

* **How to Enter:** Press and hold the **central physical button** for **EXACTLY 5 SECONDS**. The speaker emits sequential countdown beeps (at seconds 1, 2, 3, 4), followed by a distinctive long confirmation tone at second 5.
* **Menu Navigation:** Rotate the outer dial to scroll through the setup carousel:
  * **TRX Model:** Select your transceiver model (`IC-7100`, `TX-500MP`, `TS-2000`, etc.). When you pause on a model, it is automatically memorized to NVS after 0.5 seconds.
  * **CAT Baud Rate:** Select the serial baud rate (e.g., 9600 bps for TX-500, 19200 bps for IC-7100).
  * **LIGHT & BEEP:** Adjust LCD backlight brightness and acoustic feedback volume.
  * **BLUETOOTH:**
    1. Tap to initiate a **10-second scan** for nearby BLE serial adapters.
    2. Discovered adapters appear in the roller list (e.g., `JDY-33-BLE`, `HM-10`, or `Link-500` RN4678).
    3. Select your adapter and tap **CONNECT**.
    4. *(Note: Lab599 Link-500 MP / Microchip RN4678 connects transparently via native "Just Works" mode without requiring PIN entry).*
    5. Once connected, the top status bar changes to **Navy Blue**.
  * **BT DISCONNECT:** Instant one-touch disconnection of active Bluetooth links.
* **How to Exit:** Press and hold the physical button for **5 SECONDS** (or 1 second) to return to the Main Frequency screen.

---

## 7. Safe Power-Off Procedure

To prevent accidental shutdowns during critical QSO operations, CAT Dial features a secure multi-click power-off sequence:

1. **Trigger:** Press the central physical button **5 TIMES RAPIDLY** (within 0.5 seconds) from any operating screen.
2. **Countdown Screen:** A distinct red shutdown screen appears, initiating an **8-SECOND COUNTDOWN**.
3. **Audible Warnings:** During the final 3 seconds, rhythmic warning beeps sound.
4. **Confirmation:** Tap the large red on-screen **"POWER OFF"** button before the timer expires. All operational states and frequencies are committed to NVS, and the device powers down completely.
5. **Abort / Cancel:** If the button is not tapped within 8 seconds, the shutdown sequence automatically aborts and returns to your previous operating screen.

---

## 8. Quick Command Reference Sheet

| Action | Control / Gesture | Timing / Behavior |
| :--- | :--- | :--- |
| **VFO A / B Switch** | Tap `VFO A` / `VFO B` label | Short touch (0.2s) with beep |
| **Change Modulation** | Tap Mode label (e.g., `USB`) | Cycles LSB $\rightarrow$ USB $\rightarrow$ CW $\rightarrow$ AM $\rightarrow$ FM $\rightarrow$ DIG |
| **Tune Frequency** | Rotate Outer Knob | Slow = single step; Fast = $3\times$ / $10\times$ acceleration |
| **Change Tuning Decade** | Tap Left (`<`) or Right (`>`) Arrow | Shifts LED indicator under selected decade |
| **Amateur Band Jump** | Tap `B-UP` or `B-DN` buttons | Jumps to band calling frequency & sets default mode |
| **Clarifier / RIT Zero Reset**| Press & Hold Clarifier touch area | **Hold for 2 SECONDS** $\rightarrow$ resets offset to `0.00 kHz` |
| **Audio Mute** | Press & Hold Audio Gain screen | Hold for 0.6 seconds |
| **DSP / Filters Menu** | Hold Central Physical Button | **Hold for 1 SECOND** (release at first beep) |
| **System Setup Menu** | Hold Central Physical Button | **Hold for 5 SECONDS** (release after long tone) |
| **Power Off Sequence** | 5 rapid clicks on Central Button | Click 5 times in 0.5s $\rightarrow$ 8-second countdown confirmation |
| **Connection Status** | Visual bar under radio model | 🟢 **Green** = Cable CAT \| 🔵 **Blue** = Bluetooth BLE |

---
*73 and happy operating!*
