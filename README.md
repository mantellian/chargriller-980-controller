# Char-Griller 980 DIY Controller

**🔨 Work in Progress** - Actively under development. Expect changes.

## Why This Exists

My Char-Griller Gravity Fed 980's original controller started randomly dying mid-cook. After some research, I discovered the OEM replacement controller has been discontinued, and the aftermarket options like Fireboard are over $300. 

For that price, I can build something better with more features, better monitoring, and full control over the code.

This project is my solution: an ESP32-based controller with PID control, web interface, Home Assistant integration, and none of the cloud dependencies of commercial units.

---

## Project Goals

### Core Requirements
- **Standalone operation** - Works without internet or Home Assistant
- **PID temperature control** - Maintains setpoint within ±2°F using proper PID algorithm
- **K-type thermocouple** - Reads chamber temp using OEM probe via MAX31855
- **Door safety interlocks** - Fan stops when doors open, with manual override option
- **Dual LED displays** - Always visible current temp and setpoint (outdoor-bright TM1637)
- **Physical rotary encoder** - Adjust setpoint without phone/computer
- **One-button control** - Encoder button for alarm silence and setpoint confirmation
- **Fan failure detection** - Monitors current draw via INA219, alerts if fan stops
- **Audible alarms** - SOS Morse code pattern for critical alerts

### Advanced Features
- **Web interface** - Built-in ESPHome web server for monitoring and control
- **Home Assistant integration** - Full API integration with sensors and controls
- **PID autotune** - Automated Ziegler-Nichols relay method tuning
- **Temperature alarms** - High/low temp alerts with 3-minute delay
- **WiFi fallback** - Creates its own AP if home network unavailable
- **OTA updates** - Update firmware wirelessly

### Safety Features
- **Emergency shutdown** - Instantly kills fan and system
- **Door interlocks** - Fan stops when either door opens (configurable)
- **Fan failure detection** - Detects fan stoppage via current monitoring
- **Over-temperature protection** - Emergency shutdown at 800°F
- **Alarm system** - Persistent SOS alerts for critical conditions

---

## Hardware Overview

### Main Components

| Component | Purpose | Interface |
|-----------|---------|-----------|
| ESP32-WROOM-32U | Main controller | WiFi with external antenna |
| MAX31855 | Thermocouple amplifier | SPI (GPIO 18, 19, 14) |
| INA219 | Current sensor | I2C (GPIO 21, 17) |
| 2x TM1637 | 4-digit LED displays | GPIO 23/26 and 25/22 |
| KY-040 | Rotary encoder with button | GPIO 32, 33, 27 |
| IRLZ34N MOSFET | Fan PWM driver | GPIO 16 |
| LM2596 Buck | 12V→5V converter | Power supply |
| Active Buzzer | Alert system | GPIO 4 |
| Door Switch | Safety interlock | GPIO 13 |

**See [BOM.md](BOM.md) for complete parts list with specs and sourcing.**

**See [wiring_chart.md](wiring_chart.md) for detailed wiring diagrams and connections.**

---

## Features Breakdown

### Temperature Control
- **PID Algorithm**: Proportional-Integral-Derivative control for stable temperature
- **Auto-tuning**: Ziegler-Nichols relay method calculates optimal PID values
- **Range**: 175°F to 750°F in 5° increments
- **Deadband**: Reduces fan oscillation when within ±2°F of setpoint
- **Integral windup protection**: Prevents runaway integral accumulation

### Display System
**Current Temperature Display (Left):**
- Normal: Shows current pit temp (e.g., `225`)
- Off: Shows ` OFF`
- Error states: `Err` (fan error), `oPEn` (door open), `  HI` (high temp), `  Lo` (low temp)

**Setpoint Display (Right):**
- Normal: Shows target temp (e.g., `225`)
- Adjusting: Shows pending value while rotating encoder
- Off: Shows ` OFF`
- Error states: `FAn` (fan error), `door` (door open), `tEmP` (temp alarm)

### Rotary Encoder Operation
- **Rotate**: Adjust setpoint in 1° increments (175-750°F range)
- **Auto-confirm**: Setpoint locks in after 5 seconds of no movement
- **Button press**: Immediately confirms setpoint OR silences active alarms

### Alarm System
**SOS Pattern** (International distress signal):
```
● ● ●   ▬ ▬ ▬   ● ● ●
(S)     (O)     (S)
```

**Alarm Triggers:**
- **Fan Error**: Fan should be running but current < 0.1A (detected immediately)
- **High Temp**: > 50°F above setpoint for 3+ minutes
- **Low Temp**: < 50°F below setpoint for 3+ minutes

**Alarm Actions:**
- Press encoder button to silence
- Use "Silence Alarm" button in web interface
- Disable all alarms via "Alarm Enabled" switch

### Door Safety
**Normal Mode:**
- Door opens → Fan stops immediately, display shows `oPEn` / `door`
- Door closes → Fan resumes automatically

**Future Enhancement:**
- Door override feature planned for extended door-open operations

### Web Interface Features
**Main Controls (sorted by priority):**
1. Current Temperature - Real-time chamber temp reading
2. Target Temperature - Adjustable slider (175-750°F)
3. Power - On/off toggle for entire system
4. Silence Alarm - Quick button to stop buzzer
5. Alarm Enabled - Master switch for all alarms

**Diagnostics:**
- Fan Current - Real-time current draw (A)
- Door Switch - Open/closed status
- Fan Error - Active/inactive status
- High Temp Alarm - Active/inactive status
- Low Temp Alarm - Active/inactive status
- WiFi Signal - Connection strength

**PID Tuning:**
- PID Kp - Proportional gain (adjustable 0-10)
- PID Ki - Integral gain (adjustable 0-1)
- PID Kd - Derivative gain (adjustable 0-10)
- Start PID Autotune - Begin automatic tuning
- Stop PID Autotune - Cancel tuning process

---

## Current Status

### Completed 
- [x] Requirements definition
- [x] Component selection and BOM
- [x] Complete ESPHome configuration
- [x] GPIO pinout documentation
- [x] PID control algorithm with autotune
- [x] All display logic and error codes
- [x] Alarm system with SOS pattern
- [x] Fan failure detection
- [x] Door safety interlocks
- [x] Web interface with organized controls

### In Progress 🔨
- [ ] Thermocouple probe verification (need to confirm K-type)
- [ ] Perfboard prototype assembly
- [ ] Initial PID tuning values validation
- [ ] Field testing without food
- [ ] 3D printed enclosure design

### Planned 📋
- [ ] Enclosure with magnetic mounting
- [ ] Long-duration cook testing (8+ hours)
- [ ] Manual fan override mode (for startup/cleaning)
- [ ] Door safety override option
- [ ] Second probe for meat temperature (future v2.0)
- [ ] Historical data logging and graphing

---

## Quick Start Guide

### Prerequisites
- ESP32 flashed with ESPHome
- All components wired per [WIRING.md](WIRING.md)
- WiFi credentials configured in `secrets.yaml`
- 12V power supply connected

### First Boot
1. Power on 12V supply
2. ESP32 boots and attempts WiFi connection
3. If WiFi fails, AP "CharGriller-980" appears (password: `chargriller980`)
4. Access web interface at `http://chargriller-980.local`
5. Verify all sensors show reasonable values

### Basic Operation
1. **Turn on**: Toggle "Power" switch in web interface or wait for it to restore state
2. **Set temperature**: Use "Target Temperature" slider or rotate physical encoder
3. **Monitor**: Watch LED displays for current temp (left) and setpoint (right)
4. **Door opens**: Fan stops automatically, displays show door status
5. **Alarms**: Press encoder button or use web interface to silence

### PID Autotune Procedure
1. Start with charcoal lit and stabilized around 200-250°F
2. Set target temperature (e.g., 225°F)
3. Press "Start PID Autotune" in web interface
4. Wait 20-30 minutes while system oscillates temperature
5. When complete, new Kp/Ki/Kd values automatically applied and saved
6. Test for 1-2 hours before trusting on real cook

**Autotune Progress:**
- Watch logs for "Peak high" and "Peak low" messages
- System needs 8 peaks (4 complete cycles) to calculate values
- It will timeout after 30 minutes if unable to complete
- Can manually stop with "Stop PID Autotune" button

---

## Disclaimer

**READ THIS BEFORE BUILDING**

This is an experimental project, not a certified safety device. Build and use at your own risk. This project involves:
- **Managing fire:** Malfunctions could lead to overheating, property damage, or fire. Never leave the grill unattended, especially during initial testing.
- **Electrical components:** Risk of shock or fire from shorts. Use proper fusing and verify all connections.
- **Food safety:** Do not trust for overnight cooks until thoroughly tested. Use a backup thermometer to prevent undercooking.

**The author is NOT responsible for:**
- Damage to your grill, property, or equipment
- Injury resulting from use of this project
- Food spoilage or foodborne illness
- Fires or safety incidents
- Any other damages or losses

Always follow proper electrical safety practices and local codes.

---

## Version History

### v0.2 (Current - December 2024)
- Complete ESPHome configuration with all features
- PID control with Ziegler-Nichols autotune
- All alarm systems functional (fan, high temp, low temp)
- SOS morse code buzzer pattern
- Door safety interlocks
- Web interface with organized control groups
- Complete documentation (BOM, wiring, README)

### Planned v1.0 (Target: Q1 2025)
- Working PID control (field tested on 5+ cooks)
- Complete 3D printed enclosure with magnetic mounting
- Verified through multiple successful long-duration cooks
- Refined PID values for optimal performance
- Complete build guide with photos

### Future v2.0
- Second thermocouple for meat probe
- Door safety override option

---

**Maintainer**: [mantellian](https://github.com/mantellian)  
**Project Status**: Alpha/Testing - Use at your own risk  
  

