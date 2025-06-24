
# 🔩 Char-Griller 980 DIY Controller – Bill of Materials (BOM)

**Work in progress!**

---

##  Main Controller

| Item             | Description                          | Notes                                 |
|------------------|--------------------------------------|---------------------------------------|
| ESP32 Dev Board  | Microcontroller                      |      |
| Micro-USB cable  | For power and programming            |            |
| 12V → 5V Buck Converter | Powers ESP32 from grill supply | LM2596 or compact equivalent          |

---

##  Temperature Sensing

| Item                        | Description                    | Notes                                               |
|-----------------------------|--------------------------------|-----------------------------------------------------|
| Thermistor (likely 100k)    | Built-in grill probe           | Will test and reuse existing OEM sensor if possible             |


---

##  Fan Control

| Item              | Description                        | Notes                                      |
|-------------------|------------------------------------|--------------------------------------------|
| 12V Fan           | Existing grill fan (variable speed) | May reuse OEM or upgrade later             |
| Logic-level MOSFET| For PWM fan speed control           |               |
| Flyback Diode     | Protects against voltage spikes     | |
| 220Ω Gate Resistor| Limits current into gate            |                |

---

##  Display + Controls

| Item                       | Description                   | Notes                                               |
|----------------------------|-------------------------------|-----------------------------------------------------|
| 1602 or 2004 LCD (I2C)     | Character LCD display         | OLED? Probably not pratical in directd sunlight        |
| Rotary Encoder Module (KY-040) | For setpoint adjustment  | Optional at first, adds standalone UI control       |

---

##  Inputs (Optional)

| Item               | Description                        | Notes                                 |
|--------------------|------------------------------------|---------------------------------------|
| Magnetic Door Switches | Monitor lid/open state         | Reuse OEM grill switches if possible         |

---

##  Enclosure + Mounting

| Item                      | Description                    | Notes                                 |
|---------------------------|--------------------------------|---------------------------------------|
| 3D Printed Enclosure      | Mounts and protects controller | Design custom case (PETG or ASA)      |
| Weatherproof Connectors   | Cable pass-throughs            | grommets|
| Magnets / Hooks / Screws  | Mounting options               | Removable from grill for storage      |

---

## 📝 Notes
This BOM will be updated as the project evolves.

