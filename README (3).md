# Smart Door Lock System (Arduino)

A keypad-based digital door lock built with an Arduino Uno. The user enters a password on a 4x4 keypad; a servo locks/unlocks the latch, an I2C LCD shows status, and a buzzer gives audio feedback.

![Circuit diagram](docs/circuit_diagram.svg)

## Features
- Password entry with masked `*` display
- Servo-driven lock / unlock (110 deg locked, 50 deg open)
- LCD status messages (16x2 I2C)
- Buzzer feedback for success / failure
- `C` clears input, `D` toggles lock/unlock

## Hardware
See [hardware/BOM.md](hardware/BOM.md).

## Wiring

| Component | Pin | Arduino |
|-----------|-----|---------|
| Keypad rows 1-4 | 1-4 | D2, D3, D4, D5 |
| Keypad cols 1-4 | 5-8 | D6, D7, D8, D9 |
| Servo signal | Orange | D10 |
| Buzzer (+) | + | D11 |
| LCD I2C SDA | SDA | A4 |
| LCD I2C SCL | SCL | A5 |
| LCD / Servo VCC | VCC | 5V |
| All GND | GND | GND |

> Use an external 5V supply for the servo and share its ground with the Arduino.

More detail: [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md)

## Software setup
1. Install the [Arduino IDE](https://www.arduino.cc/en/software).
2. Install libraries via Library Manager:
   - `Keypad` (Mark Stanley, Alexander Brevig)
   - `LiquidCrystal I2C` (Frank de Brabander)
   - `Password` (Alexander Brevig) - install from the Arduino Playground ZIP if not listed
   - `Servo` (built in)
3. Open `src/DoorLock/DoorLock.ino`, select **Arduino Uno** and your port, then upload.

## Usage
| Key | Action |
|-----|--------|
| `0-9`, `A`, `B`, `*`, `#` | Enter password characters |
| `C` | Clear current input |
| `D` | Submit and toggle lock / unlock |

Default password: `0012` (change it in the sketch).

## Project structure
```
smart-door-lock/
├── README.md
├── LICENSE
├── .gitignore
├── src/DoorLock/DoorLock.ino
├── docs/
│   ├── ARCHITECTURE.md
│   ├── KNOWN_ISSUES.md
│   └── circuit_diagram.svg
└── hardware/BOM.md
```

## Known issues
See [docs/KNOWN_ISSUES.md](docs/KNOWN_ISSUES.md).

## License
MIT - see [LICENSE](LICENSE).

## ARM (STM32) version
A second implementation uses an STM32F103C8T6 (ARM Cortex-M3). See [docs/arm/ARCHITECTURE_ARM.md](docs/arm/ARCHITECTURE_ARM.md) and the diagram below. Firmware goes in `src/STM32/`.

![ARM circuit diagram](docs/arm/circuit_diagram_arm.svg)
