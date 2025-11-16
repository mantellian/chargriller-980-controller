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
| MAX31855 Thermocouple Amplifier Module | SPI, K-type compatible | 1 | Breakout board version |

### Display
| Part | Specs | Quantity | Notes |
|------|-------|----------|-------|
| TM1637 4-Digit LED Display | 0.56" or 0.36" digits | 2 | One for current temp, one for setpoint |

### User Input
| Part | Specs | Quantity | Notes |
|------|-------|----------|-------|
| KY-040 Rotary Encoder | With push button | 1 | For setpoint adjustment |
| Momentary Push Button | SPST, normally open | 1 | For PID enable/disable |

### Power Management
| Part | Specs | Quantity | Notes |
|------|-------|----------|-------|
| DC-DC Buck Converter | 12V input, 5V/2A+ output | 1 | LM2596 or MP1584 based |

### Fan Control
| Part | Specs | Quantity | Notes |
|------|-------|----------|-------|
| MOSFET Module | IRF520 or similar, handles 12V/2A+ | 1 | For PWM fan control |

### Alert System
| Part | Specs | Quantity | Notes |
|------|-------|----------|-------|
| Passive Buzzer | 5V piezo buzzer | 1 | For audible alerts |

### Connections & Hardware
| Part | Specs | Quantity | Notes |
|------|-------|----------|-------|
| Screw Terminal Blocks | 2-pin, 5.08mm pitch | 5-6 | For removable connections |
| Perfboard | 7x9cm or larger | 1 | For prototyping |
| Dupont Wire Kit | Male/Female/Male-Male | 1 kit | Various connections |
| Heat Shrink Tubing | Assorted sizes | 1 kit | Wire protection |
| M3 Hardware Kit | Screws, nuts, standoffs | 1 kit | PCB/display mounting |

### Enclosure
| Part | Specs | Quantity | Notes |
|------|-------|----------|-------|
| 3D Printer Filament | PETG or ABS | ~200g | Heat-resistant material |
| Cable Glands | M12 or M16 | 4-5 | Weatherproof wire entry |
| Neodymium Magnets | 10mm diameter x 2mm | 4-6 | For mounting to grill |

## Optional Components

### Enclosure Cooling
| Part | Specs | Quantity | Notes |
|------|-------|----------|-------|
| 40mm Cooling Fan | 5V, quiet operation | 1 | For hot climates |

### Future Fan Upgrade
| Part | Specs | Quantity | Notes |
|------|-------|----------|-------|
| Centrifugal Blower Fan | 12V, similar CFM to stock | 1 | Better than stock axial fan |

## Original Equipment (Not Included)

These parts are reused from your existing CharGriller 980:
- Temperature probe (K-type thermocouple)
- Fan (12V, 3-wire with tachometer)
- Door switches (2x, normally closed)
- 12V power supply

---

**Last Updated**: November 2025  
**License**: MIT (see LICENSE file)  
**Maintainer**: mantellian
