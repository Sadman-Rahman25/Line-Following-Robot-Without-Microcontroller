# Line Following Robot — Without Microcontroller

A fully **analog** line-following robot built using IR proximity sensors, **LM358 op-amp comparators**, and an **L293D H-bridge motor driver** — *no microcontroller, no code*. The robot detects a black line on a white surface and corrects its path in real time using purely analog signal processing.

> Built as part of the **CSE260 (Electronic Devices and Circuits)** lab course at BRAC University.


## How It Works

The robot has two IR sensor modules mounted on the front, one on each side of the line. Each module emits IR light and reads the reflected signal:

- **White surface** → strong reflection → high IR receiver current → comparator output **LOW**
- **Black line** → weak reflection → low IR receiver current → comparator output **HIGH**

The two comparator outputs feed directly into the L293D motor driver enable/direction pins, producing the following behavior:

| Left Sensor | Right Sensor | Action |
|---|---|---|
| White (line not detected) | White (line not detected) | Both motors forward — go straight |
| **Black (on line)** | White | Stop right motor — turn right |
| White | **Black (on line)** | Stop left motor — turn left |
| Black | Black | Both motors stop — line lost / intersection |

No code, no MCU, no PWM — the path correction logic is hardwired in analog electronics.

## Bill of Materials (BOM)

| Component | Qty | Purpose |
|---|---|---|
| BO Geared Motor 60 RPM | 2 | Drive wheels |
| Rubber wheel (matching BO motor) | 2 | Locomotion |
| Caster wheel / free wheel | 1 | Rear balance |
| IR TX (emitter) LED | 2 | Emit IR signal |
| IR RX (photodiode/phototransistor) | 2 | Detect reflected signal |
| **LM358** dual op-amp IC | 1 | Comparator for sensor thresholding |
| **L293D** motor driver IC | 1 | H-bridge to drive both motors |
| **7805** voltage regulator | 1 | Regulate 9V → 5V for logic |
| 10 kΩ trimpot (preset) | 2 | Adjust IR threshold sensitivity |
| Resistor 100 Ω | 2 | IR emitter current limiting |
| Resistor 10 kΩ | 2 | IR receiver bias |
| Resistor 220 Ω | 2 | Status LED current limiting |
| LED | 2 | Visual indicator of sensor state |
| Slide / toggle switch | 1 | Power switch |
| 9V battery + connector | 1 | Power supply |
| Chassis board, perfboard, jumper wires | — | Mounting + interconnect |
| 40-pin header strip | 1 | Connectors |

## Circuit Description

Each IR sensor module follows the same pattern:
- The IR emitter is biased through a **100 Ω** resistor from VCC
- The IR receiver and **10 kΩ** pull-up form a voltage divider; the divider output is the comparator's inverting (−) input
- A **10 kΩ trimpot** sets the reference voltage on the non-inverting (+) input — this is your sensitivity adjustment
- The **LM358** outputs LOW when reflected IR is strong (white surface), HIGH when reflected IR is weak (black line)
- Each comparator output drives one input of the **L293D** and a status LED through a **220 Ω** resistor

The L293D's `1,2EN` and `3,4EN` pins are tied high to permanently enable both H-bridges. The two BO motors are connected to the L293D's output pairs.

The **7805** regulates the 9V battery down to 5V to safely power the LM358 and the L293D's logic side, while the motor side runs directly off the 9V rail.

## Why No Microcontroller?

This was the design constraint of the lab — to demonstrate understanding of:
- Op-amp comparator behavior
- Voltage dividers and biasing
- H-bridge motor control
- Voltage regulation
- Analog signal conditioning

The result is a robot that responds in real time with essentially **zero latency** (no software loop overhead) and runs on minimal power.

## Calibration

1. Power the robot on a flat surface with a black line
2. Place a sensor over white and adjust its trimpot until the indicator LED just turns off
3. Move the same sensor over the black line — the LED should turn on
4. Repeat for the second sensor
5. Verify motor response by manually shading each sensor

## Files

```
.
├── README.md
├── components_labeled.png   # Labeled photo of all components
├── schematic.jpg            # Circuit schematic (EasyEDA)
└── build_photo.jpg          # Assembled robot
```

## Future Improvements

- Add a third center sensor for finer alignment when on the line
- Use Schmitt-trigger comparators (LM393 + hysteresis) to reduce false transitions at line edges
- Replace IR proximity modules with TCRT5000 line sensors for cleaner signals
- Add PWM speed control via a 555-timer or NE556 (still microcontroller-free) for smoother turns

## Course Context

**Course:** CSE260 — Electronic Devices and Circuits
**Institution:** BRAC University, Dhaka, Bangladesh

## Author

**S. M. Sadman Rahman** — [GitHub](https://github.com/Sadman-Rahman25) · [LinkedIn](https://www.linkedin.com/in/sadman-rahman-198a88284)

## License

MIT License — see `LICENSE` file for details.
