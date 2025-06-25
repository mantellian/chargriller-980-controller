# Char-Griller 980 DIY Controller (Work in Progress)

**Work in progress!** This project is still under development, and things will likely break, change, or get scrapped as I go.

The goal here is to build a drop-in replacement controller for my Char-Griller Gravity Fed 980 using an ESP32. The original controller has started randomly shutting off during cooks, sometimes it's fine, other times it dies mid-session. That just doesn't work.

So I'm building my own.

---

## Project Goals

- Work completely on its own  **no Home Assistant required**
- Control a **12V variable-speed fan** using PID logic
- Read the **cook chamber temperature** using the OEM probe (or a compatible one)
- Support the **door switches** (turn off fan if a door is open)
- Show current temp and setpoint on a **local display**
- Use a **physical knob** to adjust the setpoint without needing a phone or computer
- Optionally expose a **web interface** to monitor or change the setpoint
- Optionally integrate with **Home Assistant** using ESPHome
- Live in a **3D printed enclosure** that mounts to the grill (magnets or hanger), and can be brought inside when not in use

---

##  Potential Hardware

| Component                     | Purpose               | Notes                                           |
|------------------------------|-----------------------|-------------------------------------------------|
| ESP32                        | Main controller       | I'm using a QuinLED version I already had       |
| MAX31865 + PT100 RTD          | Temp sensor interface | Will try to reuse the OEM probe if I can        |
| N-channel MOSFET             | Fan PWM control       | IRLZ44N, AO3400, IRF520, etc.                    |
| 12V Fan                      | Fire control          | OEM or something better down the road           |
| Character LCD (1602 or 2004, I2C) | Display          | Better visibility in sunlight and more durable outdoors than OLED |
| Rotary Encoder               | Setpoint adjustment   | KY-040 module or similar                         |
| 12V to 3.3V Buck Converter    | Power for ESP32       | LM2596 or equivalent                             |
| Panel Connectors / Grommets   | Wiring connections    | Will aim for weather resistance                  |
| 3D Printed Enclosure         | Protects everything   | Printed in PETG or ASA                            |


---

## A Note from Me

Just a heads up, I’m not a programmer. I’ve done a few simple ESPHome and Tasmota projects in the past, but nothing this complex. I’m figuring things out as I go and using **AI** to help guide the process.

If you're following along, feel free to fork this repo, open issues, or share suggestions. And if you're better at coding or electronics than I am (which you probably are), feel free to help fix my mistakes.

---

## Current Status

- [ ] Wiring and pinout documentation
- [ ] Firmware prototype
- [ ] 3D printed enclosure design

