# CharGriller-980 Wiring Chart

**IMPORTANT: Read completely before wiring. Double-check all connections before applying power.**

---

## Power Distribution

### 12V Power Supply Connections
| From | To | Wire Gauge | Notes |
|------|-----|------------|-------|
| 12V PSU + | Buck Converter VIN+ | 18 AWG 🔴 Red | Main 12V input |
| 12V PSU + | INA219 VIN+ | 18 AWG 🔴 Red | High-side current sensing |
| 12V PSU - | Common GND (Star Point) | 18 AWG ⚫ Black | See grounding section |

### 5V Power Rail (from Buck Converter)
| From | To | Wire Gauge | Notes |
|------|-----|------------|-------|
| Buck VOUT+ | ESP32 5V/VIN | 22 AWG 🔴 Red | Main controller power |
| Buck VOUT+ | TM1637 Display #1 VCC | 22 AWG 🔴 Red | Current temp display |
| Buck VOUT+ | TM1637 Display #2 VCC | 22 AWG 🔴 Red | Setpoint display |
| Buck VOUT+ | Buzzer + | 22 AWG 🔴 Red | 5V active buzzer |
| Buck VOUT- | Common GND (Star Point) | 22 AWG ⚫ Black | Return path |

### 3.3V Power (from ESP32 onboard regulator)
| From | To | Wire Gauge | Notes |
|------|-----|------------|-------|
| ESP32 3V3 | MAX31865 VCC | 22 AWG 🟠 Orange | RTD amplifier power |
| ESP32 3V3 | INA219 VCC | 22 AWG 🟠 Orange | Current sensor logic power |

⚠️ **CRITICAL**: INA219 VCC must be 3.3V (not 5V) for stable operation with ESP32

---

## I2C Bus (INA219 Current Sensor)

| ESP32 Pin | Signal | Connect To | Wire Color | Notes |
|-----------|--------|------------|------------|-------|
| GPIO21 | SDA | INA219 SDA | ⚪ White | I2C Data |
| GPIO17 | SCL | INA219 SCL | 🔵 Blue | I2C Clock |

**I2C Address**: 0x40 (default for INA219)

**Pull-up Resistors**: Not required - ESP32 has internal pull-ups enabled in software

---

## SPI Bus (MAX31865 Thermocouple Amplifier)

| ESP32 Pin | Signal | MAX31865 Pin | Wire Color | Notes |
|-----------|--------|--------------|------------|-------|
| GPIO18 | SCK | CLK | 🟡 Yellow | SPI Clock |
| GPIO19 | MISO | DO (SO) | 🟢 Green | SPI Data Out |
| GPIO14 | CS | CS | 🟠 Orange | Chip Select (active low) |
| 3.3V | Power | VCC | 🟠 Orange | 3.3V power |
| GND | Ground | GND | ⚫ Black | Ground |

**Note**: MAX31865 requires MOSI — add GPIO5 → MAX31865 SDI wire

### Thermocouple Connection
| MAX31865 Terminal | Thermocouple Wire | Notes |
|-------------------|-------------------|-------|
| RTD+ | Either probe wire | 2-wire PT1000 — no polarity |
| RTD- | Other probe wire | No polarity — but note which wire for consistency |

⚠️ **PT1000 probe: verify with multimeter (~1040-1080Ω at room temp ~21°C). No polarity.**

---

## TM1637 Display #1 (Current Temperature - Left)

| ESP32 Pin | Signal | TM1637 Pin | Wire Color | Notes |
|-----------|--------|------------|------------|-------|
| GPIO23 | CLK | CLK | 🟤 Brown | Clock signal |
| GPIO26 | DIO | DIO | 🟣 Purple | Data I/O (bidirectional) |
| 5V | Power | VCC | 🔴 Red | 5V power |
| GND | Ground | GND | ⚫ Black | Ground |

---

## TM1637 Display #2 (Setpoint - Right)

| ESP32 Pin | Signal | TM1637 Pin | Wire Color | Notes |
|-----------|--------|------------|------------|-------|
| GPIO25 | CLK | CLK | 🟤 Brown | Clock signal |
| GPIO22 | DIO | DIO | 🟣 Purple | Data I/O (bidirectional) |
| 5V | Power | VCC | 🔴 Red | 5V power |
| GND | Ground | GND | ⚫ Black | Ground |

**TM1637 Notes:**
- Some modules have 4 pins, others have 6 (ignore extra pins)
- CLK and DIO can tolerate 5V signals even though ESP32 is 3.3V
- Keep wires under 12 inches for reliable operation

---

## Rotary Encoder (KY-040)

| ESP32 Pin | Signal | Encoder Pin | Wire Color | Notes |
|-----------|--------|-------------|------------|-------|
| GPIO32 | Phase A | CLK / A | ⚪ White | Encoder channel A |
| GPIO33 | Phase B | DT / B | 🩶 Gray | Encoder channel B |
| GPIO27 | Button | SW | 🟢 Green | Push button switch |
| GND | Ground | GND / - | ⚫ Black | Common ground |
| (not connected) | Power | + | (none) | KY-040 has internal pull-ups |

**Encoder Module Notes:**
- Most KY-040 modules have built-in pull-up resistors
- ESP32 enables internal pull-ups in software for redundancy
- If encoder is "jumpy" or misses steps, add 0.1µF capacitors between each signal pin and GND

---

## Door / Hopper Safety Switches (Stock Harness or Manual Toggle)

| ESP32 Pin | Signal | Switch Terminal | Wire Color | Notes |
|-----------|--------|-----------------|------------|-------|
| GPIO13 | Input | Terminal 1 | 🟡 Yellow | Signal input (with internal pull-up) |
| GND | Ground | Terminal 2 | ⚫ Black | Ground return |

### How the Stock Char-Griller 980 System Works:
The factory Char-Griller 980 wiring harness has **two microswitches** (upper hopper lid for fueling, and lower hopper door for ash/lighting) **already wired in series into a single 2-pin connector**:
- **Both doors closed**: Both switches are depressed/closed → Circuit completed to GND → GPIO13 is pulled **LOW** (`OFF` / Normal run).
- **Either door opened**: Circuit is broken (open loop) → GPIO13 is pulled **HIGH** by internal pull-up (`ON` / Fan paused / Display shows `oPEn` / `door`).

### Option 1: Plug-and-Play with Factory Harness
Connect the factory 2-pin door switch harness connector directly between **GPIO13** and **GND**. No changes to the factory wiring required!

### Option 2: Manual SPST Toggle Switch (if OEM switches are broken or bypassed)
Wire a simple ON/OFF toggle switch between **GPIO13** and **GND**:
- **Switch ON / Closed** (shorted to GND) = "Doors Closed / Run"
- **Switch OFF / Open** (disconnected) = "Chamber Open / Fan Paused"

---

## Fan Control Circuit

### PWM Signal Path
| ESP32 Pin | Connect To | Wire Color | Notes |
|-----------|------------|------------|-------|
| GPIO16 | 220Ω Resistor → MOSFET Gate | 🔵 Blue | PWM control signal |

### Fan Power Path (High-Side Current Sensing)
```
12V+ → INA219 VIN+ → INA219 VIN- → MOSFET Drain → Fan Negative (Black)
                                                       ↓
                                                 MOSFET Source → GND
                                                 
Fan Positive (Red) → 12V+ (direct connection)
```

**Detailed Connections:**

| From | To | Wire Gauge | Notes |
|------|-----|------------|-------|
| 12V PSU + | INA219 VIN+ | 18 AWG 🔴 Red | Current sensing input |
| INA219 VIN- | MOSFET Drain | 18 AWG 🔴 Red | To switching element |
| MOSFET Drain | Fan Negative Wire | 18 AWG ⚫ Black | PWM-switched ground |
| MOSFET Source | Common GND | 18 AWG ⚫ Black | Return to ground |
| Fan Positive Wire | 12V+ | 18 AWG 🔴 Red | Direct to power supply |
| GPIO16 | 220Ω Resistor | 22 AWG 🔵 Blue | PWM signal |
| 220Ω Resistor | MOSFET Gate | 22 AWG 🔵 Blue | Gate drive |
| MOSFET Gate | 10kΩ Resistor → GND | (optional) | Pull-down for defined off-state |

### Flyback Diode (Protection)
| Component | Connection | Notes |
|-----------|------------|-------|
| 1N4007 Diode | Cathode (stripe) → 12V+ | Reverse voltage protection |
| 1N4007 Diode | Anode → Fan Negative | Clamps inductive kickback |

**MOSFET Module Notes:**
- If using pre-made MOSFET module, gate resistor and flyback diode may be included
- Verify module is "logic-level" (Vgs < 5V) - IRLZ34N, or similar
- Heat sink recommended if fan draws > 2A

---

## Buzzer (Active Piezo)

| ESP32 Pin | Signal | Buzzer Terminal | Wire Color | Notes |
|-----------|--------|-----------------|------------|-------|
| GPIO4 | Control | + (Positive) | 🟣 Purple | Active-high control |
| GND | Ground | - (Negative) | ⚫ Black | Ground |

**Buzzer Type**: Must be **ACTIVE** buzzer (has internal oscillator)
- 2-wire active buzzer (not 3-wire passive)
- 5V rated, typically draws 20-30mA
- If too quiet, consider adding transistor driver

**Testing**: Connect to 5V directly - should produce continuous tone

---

## Grounding Architecture (CRITICAL)

Use **star grounding** to minimize noise and ground loops:

```
                    ┌─────────────────┐
                    │   12V PSU -     │
                    │  (STAR POINT)   │
                    └────────┬────────┘
                             │
           ┌─────────────────┼─────────────────┐
           │                 │                 │
           │                 │                 │
      ESP32 GND         Buck GND          INA219 GND
           │                 │                 │
           │                 │            MOSFET Source
           │                 │                 │
      MAX31865 GND      TM1637 #1          Fan Ground
           │                 │
      Encoder GND       TM1637 #2
           │                 │
      Door Switch       Buzzer GND
```

**Grounding Rules:**
1. All grounds eventually connect at **ONE** central point (star)
2. No ground loops (no circular paths)
3. Keep high-current ground (fan) as short as possible
4. Run separate ground wires - don't daisy-chain

---

## Power Supply Requirements

### Input Power
| Parameter | Specification |
|-----------|---------------|
| Voltage | 12V DC (11.5-12.5V acceptable) |
| Current | 5A recommended (3A minimum) |
| Connector | 2.1mm barrel jack or screw terminals |
| Type | Regulated switching power supply |

### Current Budget
| Component | Voltage | Current Draw | Notes |
|-----------|---------|--------------|-------|
| ESP32 | 5V | 500mA | Peak during WiFi transmission |
| TM1637 #1 | 5V | 100mA | At full brightness |
| TM1637 #2 | 5V | 100mA | At full brightness |
| Buzzer | 5V | 30mA | When active |
| MAX31865 | 3.3V | 2mA | From ESP32 regulator |
| INA219 | 3.3V | 1mA | From ESP32 regulator |
| **5V Rail Total** | | **~730mA** | Use 2A buck converter for headroom |
| **Fan (12V)** | 12V | 1-3A | Depends on fan model and duty cycle |
| **Total System** | 12V input | **~4A peak** | 5A supply recommended |

---

## Wiring Best Practices

### Wire Gauge Selection
| Circuit | Recommended Gauge | Max Length |
|---------|-------------------|------------|
| 12V main power | 18 AWG | 6 feet |
| 5V power distribution | 22 AWG | 3 feet |
| 3.3V signals | 22-24 AWG | 2 feet |
| Digital signals | 22-24 AWG | 1 foot |

### Wire Management
1. **Use ferrules on stranded wire** - Prevents fraying in screw terminals
2. **Twist thermocouple wires together** - Reduces EMI pickup
3. **Separate power and signal wires** - Avoid parallel runs > 6 inches
4. **Keep SPI wires short** - MAX31865 is sensitive to capacitance
5. **Label everything** - Use label maker or masking tape
6. **Use heat shrink** - Cover all solder joints and connections
7. **Strain relief** - Secure wires near connectors

### Color Code Recommendations
| Purpose | Color | Notes |
|---------|-------|-------|
| 12V positive | 🔴 Red | High voltage power |
| 5V positive | 🔴 Red | Lower voltage power |
| 3.3V positive | 🟠 Orange | Logic power |
| Ground (any) | ⚫ Black | All returns |
| I2C SDA | ⚪ White | Data signal |
| I2C SCL | 🔵 Blue | Clock signal |
| SPI Clock | 🟡 Yellow | Clock signal |
| SPI Data | 🟢 Green | Data signal |
| PWM/Control | 🔵 Blue or 🟣 Purple | Control signals |

**Note**: These are suggestions - use what you have, but **document your actual colors**!

---

## Pre-Power Checklist

Before applying power for the first time:

### Visual Inspection
- [ ] All solder joints shiny and solid (no cold joints)
- [ ] No solder bridges between pads
- [ ] No exposed wire strands near terminals
- [ ] All ICs oriented correctly (check pin 1 markers)
- [ ] Antenna connected to ESP32 U.FL connector
- [ ] Heat sinks installed on buck converter and MOSFET (if needed)

### Continuity Testing (Power OFF)
- [ ] Verify ground continuity between all GND points
- [ ] Verify NO continuity between 12V and GND
- [ ] Verify NO continuity between 5V and GND
- [ ] Verify NO continuity between 3.3V and GND
- [ ] Check for shorts on all power rails

### Voltage Testing (12V connected, ESP32 disconnected)
- [ ] Measure 12V rail: 11.5-12.5V
- [ ] Adjust buck converter to exactly 5.0V (±0.05V)
- [ ] Verify no voltage on signal pins (except pull-ups)

### First Power-On (12V + ESP32)
- [ ] Power on and monitor for smoke/heat
- [ ] Check ESP32 LED indicators (should blink)
- [ ] Verify 5V rail remains stable
- [ ] Check 3.3V output from ESP32
- [ ] Feel components for excessive heat

---

## Troubleshooting Quick Reference

| Symptom | Possible Cause | Check |
|---------|----------------|-------|
| ESP32 won't boot | Power issue | Verify 5V at VIN pin |
| | GPIO strapping | Check GPIO12 is LOW at boot |
| No WiFi | Antenna not connected | Verify U.FL connection |
| Temperature shows -127°C | Thermocouple disconnected | Check T+ and T- terminals |
| | Wrong polarity | Swap thermocouple wires |
| Temperature unstable | EMI pickup | Twist thermocouple wires |
| | Loose connection | Check screw terminals |
| Display doesn't light | No power | Check 5V at VCC pin |
| | Wrong wiring | Verify CLK/DIO connections |
| Encoder doesn't work | Pull-ups needed | Enable INPUT_PULLUP in config |
| | Bouncing | Add 0.1µF caps to pins |
| Fan doesn't run | MOSFET not switching | Check gate voltage (should vary 0-3.3V) |
| | No 12V | Verify 12V at fan positive |
| | Bad MOSFET | Test with multimeter |
| Fan runs always | MOSFET stuck on | Check for short gate-to-drain |
| | Inverted logic | May need LOW-side switching |
| INA219 not detected | Wrong I2C pins | Verify SDA=21, SCL=17 |
| | Bad module | Try I2C scan in logs |
| Buzzer doesn't work | Passive buzzer | Need active buzzer with oscillator |
| | Wrong polarity | Swap +/- connections |

---

## ESP32 GPIO Pin Summary (Quick Reference)

| GPIO | Function | Component | Direction | Notes |
|------|----------|-----------|-----------|-------|
| 4 | Output | Buzzer | OUT | Active-high control |
| 13 | Input | Door Switch | IN | INPUT_PULLUP enabled |
| 14 | SPI | MAX31865 CS | OUT | Chip select (active low) |
| 16 | PWM | Fan Control | OUT | 25kHz PWM signal |
| 17 | I2C | SCL (INA219) | OUT | Clock line |
| 18 | SPI | MAX31865 CLK | OUT | SPI clock |
| 5  | SPI | MAX31865 MOSI | OUT | SPI data out (required for MAX31865) |
| 19 | SPI | MAX31865 MISO | IN | SPI data in |
| 21 | I2C | SDA (INA219) | I/O | Data line |
| 22 | Output | TM1637 #2 DIO | I/O | Setpoint display data |
| 23 | Output | TM1637 #1 CLK | OUT | Current temp clock |
| 25 | Output | TM1637 #2 CLK | OUT | Setpoint clock |
| 26 | Output | TM1637 #1 DIO | I/O | Current temp data |
| 27 | Input | Encoder Button | IN | INPUT_PULLUP enabled |
| 32 | Input | Encoder Phase A | IN | INPUT_PULLUP enabled |
| 33 | Input | Encoder Phase B | IN | INPUT_PULLUP enabled |

**Unused Pins Available for Expansion:**
- GPIO 12, 15, 34, 35, 36, 39 (input only)
- GPIO5 now used: MAX31865 MOSI

---

## Advanced: Adding a Second Thermocouple (Meat Probe)

To add meat temperature monitoring (future expansion):

### Additional Hardware Needed
- MAX31865 module (second unit)
- K-type food-grade thermocouple probe
- 3 additional wires

### Wiring
| ESP32 Pin | MAX31865 #2 |
|-----------|-------------|
| GPIO18 | CLK (shared) |
| GPIO19 | MISO (shared) |
| GPIO5 | CS (dedicated) |
| 3.3V | VCC |
| GND | GND |

### ESPHome Config Addition
```yaml
sensor:
  - platform: max31855
    name: "Meat Temperature"
    cs_pin: GPIO5  # Different CS pin
    update_interval: 2s
    # ... rest of config
```

---

**Document Version**: 1.1  
**Last Updated**: December 2024  
**Matches Config**: chargriller-980.yaml v0.2
