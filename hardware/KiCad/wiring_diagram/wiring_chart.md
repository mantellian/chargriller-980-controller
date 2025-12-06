# CharGriller-980 Wiring Chart

## Power Distribution
| Component | Connection | Wire Color |
|-----------|------------|------------|
| ESP32 | 5V Power Supply | 🔴 Red (5V) |
| ESP32 | Ground | ⚫ Black (GND) |
| All Components | Common Ground | ⚫ Black (GND) |

---

## I2C Bus
| ESP32 Pin | Function | Wire Color |
|-----------|----------|------------|
| GPIO21    | SDA      | ⚪ White   |
| GPIO17    | SCL      | 🔵 Blue    |

---

## MAX31855 Thermocouple Amplifier (SPI)
| ESP32 Pin | MAX31855 Pin | Wire Color | Function |
|-----------|--------------|------------|----------|
| GPIO18 | CLK | 🟡 Yellow | SPI Clock |
| GPIO19 | DO (MISO) | 🟢 Green | SPI Data Out |
| GPIO14 | CS | 🟠 Orange | Chip Select |
| 3.3V | VCC | 🔴 Red | Power (3.3V) |
| GND | GND | ⚫ Black | Ground |

**Thermocouple Connection:**
| MAX31855 | Thermocouple | Wire Color |
|----------|--------------|------------|
| T+ | Positive | 🔴 Red |
| T- | Negative | 🔵 Blue |

---

## INA219 Current Sensor (I2C)
| ESP32 Pin | INA219 Pin | Wire Color | Function |
|-----------|------------|------------|----------|
| GPIO21    | SDA        | ⚪ White   | I2C Data |
| GPIO17    | SCL        | 🔵 Blue    | I2C Clock |
| 3.3V / 5V | VCC        | 🔴 Red     | Power    |
| GND       | GND        | ⚫ Black   | Ground   |

---

## TM1637 Display #1 (Current Temperature)
| ESP32 Pin | TM1637 Pin | Wire Color | Function |
|-----------|------------|------------|----------|
| GPIO23 | CLK | 🟤 Brown | Clock |
| GPIO26 | DIO | 🟣 Purple | Data I/O |
| 5V | VCC | 🔴 Red | Power (5V) |
| GND | GND | ⚫ Black | Ground |

---

## TM1637 Display #2 (Setpoint Temperature)
| ESP32 Pin | TM1637 Pin | Wire Color | Function |
|-----------|------------|------------|----------|
| GPIO25 | CLK | 🟤 Brown | Clock |
| GPIO22 | DIO | 🟣 Purple | Data I/O |
| 5V | VCC | 🔴 Red | Power (5V) |
| GND | GND | ⚫ Black | Ground |

---

## Rotary Encoder (with Push Button)
| ESP32 Pin | Encoder Pin | Wire Color | Function |
|-----------|-------------|------------|----------|
| GPIO32 | A / CLK | ⚪ White | Phase A |
| GPIO33 | B / DT | 🔵 Gray | Phase B |
| GPIO27 | SW (Button) | 🟢 Green | Push Button |
| GND | GND (pin) | ⚫ Black | Common Ground |
| 3.3V | + (if needed) | 🔴 Red | Power (some encoders) |

**Note:** Most rotary encoders have internal pullups. If yours doesn't, the ESP32 INPUT_PULLUP mode handles it.

---

## Door/Lid Switch
| ESP32 Pin | Switch Pin | Wire Color | Function |
|-----------|------------|------------|----------|
| GPIO13 | One side | 🟡 Yellow | Signal |
| GND | Other side | ⚫ Black | Ground |

**Switch Type:** Normally Open (NO) - closes when lid opens

---

## Blower Fan (PWM Control)
| ESP32 Pin | Fan Connection | Wire Color | Function |
|-----------|----------------|------------|----------|
| GPIO16 | PWM Input | 🔵 Blue | PWM Speed Control |
| 12V+ | Fan Power + | 🔴 Red | 12V Power |
| GND | Fan Power - | ⚫ Black | Ground |

**Note:** Fan needs external 12V power supply. ESP32 only provides PWM signal (low current).

---

## Buzzer (Active Buzzer)
| ESP32 Pin | Buzzer Pin | Wire Color | Function |
|-----------|------------|------------|----------|
| GPIO4 | Positive (+) | 🟣 Purple | Signal |
| GND | Negative (-) | ⚫ Black | Ground |

**Note:** Use an active buzzer (has internal oscillator). If using a passive buzzer, you may need a transistor driver.

---

## Power Supply Requirements

### Main Power Supply
- **12V DC** (for blower fan)
- **5V DC** (for ESP32 via USB or buck converter from 12V)
- Recommended: 12V power supply with buck converter to 5V

### Voltage Levels by Component
| Component | Voltage | Current Draw |
|-----------|---------|--------------|
| ESP32 | 5V | ~500mA |
| MAX31855 | 3.3V | ~2mA (from ESP32) |
| TM1637 Displays (x2) | 5V | ~100mA each |
| Rotary Encoder | 3.3V-5V | ~1mA |
| Buzzer | 5V | ~30mA |
| Blower Fan | 12V | 1-3A (depends on fan) |

**Total 5V requirement:** ~800mA (without fan)  
**Total 12V requirement:** 1-3A (fan only)

---

## Wiring Tips

1. **Use a common ground** - Connect all GND connections together (star ground recommended)
2. **Keep SPI wires short** - MAX31855 is sensitive to noise
3. **Separate high-current wires** - Keep 12V fan wires away from signal wires
4. **Twist thermocouple wires** - Reduces electromagnetic interference
5. **Use ferrules on stranded wire** - Better connection in screw terminals
6. **Label all wires** - Makes troubleshooting easier
7. **Add pull-down resistor on fan PWM if needed** - Some fans need 10kΩ to GND on PWM line

---

## Quick Reference - ESP32 Pin Summary

| GPIO | Component | Function |
|------|-----------|----------|
| 4    | Buzzer    | Signal   |
| 13   | Door Switch | Lid Sensor |
| 14   | MAX31855  | CS       |
| 16   | Fan PWM   | Fan Speed |
| 17   | I2C       | SCL (for INA219) |
| 18   | MAX31855  | CLK      |
| 19   | MAX31855  | MISO     |
| 21   | I2C       | SDA (for INA219) |
| 22   | TM1637 (Set) | DIO (Setpoint Data) |
| 23   | TM1637 (Cur) | CLK (Current Temp Clock) |
| 25   | TM1637 (Set) | CLK (Setpoint Clock) |
| 26   | TM1637 (Cur) | DIO (Current Temp Data) |
| 27   | Encoder   | Button   |
| 32   | Encoder   | Phase A  |
| 33   | Encoder   | Phase B  | 
