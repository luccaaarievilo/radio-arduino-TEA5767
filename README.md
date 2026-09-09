📻 FM Radio — Arduino Uno + TEA5767 + Rotary Encoder

A simple FM radio receiver project built with an Arduino Uno, TEA5767 FM receiver module, and a rotary encoder for tuning and control.

The project does not use a display. Frequency, signal strength, stereo/mono status, and mute state are shown through the Serial Monitor.

✨ Features
📻 FM tuning from 87.5 MHz to 108.0 MHz
🎛️ Frequency control using a rotary encoder
🔎 Automatic station scanning
🔇 Mute mode
🤫 TEA5767 soft mute to reduce static
💾 Automatic frequency saving to EEPROM
📡 Signal-strength monitoring
🎵 Stereo/mono detection
🔄 Automatically restores the last frequency after reboot
⚡ Temporary muting while tuning
🖥️ Serial Monitor feedback
🧰 Components
Required
Arduino Uno
TEA5767 FM receiver module
Rotary encoder with push button (CLK, DT, and SW)
Jumper wires
USB power source for the Arduino
Audio

The audio output depends on the specific TEA5767 module being used.

Some modules provide outputs for:

External amplifiers
Headphones
Speakers through an amplifier circuit

Important: The TEA5767 is primarily an FM receiver. Depending on your module, an external audio amplifier may be required to drive a speaker properly.

🔌 Pinout
TEA5767 → Arduino Uno
TEA5767	Arduino Uno
GND	GND
SDA	A4
SCL	A5
+5V	5V

You can also use the dedicated SDA/SCL pins on boards that provide them.

I²C

The TEA5767 uses the following I²C address:

0x60
🎛️ Rotary Encoder → Arduino Uno
Encoder	Arduino Uno
GND	GND
+5V	5V
CLK	D2
DT	D3
SW	D4

The code uses INPUT_PULLUP for the encoder inputs.

🎮 Controls

The rotary encoder provides three main controls.

Rotate the encoder
↻ Rotate clockwise
   Frequency +0.1 MHz

↺ Rotate counter-clockwise
   Frequency -0.1 MHz

The default tuning step is:

#define FREQ_STEP 0.1f
Encoder button

Different numbers of clicks perform different actions:

Action	Function
1 click	Scan for previous station
2 clicks	Scan for next station
3+ clicks	Toggle mute

Example:

1x → Previous station
2x → Next station
3x → MUTE
🔇 Anti-Static System

The project uses two mechanisms to reduce unwanted noise.

Soft Mute

The TEA5767's built-in SMUTE functionality is used to reduce audio when the signal is too weak.

uint8_t smute = 1;

This helps prevent excessive static when no usable station is being received.

Mute While Tuning

When the encoder is rotated, the audio is temporarily muted:

Encoder rotation
      ↓
    MUTE
      ↓
Frequency changes
      ↓
Encoder stops
      ↓
~400 ms
      ↓
Audio returns

The delay can be adjusted with:

#define TUNING_UNMUTE_DELAY_MS 400
📡 Automatic Station Scanning

The encoder button can start an automatic scan through the FM band.

Previous station
1 click
   ↓
Scan downward
   ↓
Station found
   ↓
Tune to station
Next station
2 clicks
   ↓
Scan upward
   ↓
Station found
   ↓
Tune to station

The audio is muted during scanning to prevent unwanted noise.

The maximum scan time is approximately:

4 seconds
💾 EEPROM

The current frequency is automatically stored in the Arduino's EEPROM.

For example:

Radio tuned to:
101.9 MHz

Arduino powered off
        ↓
Arduino powered on
        ↓
101.9 MHz restored

The frequency is stored as an integer representing tenths of a MHz, avoiding floating-point storage in EEPROM.

Example:

101.9 MHz → 1019
🖥️ Serial Monitor

Open the Arduino IDE Serial Monitor at:

9600 baud

At startup, the Arduino displays information similar to:

Radio FM - Arduino Uno + TEA5767 + Encoder (no display)

101.9 MHz   signal:8   STEREO

Ready. Rotate = tuning | button: 1=previous 2=next 3=mute

During operation, the Serial Monitor displays:

Current frequency
Signal strength
Stereo/mono status
Mute state
⚙️ Configuration

The main configuration values are located near the beginning of the code.

#define FREQ_STEP  0.1f

#define FREQ_MIN10 875
#define FREQ_MAX10 1080
#define FREQ_DEF10 1019

These correspond to:

Setting	Value
Minimum frequency	87.5 MHz
Maximum frequency	108.0 MHz
Default frequency	101.9 MHz
Tuning step	0.1 MHz
🔄 Reversing Encoder Direction

If the encoder operates in the opposite direction, you do not need to change the wiring.

Change:

#define ENCODER_INVERT 0

to:

#define ENCODER_INVERT 1
Normal
↻ → Frequency increases
↺ → Frequency decreases
Inverted
↻ → Frequency decreases
↺ → Frequency increases
📚 Libraries

The project uses libraries included with the standard Arduino environment:

#include <Wire.h>
#include <EEPROM.h>
Wire

Used for I²C communication with the TEA5767.

EEPROM

Used to store the last tuned frequency.

No external libraries are required for the provided code.

🚀 Installation
1. Clone the repository
git clone https://github.com/YOUR-USERNAME/radio-fm-arduino.git

Or simply download the .ino file.

2. Open the project

Open:

radio-arduino.ino

Select:

Tools → Board → Arduino Uno

Then select the COM/serial port connected to your Arduino.

3. Wire the hardware
TEA5767
GND  → GND
SDA  → A4
SCL  → A5
VCC  → 5V
Rotary Encoder
GND  → GND
VCC  → 5V
CLK  → D2
DT   → D3
SW   → D4
4. Upload the firmware

Compile and upload the sketch using the Arduino IDE.

Then open the:

Serial Monitor

and set it to:

9600 baud
🧪 TEA5767 Detection

During setup(), the Arduino checks whether a device responds at:

0x60

If the module does not respond, the Serial Monitor displays:

WARNING: TEA5767 did not respond at 0x60. Check SDA/SCL/VCC.

If this happens, check:

TEA5767 power
Common GND
SDA → A4
SCL → A5
I²C address of the module
🗂️ Project Structure
radio-fm-arduino/
│
├── radio-arduino.ino
├── radio-arduino-pinout.txt
└── README.md
📋 Specifications
Item	Specification
MCU	Arduino Uno
FM Receiver	TEA5767
Communication	I²C
I²C Address	0x60
Frequency Range	87.5–108.0 MHz
Tuning Step	0.1 MHz
Control	Rotary encoder
Button	1 / 2 / 3 clicks
Storage	EEPROM
Interface	Serial Monitor
Baud Rate	9600
Display	None
⚠️ Notes
The antenna has a significant impact on reception quality.
The TEA5767 and Arduino must share a common GND.
Different TEA5767 modules may have different power and audio circuits. Check the pinout of your specific module.
If the audio output is too weak for a speaker, use a suitable external amplifier.
The project is configured for the 87.5–108.0 MHz FM band.
EEPROM is used only to store the last tuned frequency.
