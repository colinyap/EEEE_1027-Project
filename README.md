# EEEE_1027 – Arduino Robot Car

Coursework for the **EEEE1027 Introduction to Engineering** module at the University of Nottingham Malaysia. This repository contains the firmware for a small Arduino-powered robot car, built up across a series of weekly tasks that each explore a different sensor and control technique — line following, IMU-based ramp detection, ultrasonic obstacle avoidance, and Bluetooth remote control.

## Overview

All sketches share the same two-motor differential drive base, driven through an L298N dual H-bridge and controlled by an Arduino Uno. Each task file swaps in a different sensor and control strategy on top of that base, so they are meant to be flashed and run one at a time rather than combined into a single program.

## Hardware

| Component | Details |
|-----------|---------|
| Microcontroller | Arduino Uno (ATmega328P) |
| Motor driver | L298N dual H-bridge |
| Motors | 2× DC gear motors (differential drive) |
| Line sensors | 2–3× IR sensors |
| Speed feedback | Slotted optical encoder, 20 pulses/rev (Week 2 & Task 1) |
| IMU | MPU6050 accelerometer + gyroscope (Task 2) |
| Distance sensor | HC-SR04 ultrasonic (Task 3) |
| Wireless | HC-05 Bluetooth module (Task 4) |
| Display | 16×2 character LCD (parallel, 4-bit mode) |
| Wheels | 65 mm diameter |

## Files

| File | Sensor / Focus | What it does |
|------|----------------|--------------|
| `Project Week 2 Code.ino` | IR + encoder | Base line-follower. Uses two IR sensors for steering (forward / left / right / hard-turn / stop) and an interrupt-driven encoder to compute RPM and cumulative distance, both shown live on the LCD. |
| `Week 3 Task 1.ino` | IR + encoder | Line-follower with a distance trigger. Counts encoder pulses to track distance and stops the car for 2 s once it reaches a set distance along the track, then resumes. |
| `Week 3 Task 2.ino` | MPU6050 IMU | Ramp detection and turn. Reads accelerometer to detect when the car starts climbing a ramp and when it reaches the flat top, pauses, then uses the gyroscope's Z-axis to perform a controlled turn. |
| `Week 3 Task 3.ino` | HC-SR04 ultrasonic | Obstacle avoidance. Continuously measures distance ahead; drives forward normally but turns right when an object comes within ~30 cm. |
| `Week 3 Task 4.ino` | HC-05 Bluetooth | Remote control. Receives single-character commands over Bluetooth (`F`/`B`/`L`/`R`/`0`) to drive forward, back, left, right, or stop, echoing each command to the LCD. |

## How the Line Follower Works (Week 2 & Task 1)

The two IR sensors return `1` on black and `0` on white. Based on the combination, the car drives forward, steers, performs a hard turn at junctions, or stops when it leaves the line. An `onstraight` timer decides when a hard turn should be triggered after a straight run. The encoder fires an interrupt on each slot; from the pulse timing the code derives RPM, and by counting revolutions against the 65 mm wheel circumference it accumulates total distance. An empirical scaling factor is applied so the displayed distance matches the real-world track.

## Getting Started

1. Open the desired `.ino` file in the [Arduino IDE](https://www.arduino.cc/en/software).
2. Install any required libraries:
   - **LiquidCrystal** (bundled with the IDE) — all tasks with an LCD.
   - **Adafruit MPU6050**, **Adafruit Unified Sensor**, **Wire** — Task 2.
   - **SoftwareSerial** (bundled) — Task 4.
3. Select **Arduino Uno** as the board and the correct COM port.
4. Wire the hardware to match the pin definitions at the top of that file.
5. Upload, place the car on the track (or pair the HC-05 for Task 4), and power the motors.

## Notes and Calibration

- **Pin assignments differ between files** — motor and sensor pins were remapped as different sensors were added (e.g. `IN1` moves between A5 and pin 2). Always check the constants at the top of a sketch before wiring.
- **IR polarity differs too** — the sensor combination that means "on the line" is not identical across the line-following files; verify the conditions in `drive()` against your own sensors.
- Motor speed constants (`FAST`, `WALK`, `SLOW`, and the raw `analogWrite` values) can be tuned to suit your track surface and battery voltage.
- Distance and rotation readings rely on empirical scaling factors — re-tune them for your wheel size, encoder, and IMU mounting.
- Task 2 and Task 4 use different serial baud rates (115200 and 9600 respectively); set the Serial Monitor accordingly.

## Author

**Colin Yap** — Electrical & Electronic Engineering, University of Nottingham Malaysia.
