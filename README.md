# Char-Griller 980 DIY Controller

**🔨 Work in Progress** - Actively under development. Expect changes.

## Why This Exists

My Char-Griller Gravity Fed 980's original controller started randomly dying mid-cook. After some research, I discovered the OEM replacement controller has been discontinued, and the aftermarket options like Fireboard are $300+. 

For that price, I can build something better—with more features, better monitoring, and full control over the code.

This project is my solution: an ESP32-based controller with PID control, web interface, Home Assistant integration, and none of the cloud dependencies of commercial units.

---

## Project Goals

### Core Requirements
- ✅ **Standalone operation** - Works without internet or Home Assistant
- ✅ **PID temperature control** - Maintains setpoint within ±2°F using proper PID algorithm
- ✅ **K-type thermocouple** - Reads chamber temp using OEM probe via MAX31855
- ✅ **Door safety interlocks** - Fan stops when doors open, with manual override option
- ✅ **Dual LED displays** - Always-visible current temp and setpoint (outdoor-bright TM1637)
- ✅ **Physical rotary encoder** - Adjust setpoint without phone/computer
- ✅ **One-button control** - Encoder button for alarm silence and setpoint confirmation
- ✅ **Fan failure detection** - Monitors current draw via INA219, alerts if fan stops
- ✅ **Audible alarms** - SOS morse code pattern for critical alerts

### Advanced Features
- ✅ **Web interface** - Built-in ESPHome web server for monitoring and control
- ✅ **Home Assistant integration** - Full API integration with sensors and controls
- ✅ **PID autotune** - Automated Ziegler-Nichols relay method tuning
- ✅ **Temperature alarms** - High/low temp alerts with 3-minute delay
- ✅ **WiFi fallback** - Creates its own AP if home network unavailable
- ✅ **OTA updates** - Update firmware wirelessly

### Safety Features
- ✅ **Emergency shutdown** - Instantly kills fan and system
- ✅ **Door interlocks** - Fan stops when either door opens (configurable)
- ✅ **Fan failure detection** - Detects fan stoppage via current monitoring
- ✅ **Over-temperature protection** - Emergency shutdown at 800°F
- ✅ **Alarm system** - Persistent SOS alerts for critical conditions

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
| IRF520 MOSFET | Fan PWM driver | GPIO 16 |
| LM2596 Buck | 12V→5V converter | Power supply |
| Active Buzzer | Alert system | GPIO 4 |
| Door Switch | Safety interlock | GPIO 13 |

**See [BOM.md](BOM.md) for complete parts list with specs and sourcing.**

**See [WIRING.md](WIRING.md) for detailed wiring diagrams and connections.**

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

### Completed ✅
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
- Will timeout after 30 minutes if unable to complete
- Can manually stop with "Stop PID Autotune" button

---

## Safety Warnings

⚠️ **CRITICAL SAFETY INFORMATION** ⚠️

### Fire Hazards
- This controller manages **FIRE** - malfunction could cause overheating or fire
- **Never leave unattended** during initial testing
- Keep fire extinguisher nearby during testing
- This is a DIY device - **no safety certifications**

### Electrical Safety
- 12V system is relatively safe, but shorts can still cause fires
- Use proper fuses on 12V line
- Verify all connections before powering on
- Keep electronics away from water/grease

### Food Safety
- **Do not trust for overnight cooks** until thoroughly tested
- Use backup temperature monitoring during initial testing
- Low temps (< 140°F) can allow bacterial growth
- High temps can ruin food or cause flare-ups

### Emergency Procedures
- **If fire spreads**: Use fire extinguisher, not water
- **If electronics smoke**: Cut power immediately
- **If temp runs away**: Cut power and open doors
- **If alarm sounds**: Investigate immediately, don't just silence

### Recommendations
- Test with **cheap/expendable food first** (hot dogs, not brisket)
- Always use a **backup thermometer** during initial testing
- Start with **short 2-3 hour cooks** before attempting long cooks
- Monitor actively for first 5-10 cooks
- Consider adding a **physical temperature limit switch** as failsafe

---

## Technical Details

### PID Controller
The system uses a discrete PID controller running at 1Hz (every second):

```
output = (Kp × error) + (Ki × Σerror) + (Kd × Δerror)
```

- **Kp** (Proportional): Reacts to current error
- **Ki** (Integral): Eliminates steady-state error
- **Kd** (Derivative): Dampens oscillation

**Default Values:**
- Kp = 0.3
- Ki = 0.005
- Kd = 0.8

These can be adjusted via web interface or overwritten by autotune.

### Autotune Algorithm
Uses Ziegler-Nichols relay method:
1. Applies relay control (bang-bang) around setpoint
2. Measures oscillation period (Tu) and amplitude
3. Calculates ultimate gain (Ku)
4. Derives PID values:
   - Kp = 0.6 × Ku
   - Ki = 1.2 × Ku / Tu
   - Kd = 0.075 × Ku × Tu

### Alarm Logic
**Fan Error:**
- Triggers when: Duty cycle > 10% AND current < 0.1A
- Action: Immediate alarm (no delay)
- Clears when: Current returns to > 0.1A

**High/Low Temperature:**
- Triggers when: > 50°F deviation from setpoint
- Delay: 3 minutes (180 seconds) continuous deviation
- Clears when: Returns within 25°F of setpoint
- Action: SOS alarm, repeats every 3 seconds

### Power Requirements
- **ESP32**: ~500mA @ 5V (peaks during WiFi)
- **Displays**: ~200mA @ 5V (both, full brightness)
- **Sensors**: ~3mA @ 3.3V (MAX31855 + INA219)
- **Fan**: 1-3A @ 12V (varies with duty cycle)
- **Total**: ~4A @ 12V (worst case)

---

## A Note from the Builder

I'm not a professional programmer or electrical engineer. I'm a DIY enthusiast learning as I go, with significant help from AI tools and the ESPHome community.

**If you're following along:**
- Expect bugs and issues
- Test thoroughly before trusting with food
- Fork, modify, and improve as you see fit
- Share your experiences via issues or pull requests

**If you're better at this than me** (which you probably are):
- Help fix my mistakes
- Suggest improvements
- Contribute better solutions

This is a personal project shared in the spirit of open source. Use at your own risk.

---

## Contributing

Contributions welcome! Here's how you can help:

### Bug Reports
- Open an issue with detailed description
- Include relevant log output from ESPHome
- Describe expected vs actual behavior
- Include ESPHome version and hardware details

### Feature Requests
- Check existing issues first
- Explain the use case clearly
- Consider if it fits the project scope
- Be specific about desired behavior

### Code Contributions
- Fork the repository
- Create a feature branch
- Test your changes thoroughly
- Submit a pull request with clear description
- Follow ESPHome YAML style conventions

### Documentation
- Fix typos or unclear sections
- Add photos of your build
- Share PID tuning values that worked for you
- Write guides for beginners
- Translate to other languages

---

## Troubleshooting

### ESP32 Won't Boot
- Check 5V power supply voltage
- Verify GPIO12 is not pulled HIGH
- Disconnect peripherals and test ESP32 alone
- Check for solder bridges on board

### Temperature Reading Issues
- **Shows -127°C**: Thermocouple not connected
- **Shows 1000°C+**: Thermocouple reversed polarity or short
- **Erratic readings**: EMI interference, twist thermocouple wires
- **Consistent offset**: Cold junction compensation issue

### Display Problems
- **No display**: Check 5V power, verify CLK/DIO connections
- **Dim display**: Normal - TM1637 auto-dims, can be adjusted in code
- **Wrong numbers**: Check if CLK/DIO are swapped between displays
- **Flickering**: Power supply noise, add 100µF capacitor

### Fan Issues
- **Won't run**: Check 12V supply, verify MOSFET connections, test gate voltage
- **Runs constantly**: MOSFET may be shorted, check for proper PWM signal
- **Erratic speed**: Poor PWM frequency, check wiring for noise
- **False fan errors**: Adjust current threshold in code (currently 0.1A)

### WiFi Problems
- **Won't connect**: Check credentials in secrets.yaml
- **Weak signal**: Verify external antenna connected to U.FL
- **Drops frequently**: Power supply noise, add filtering capacitors
- **Can't find device**: Try IP address instead of .local name

### Alarm Malfunctions
- **Won't stop**: Check encoder button wiring (GPIO27)
- **Too sensitive**: Adjust thresholds in config (currently 50°F, 3 minutes)
- **Doesn't sound**: Verify buzzer is active type (not passive)
- **Wrong pattern**: Check SOS script timing

---

## License

MIT License - See [LICENSE](LICENSE) file for details.

You are free to:
- ✅ Use this project personally or commercially
- ✅ Modify and distribute
- ✅ Build and sell controllers based on this design

Please:
- ✅ Keep the license notice
- ✅ Don't hold me liable for burnt brisket
- ✅ Share improvements back with the community

---

## Disclaimer

**READ THIS BEFORE BUILDING**

This project involves:
- **Electrical components** - Risk of shock, fire, or equipment damage
- **Modifying a cooking appliance** - Risk of fire, property damage, or injury
- **Managing fire** - Risk of uncontrolled combustion
- **Food safety** - Risk of undercooking or spoilage

**The author is NOT responsible for:**
- ❌ Damage to your grill, property, or equipment
- ❌ Injury resulting from use of this project
- ❌ Food spoilage or foodborne illness
- ❌ Fires or safety incidents
- ❌ Any other damages or losses

**Build and use at your own risk.** Always follow proper electrical safety practices and local codes. Never leave fire unattended. This is an experimental project, not a certified safety device.

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
**Last Updated**: December 2025  

