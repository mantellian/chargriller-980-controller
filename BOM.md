# CharGriller 980 ESP32 Controller - Bill of Materials (BOM)

## Required Components

### Microcontroller
| Part | Specs | Quantity | Notes |
|------|-------|----------|-------|
| ESP32 Dev Board with External Antenna | U.FL/IPEX connector | 1 | ESP32-DevKitC or similar |
| 2.4GHz WiFi Antenna with U.FL connector | 3-5dBi gain | 1 | RP-SMA or direct U.FL |

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

## Original Equipment (Not Included)

These parts are reused from your existing CharGriller 980:
- Temperature probe (K-type thermocouple)
- Fan (12V, 3-wire with tachometer)
- Door switches (2x, normally closed)
- 12V power supply
