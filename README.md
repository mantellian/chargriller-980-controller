# Char-Griller 980 DIY Controller (Work in Progress)

**Work in progress!** This project is still under development, and things will likely break, change, or get scrapped as I go.

The goal here is to build a drop-in replacement controller for my Char-Griller Gravity Fed 980 using an ESP32. The original controller has started randomly shutting off during cooks—sometimes it's fine, other times it dies mid-session. That just doesn't work.

So I'm building my own.

---

## Project Goals

- Work completely on its own—**no Home Assistant required**
- Control a **12V variable-speed fan** using PID logic
- Read the **cook chamber temperature** using the OEM probe (K-type thermocouple)
- Support the **door switches** (turn off fan if a door is open, with override option)
- Show current temp and setpoint on **dual LED displays** (bright enough for outdoor use)
- Use a **physical rotary encoder** to adjust the setpoint without needing a phone or computer
- **Long-press button** to enable/disable PID control
- **Fan failure detection** with audible alert
- Expose a **web interface** for monitoring and control
- Full **Home Assistant integration** using ESPHome
- Live in a **3D printed enclosure** that mounts to the grill (magnets), and can be brought inside when not in use

---

## Hardware

### Final Component List

| Component | Purpose | Notes |
|-----------|---------|-------|
| ESP32 Dev Board with External Antenna | Main controller | U.FL/IPEX connector for better WiFi range |
| 2.4GHz WiFi Antenna | WiFi connectivity | 3-5dBi gain, U.FL connector |
| MAX31855 Thermocouple Amplifier | Temp sensor interface | Works with stock K-type thermocouple probe |
| 2x TM1637 4-Digit LED Display | Display | One for current temp, one for setpoint - bright for outdoor use |
| KY-040 Rotary Encoder | Setpoint adjustment | With integrated push button for door override |
| Momentary Push Button | PID enable/disable | Long-press to toggle |
| MOSFET Module | Fan PWM control | IRF520 or similar, handles 12V fan |
| Active Piezo Beeper | Alert system | 5V beeper with SOS morse code alert pattern |
| 12V to 5V Buck Converter | Power for ESP32 | LM2596 or MP1584 based, 2A+ output |
| OEM 12V Fan | Fire control | 3-wire with tachometer feedback |
| OEM Temperature Probe | Chamber temp sensing | K-type thermocouple, reused from original |
| OEM Door Switches | Safety interlocks | 2x switches wired in series |
| Screw Terminal Blocks | Connections | For removable wiring |
| 3D Printed Enclosure | Housing | PETG or ABS, magnet-mounted |

See [BOM.md](BOM.md) for complete parts list with specs and sourcing recommendations.

---

## Features

### Core Functionality
- **Standalone operation** - No internet or Home Assistant required for basic operation
- **PID temperature control** - Maintains setpoint within ±2°F
- **Temperature range** - 150°F to 700°F in 5° increments
- **Door safety** - Automatically stops fan when door opens
- **Door override** - Press rotary button to disable door safety during extended door-open operations
- **Fan monitoring** - Detects fan failure and alerts via buzzer and display error code (ErF)
- **Physical controls** - Rotary encoder for setpoint, button for PID on/off

### Interface Options
- **Dual LED displays** - Always-visible current temp and setpoint
- **Web server** - Built-in ESPHome web interface accessible from any device on the network
- **Home Assistant** - Full integration with sensors, controls, and automations
- **Fallback AP** - Creates its own WiFi network if home network unavailable

### Safety Features
- **Door interlocks** - Fan stops when either door opens (unless overridden)
- **Fan failure detection** - Monitors tachometer signal, alerts if fan stops unexpectedly
- **Temperature limits** - Software enforced min/max temperatures
- **PID deadband** - Prevents oscillation and excessive fan cycling

---

## A Note from Me

Just a heads up, I'm not a programmer. I've done a few simple ESPHome and Tasmota projects in the past, but nothing this complex. I'm figuring things out as I go and using **AI** to help guide the process.

If you're following along, feel free to fork this repo, open issues, or share suggestions. And if you're better at coding or electronics than I am (which you probably are), feel free to help fix my mistakes.

---

## Current Status

- [x] Requirements definition
- [x] Component selection
- [x] Bill of materials
- [x] ESPHome configuration (initial version)
- [ ] Probe type verification
- [ ] GPIO pinout documentation
- [ ] Perfboard prototype build
- [ ] PID tuning
- [ ] Display implementation (TM1637)
- [ ] 3D printed enclosure design
- [ ] Field testing
- [ ] Documentation completion

---

## Contributing

This is a personal project, but contributions are welcome! If you:
- Find bugs or issues
- Have suggestions for improvements
- Want to contribute code or documentation
- Build your own version and have feedback

Please open an issue or submit a pull request.

---

## License

MIT License - See [LICENSE](LICENSE) file for details

---

## Disclaimer

This project involves working with electrical components and modifying a cooking appliance. Build and use at your own risk. The author is not responsible for any damage, injury, or burnt brisket that may result from using this project.

Always follow proper electrical safety practices and local codes.

---

**Maintainer**: [mantellian](https://github.com/mantellian)  
**Last Updated**: November 2025.
