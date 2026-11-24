# Char-Griller 980 DIY Controller — PCB Engineering Specification

**Design Goal:** A safe, industrial-grade, highly reliable carrier PCB engineered specifically for high-heat outdoor grilling environments.

---

## 1. Safety & Reliability Features

| Feature | Implementation | Engineering Purpose |
|---|---|---|
| **Power Supply Short Protection** | Upstream 12V adapter foldback cutoff | Relies on power supply's internal short-circuit protection (matches OEM design, eliminates bulky board fuse). |
| **Reverse Polarity Protection** | High-Current Schottky Diode (`D2`) on 12V IN | Blocks reverse voltage if 12V power adapter is wired backwards. |
| **Inductive Kickback Clamping** | 1N4007 Diode (`D1`) across Fan (+) and (-) | Absorbs voltage spikes when fan PWM switches OFF, protecting the MOSFET and ESP32. |
| **MOSFET Gate Fail-Safe** | 10kΩ Pull-Down Resistor (`R2`) on Gate | Forces MOSFET Gate to 0V (Fan OFF) during microcontroller boot, reset, or code crash. |
| **Gate Inrush Limiting** | 220Ω Series Resistor (`R1`) on GPIO16 | Protects ESP32 GPIO16 output driver from capacitive gate inrush currents. |
| **Passive Thermal Management** | 10–15mm magnetic standoff air gap + bottom louvers | No fan needed. Convective draft + standoff air gap shields electronics from hopper heat; prevents ash/soot ingress. |
| **Star Grounding & Copper Pours** | Bottom-layer solid GND plane | High-current fan return (up to 3A) is isolated from sensitive MAX31865 SPI and INA219 I2C ground paths. |
| **Heavy-Duty Trace Geometry** | 2.54mm (100 mil) traces for 12V / Fan / GND | Rated for 5A+ continuous current with < 10°C temperature rise on standard 1oz copper. |
| **Socketed Architecture** | Female headers for all breakout modules | No fragile SMD hand-soldering. If a module fails in the field, pull and replace in seconds. |

---

## 2. Quick-Disconnect Harness Connector (8-Pin, 5.08mm Pitch)

Located along the bottom edge of the board. Uses a **pluggable terminal block** (Phoenix Contact / Euroblock style) with a mating screw-terminal plug:

| Pin # | Net Name | Wire Color | Destination on Grill |
|---|---|---|---|
| **1** | `+12V_RAW` | 🔴 Red (18 AWG) | 12V Power Supply Input (+) |
| **2** | `GND_PWR` | ⚫ Black (18 AWG) | 12V Power Supply Return (-) |
| **3** | `FAN_+12V` | 🔴 Red (18 AWG) | Fan Positive (+) Lead (via INA219) |
| **4** | `FAN_PWM_NEG` | ⚫ Black (18 AWG) | Fan Negative (-) Lead (switched by MOSFET) |
| **5** | `DOOR_SENSE` | 🟡 Yellow (22 AWG) | Hopper Door Switch Harness (GPIO13) |
| **6** | `DOOR_GND` | ⚫ Black (22 AWG) | Hopper Door Switch Harness Ground |
| **7** | `RTD_PROBE_1` | ⚪ White (22 AWG) | OEM PT1000 Probe Wire 1 (to MAX31865 RTD+) |
| **8** | `RTD_PROBE_2` | ⚪ White (22 AWG) | OEM PT1000 Probe Wire 2 (to MAX31865 RTD-) |

> **Quick-Release Operation:** Pulling this single connector unplugs the entire grill harness. The controller can then be lifted off its magnetic mounts and stored indoors in under 5 seconds.

---

## 3. Physical Layout & Ergonomics (Replicating OEM Faceplate)

```
┌────────────────────────────────────────────────────────────────────────┐
│  [M3 / Magnet]                                          [M3 / Magnet]  │
│                                                                        │
│   ┌───────────────┐        ┌─────────────────┐ ┌─────────────────┐     │
│   │               │        │   TM1637 #1     │ │   TM1637 #2     │     │
│   │    KY-040     │        │  CURRENT TEMP   │ │    SETPOINT     │     │
│   │    ROTARY     │        │   (0.56" Red)   │ │   (0.56" Red)   │     │
│   │    ENCODER    │        └─────────────────┘ └─────────────────┘     │
│   │   WITH PUSH   │                                                    │
│   │    BUTTON     │        ───────────────────────────────────────     │
│   └───────────────┘             STATUS: Normal / Alarms / Doors        │
│                                                                        │
│  ────────────────────────────────────────────────────────────────────  │
│                                                                        │
│   [ESP32-DevKitC-32U]      [MAX31865 RTD]   [INA219]    [LM2596 BUCK]  │
│   (Socket Headers)         (6-pin Socket)   (Socket)    (5V Converter) │
│                                                                        │
│            [IRLZ34N MOSFET + Heatsink]    [Active 5V Buzzer]           │
│                                                                        │
│  [M3 / Magnet]   ┌───────────────────────────────────┐  [M3 / Magnet]  │
│                  │  8-PIN PLUGGABLE HARNESS PLUG     │                 │
└──────────────────┴──[ 1 ][ 2 ][ 3 ][ 4 ][ 5 ][ 6 ][ 7 ][ 8 ]─┴─────────────────┘
```

- **Dimensions (Estimated Target)**: ~120mm wide × 85mm high (fits comfortably inside stock rectangular hopper cavity).
- **Front Face**:
  - Encoder knob on **LEFT** (matching Char-Griller OEM dial position).
  - Dual TM1637 4-digit displays on **RIGHT** (side-by-side or stacked).
- **Four Corner Mounts**: 3.2mm holes sized for M3 screws and backed with 10mm × 3mm neodymium disc magnets.

---

## 4. Trace Width & Stackup Rules

| Net Class | Trace Width | Minimum Clearance | Copper Weight |
|---|---|---|---|
| **High Current (12V IN, Fan, Power GND)** | 2.54 mm (100 mil) | 0.50 mm (20 mil) | 1 oz (35 µm) |
| **Medium Current (5V Rail, Buck Output)** | 1.27 mm (50 mil) | 0.35 mm (14 mil) | 1 oz (35 µm) |
| **Low-Voltage Signals (SPI, I2C, GPIO)** | 0.254 mm (10 mil) | 0.254 mm (10 mil) | 1 oz (35 µm) |
| **Ground Plane** | Solid Copper Pour | 0.50 mm (20 mil) | Bottom Layer |
