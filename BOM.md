# CharGriller 980 ESP32 Controller - Bill of Materials (BOM)

This is a complete parts list for building an ESP32-based controller replacement for the CharGriller Gravity 980 smoker.

## Required Components

### Microcontroller
| Part | Specs | Quantity | Notes |
|------|-------|----------|-------|
| ESP32-DevKitC with ESP32-WROOM-32U | 38-pin, U.FL/IPEX connector | 1 | Must be 32U variant (not 32D) for external antenna |
| 2.4GHz WiFi Antenna with U.FL connector | 3-5dBi gain | 1 | RP-SMA bulkhead or direct U.FL mount |

### Temperature Sensing
| Part | Specs | Quantity | Notes |
|------|-------|----------|-------|
| MAX31855 Thermocouple Amplifier Module | SPI, K-type compatible | 1 | Breakout board version with screw terminals |
| K-type Thermocouple | High-temperature, 1000°F+ rated | 1 | Required for chamber temperature sensing |

### Current Sensing
| Part | Specs | Quantity | Notes |
|------|-------|----------|-------|
| INA219 Current Sensor Module | I2C, 26V, bidirectional, 0.1Ω shunt | 1 | For fan current/voltage/power monitoring |

### Display
| Part | Specs | Quantity | Notes |
|------|-------|----------|-------|
| TM1637 4-Digit LED Display | 0.56" or 0.36" digits, common cathode | 2 | One for current temp, one for setpoint - must be bright for outdoor use |

### User Input
| Part | Specs | Quantity | Notes |
|------|-------|----------|-------|
| KY-040 Rotary Encoder Module | With integrated push button | 1 | For setpoint adjustment and alarm silence |

### Power Management
| Part | Specs | Quantity | Notes |
|------|-------|----------|-------|
| 12V DC Power Supply | 3A minimum (5A recommended) | 1 | Wall adapter or laptop brick style |
| DC-DC Buck Converter Module | 12V input, 5V/2A+ output, adjustable | 1 | LM2596 or MP1584EN based - must support 800mA+ load |

### Fan Control
| Part | Specs | Quantity | Notes |
|------|-------|----------|-------|
| MOSFET Module | IRLZ34N | 1 | For PWM fan control, must handle 12V/3A+ |
| Gate Resistor | 220Ω-1kΩ, 1/4W | 1 | Between ESP32 GPIO and MOSFET gate (may be included on module) |
| Flyback Diode | 1N4007 or equivalent, 1A+ | 1 | Across fan terminals for protection (may be included on module) |

### Alert System
| Part | Specs | Quantity | Notes |
|------|-------|----------|-------|
| Active Piezo Buzzer | 5V, 2-wire (not 3-wire passive) | 1 | Must be active type with internal oscillator |

### Connections & Hardware
| Part | Specs | Quantity | Notes |
|------|-------|----------|-------|
| Screw Terminal Blocks | 2-pin, 5.08mm pitch | 8-10 | For removable connections |
| Perfboard or Prototype PCB | 7x9cm or larger | 1 | For component mounting |
| Dupont Wire Kit | Male/Female/Male-Male, 22-24AWG | 1 kit | Various connections |
| Solid Core Wire | 22AWG, multiple colors | 1 spool set | For perfboard connections |
| Stranded Wire | 18AWG for power, 22AWG for signals | 1 spool set | For external connections |
| Heat Shrink Tubing | Assorted sizes (2mm-10mm) | 1 kit | Wire protection and insulation |
| Wire Ferrules | 22AWG and 18AWG | 1 kit | For stranded wire in screw terminals |
| M3 Hardware Kit | Screws, nuts, standoffs | 1 kit | PCB/display mounting |

### Enclosure
| Part | Specs | Quantity | Notes |
|------|-------|----------|-------|
| 3D Printer Filament | PETG or ABS | ~250g | Heat-resistant material (not PLA) |
| Cable Glands | M12 or M16, IP65 rated | 4-6 | Weatherproof wire entry |
| Neodymium Magnets | 10mm diameter x 2-3mm thick | 6-8 | For mounting to grill body |
| Clear Acrylic Sheet | 2mm thick, for display windows | Small piece | Laser-cut or hand-cut for display protection |

## Optional Components

### Enhanced Monitoring
| Part | Specs | Quantity | Notes |
|------|-------|----------|-------|
| Second MAX31855 Module | SPI, K-type compatible | 1 | For meat probe (future expansion) |
| Additional K-type Thermocouple | Food-grade, probe style | 1 | For meat temperature monitoring |

### Enclosure Cooling
| Part | Specs | Quantity | Notes |
|------|-------|----------|-------|
| 40mm Cooling Fan | 5V, quiet operation | 1 | For hot climates or if ESP32 overheats |

### Testing & Development
| Part | Specs | Quantity | Notes |
|------|-------|----------|-------|
| Breadboard | 830 tie-points | 1 | For initial prototyping |
| Multimeter | DC voltage/current/resistance | 1 | Essential for troubleshooting |
| Logic Analyzer | 8-channel (optional) | 1 | Helpful for debugging I2C/SPI issues |

### Future Upgrades
| Part | Specs | Quantity | Notes |
|------|-------|----------|-------|
| Upgraded Centrifugal Blower | 12V, similar CFM to stock | 1 | Better airflow than stock axial fan |
| Inline Fuse Holder | 5A automotive blade fuse | 1 | Added safety for 12V line |
| TVS Diode | 15V, bidirectional | 1 | Voltage spike protection |

## Original Equipment (Reused from CharGriller 980)

These parts are reused from your existing CharGriller 980:
- **Fan** - 12V axial fan, 3-wire with tachometer (current draw: typically 1-3A)
- **Door switch** - 1x normally closed (NC) switch, closes when lid opens
- **Temperature probe** - K-type thermocouple with aviation-style connector
- **Grill body** - Metal frame for magnetic mounting

## Part Number References

### Recommended Specific Models

| Component | Model/Part Number | Source |
|-----------|-------------------|--------|
| ESP32 Dev Board | ESP32-DevKitC-32U | Espressif official |
| MAX31855 Module | Adafruit 269 or generic breakout | Adafruit or Amazon |
| INA219 Module | Adafruit 904 or generic I2C | Adafruit or Amazon |
| TM1637 Display | Generic 4-digit 0.56" red | Amazon/AliExpress |
| Buck Converter | LM2596 adjustable module | Amazon/AliExpress |
| MOSFET Module | IRLZ34N module | Amazon/AliExpress |

## Notes on Component Selection

### ESP32 Variants
- **Must use ESP32-WROOM-32U** (U.FL connector) for external antenna
- The 32D variant has PCB antenna only and may have poor WiFi range near metal grill
- Verify pin compatibility - some boards have different GPIO layouts

### INA219 Shunt Resistor
- Most modules come with 0.1Ω shunt (3.2A max)
- Verify your module's shunt value in config: `shunt_resistance: 0.1 ohm`
- If fan draws > 3A, consider 0.01Ω shunt module

### TM1637 Display Brightness
- Outdoor readability is critical
- Look for "0.56 inch" displays (larger/brighter than 0.36")
- Red LEDs are typically brightest
- Avoid cheap displays with dim segments

### Buck Converter Efficiency
- LM2596 modules are reliable and efficient (85-92%)
- Ensure converter can handle 1A continuous (2A peak)
- Adjustable output is essential - you'll need to tune to exactly 5V

### MOSFET Module vs Bare MOSFET
- Module includes gate resistor and often flyback diode
- Bare MOSFET requires additional components
- For beginners, module is recommended

---

**License**: MIT (see LICENSE file)  
**Maintainer**: mantellian  
**Last Updated**: December 2024
