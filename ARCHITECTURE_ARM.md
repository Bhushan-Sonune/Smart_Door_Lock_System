# ARM Version - System Architecture (STM32F103C8T6)

> Assumes an STM32F103C8T6 "Blue Pill" (ARM Cortex-M3, 72 MHz, 3.3V logic). If you used a different ARM board (LPC2148, STM32F4, etc.), only the pin map changes.

## Block diagram

```mermaid
flowchart LR
    subgraph INPUT
        KP[4x4 Keypad]
    end
    subgraph CONTROLLER
        MCU[STM32F103C8T6<br/>ARM Cortex-M3]
    end
    subgraph OUTPUT
        LCD[16x2 I2C LCD]
        SRV[Servo Lock]
        DRV[NPN Driver 2N2222] --> BZ[Buzzer]
    end
    PSU[External 5V Supply] --> MCU
    PSU --> SRV
    PSU --> LCD
    PSU --> BZ
    STL[ST-Link V2] -- "SWD: PA13/PA14" --> MCU
    KP -- "PA0-PA7 (GPIO scan)" --> MCU
    MCU -- "PB6/PB7 (I2C1)" --> LCD
    MCU -- "PA8 (TIM1_CH1 PWM, 50 Hz)" --> SRV
    MCU -- "PB12 (GPIO)" --> DRV
```

## Firmware layers

```mermaid
flowchart TD
    APP["Application: lock state machine, password check"]
    DRV["Drivers: keypad scan, LCD (I2C), servo PWM, buzzer"]
    HAL["STM32 HAL / LL: GPIO, I2C1, TIM1, SysTick"]
    HW["STM32F103 hardware"]
    APP --> DRV --> HAL --> HW
```

## Pin map

| STM32 Pin | Connected To | Function |
|-----------|--------------|----------|
| PA0-PA3 | Keypad rows 1-4 | GPIO output (scan) |
| PA4-PA7 | Keypad cols 1-4 | GPIO input, internal pull-up |
| PA8 | Servo signal | TIM1_CH1 PWM (50 Hz, 1-2 ms pulse) |
| PB12 | Buzzer driver (Q1 base via 1k) | GPIO output |
| PB6 | LCD SCL | I2C1 clock |
| PB7 | LCD SDA | I2C1 data |
| PA13 / PA14 | ST-Link | SWDIO / SWCLK |
| 5V | LCD, Servo, Buzzer | Power (from external supply) |
| GND | All grounds | Common ground |

## Arduino vs ARM

| Item | Arduino Uno | STM32 Blue Pill |
|------|-------------|-----------------|
| Core | 8-bit AVR, 16 MHz | 32-bit ARM Cortex-M3, 72 MHz |
| Logic level | 5V | 3.3V (some pins 5V-tolerant) |
| Servo | `Servo` library (D10) | Hardware timer PWM (PA8) |
| I2C | A4 / A5 | PB6 / PB7 |
| Buzzer | Direct from pin | Via NPN transistor |
| Programming | USB bootloader | ST-Link (SWD) |

## Build notes
- Toolchain options: STM32CubeIDE (HAL), PlatformIO, or Arduino IDE with the STM32duino core.
- Configure I2C1 at 100 kHz and TIM1 for 50 Hz (prescaler/ARR so 1-2 ms pulses map to about 0-180 degrees).
- Keep the same servo angles as the Arduino version: 50 degrees open, 110 degrees locked (tune for your latch).
