# EEEE_1027 – Line-Following Robot Car

Arduino-based autonomous line-following robot developed for the **EEEE1027 Introduction to Engineering** module at the University of Nottingham Malaysia. The car follows a black line using infrared sensors, drives a two-motor differential base through an L298N driver, and reports live distance and elapsed time on a 16×2 LCD via a wheel encoder.

## Overview

The robot reads two IR sensors to stay on the track, applies a simple state-based steering logic (forward / left / right / hard-turn / stop), and uses an interrupt-driven optical encoder to estimate wheel RPM and cumulative distance travelled. Distance and time are shown on the LCD in real time. Later task files extend this core with behaviours such as stopping at a set distance along the track.

## Hardware

| Component | Details |
|-----------|---------|
| Microcontroller | Arduino Uno (ATmega328P) |
| Motor driver | L298N dual H-bridge |
| Motors | 2× DC gear motors (differential drive) |
| Line sensors | 2× IR sensors (left / right), 1× middle IR (reserved) |
| Speed feedback | Slotted optical encoder, 20 pulses/rev |
| Display | 16×2 character LCD (parallel, 4-bit mode) |
| Wheels | 65 mm diameter |

## Pin Mapping

| Function | Pin |
|----------|-----|
| Motor IN1 / IN2 (left) | A5 / A2 |
| Motor IN3 / IN4 (right) | 12 / 13 |
| Motor enable ENA / ENB (PWM) | 3 / 11 |
| IR left / right / middle | A3 / A4 / A1 |
| Encoder (interrupt) | 2 |
| LCD (RS, E, D4–D7) | 8, 9, 4, 5, 6, 7 |

*Note: pin assignments and IR line-detection polarity vary slightly between task files — check the top of each `.ino` before flashing.*

## Files

| File | Description |
|------|-------------|
| `Project Week 2 Code.ino` | Base line-follower with encoder-based distance and time display on the LCD. |
| `Week 3 Task 1.ino` | Adds a distance trigger — stops for 2 s once the car reaches a set distance (~4.1 m), then resumes. |
| `Week 3 Task 2.ino` | Task 2 line-following variant. |
| `Week 3 Task 3.ino` | Task 3 line-following variant. |
| `Week 3 Task 4.ino` | Task 4 line-following variant. |

## How It Works

**Line following.** The two IR sensors return `1` on black and `0` on white. Depending on the combination, the car drives forward, steers left/right, performs a hard turn at junctions, or stops when both sensors leave the line. A short timer (`onstraight`) is used to decide when a hard turn should be triggered after a straight run.

**Speed and distance.** The encoder fires an interrupt on each slot. From the time between pulses the code computes RPM, and by counting revolutions against the wheel circumference (65 mm) it accumulates total distance. A calibration factor is applied to match measured real-world distance.

**Display.** The LCD continuously shows the estimated distance travelled and the elapsed run time in seconds.

## Getting Started

1. Open the desired `.ino` file in the [Arduino IDE](https://www.arduino.cc/en/software).
2. Ensure the **LiquidCrystal** library is available (bundled with the IDE).
3. Select **Arduino Uno** as the board and the correct COM port.
4. Wire the hardware to match the pin table at the top of that file.
5. Upload, place the car on the track, and power the motors.

## Calibration Notes

- Distance readings use an empirical scaling factor; re-tune it for your wheel size and encoder.
- IR detection polarity (which reading means "on the line") differs between files — verify the `drive()` conditions match your sensors before running.
- Motor speed constants `FAST`, `WALK`, and `SLOW` can be adjusted to suit your track and battery voltage.

## Author

**Colin Yap** — Electrical & Electronic Engineering, University of Nottingham Malaysia.
