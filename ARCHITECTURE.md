# System Architecture

## Block diagram

```mermaid
flowchart LR
    subgraph INPUT
        KP[4x4 Keypad]
    end
    subgraph CONTROLLER
        MCU[Arduino Uno<br/>ATmega328P]
    end
    subgraph OUTPUT
        LCD[16x2 I2C LCD]
        SRV[Servo Lock]
        BZ[Buzzer]
    end
    PSU[5V Supply] --> MCU
    PSU --> SRV
    KP -- "D2-D9 (matrix scan)" --> MCU
    MCU -- "A4/A5 (I2C)" --> LCD
    MCU -- "D10 (PWM)" --> SRV
    MCU -- "D11" --> BZ
```

## Firmware flow

```mermaid
flowchart TD
    A[Power on] --> B[Init servo, LCD, buzzer]
    B --> C[Show welcome screen]
    C --> D[Loop: ENTER PASSWORD]
    D --> E{Key pressed?}
    E -- No --> D
    E -- "0-9, A, B, *, #" --> F[Append to buffer, show *]
    F --> D
    E -- "C" --> G[Clear buffer]
    G --> D
    E -- "D" --> H{State?}
    H -- Unlocked --> I[doorlocked: evaluate password]
    H -- Locked --> J[dooropen: evaluate password]
    I -- Match --> K[Servo 110 deg, beep, LOCKED]
    J -- Match --> L[Servo 50 deg, beep, OPENED]
    I -- No match --> M[Error beeps, WRONG PASSWORD]
    J -- No match --> M
    K --> G
    L --> G
    M --> G
```

## Pin map

| Arduino Pin | Connected To | Function |
|-------------|--------------|----------|
| D2-D5 | Keypad rows 1-4 | Matrix scan (output) |
| D6-D9 | Keypad cols 1-4 | Matrix scan (input) |
| D10 | Servo signal | PWM position control |
| D11 | Buzzer (+) | Digital output |
| A4 (SDA) | LCD I2C SDA | I2C data |
| A5 (SCL) | LCD I2C SCL | I2C clock |
| 5V | LCD VCC, Servo VCC | Power |
| GND | All grounds | Common ground |

## Servo positions

| State | Angle |
|-------|-------|
| Open / unlocked | 50 deg |
| Locked | 110 deg |
