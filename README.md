# CORE MB1

**ESP32 audio, display and control platform — monoblock display project**

CORE MB1 is an ESP32-S3 touch display project for a monoblock amplifier. It
provides a 480 × 480 interface with two analog-style VU meter faces, a boot and
standby flow, local settings, and ESP-NOW communication intended for the CORE
PR1 preamplifier.

> **Project status:** This firmware currently builds in **demo mode**. Relay and
> protection outputs are disabled, heatsink temperature is simulated, and the
> default VU input is a generated signal. It is a display and interaction demo,
> **not firmware for controlling a physical amplifier**.

## The CORE device family

MB1 is one of three related ESP32 projects. **PR1** is the preamplifier and
system coordinator; **MB1** is a monoblock endpoint; **RC1** is the handheld
remote. PR1 and RC1 have their own firmware projects and are not included in
this repository.

The planned **ESP32 audio, display and control platform** will give the three
projects one versioned protocol and a small, hardware-independent runtime for
commands, confirmed state and telemetry. Each device keeps its own display,
input, power and audio hardware adapter. This shared package has **not** yet
been extracted: the projects currently contain copies of some protocol headers.
The goal is a useful DIY edition that builds without private components, with
separate production capabilities only where they are actually developed.

See the [family architecture](.agents/design_docs/core_family/technical_design_0001.md)
and [ordered development backlog](backlog.md) for the proposed boundaries and
implementation sequence.

## Hardware and tools

- [Waveshare ESP32-S3-Touch-LCD-2.8C](https://www.waveshare.com/wiki/ESP32-S3-Touch-LCD-2.8C)
  (SKU 29086; 480 × 480 ST7701 display, GT911 touch, 16 MB flash, 8 MB PSRAM)
- USB cable for flashing and serial output
- [PlatformIO](https://platformio.org/) CLI or the PlatformIO IDE extension

The configured target is `waveshare_s3_touch_lcd_2_8c`. Do not flash this
firmware onto a different ESP32-S3 display board without adapting its display,
touch, and pin configuration.

## Build and run

From the repository root:

```sh
pio run -e waveshare_s3_touch_lcd_2_8c
```

Connect the Waveshare board, identify its serial port, then upload and monitor:

```sh
pio run -e waveshare_s3_touch_lcd_2_8c -t upload --upload-port COM10
pio device monitor --port COM10 --baud 115200
```

Replace `COM10` with your port (`/dev/ttyACM0`, for example, on some Linux
systems). `platformio.ini` also contains a local `COM10` default; override it
on the command line or edit it for your setup. PlatformIO installs the declared
Arduino GFX, LVGL, and ArduinoJson dependencies during the build.

The UI translations are embedded in the firmware. Uploading a LittleFS image is
not required to try the demo.

## What you can try

- Watch the boot sequence and the dark or warm-light analog VU face.
- Tap the VU screen to open the menu, change the meter face, or enter standby.
- Adjust device settings and use the English or Turkish interface.
- Explore the PR1 pairing and family standby flows when compatible companion
  firmware is available.

Settings are stored in ESP32 nonvolatile storage. The VU animation is a visual
demo rather than a calibrated measurement. Selecting the real signal input
currently displays zero because the analog input hardware is not connected.

## Repository guide

| Path | Contents |
| --- | --- |
| `src/`, `include/` | Firmware, display/UI, settings, and ESP-NOW code |
| `data/` | English and Turkish translation JSON sources |
| `assets/vu/`, `tools/` | VU artwork and asset generation tools |
| `tests/` | Protocol tests |
| `docs/waveshare-migration.md` | Board details, integration notes, and hardware follow-up |

The `demo/` directory contains manufacturer and library reference material; it
is not the PlatformIO application target. Its contents need a separate rights
review before any public release.

## Licensing plan

The intended model is personal, noncommercial DIY use under published terms,
with a **separate commercial agreement charging per produced device**.
Commercial licensing would grant limited use; ownership of the code would stay
with its rights holder. A compiled library alone cannot count manufactured or
sold devices, so commercial accounting belongs in the agreement and production
process.

**No project license has been added yet.** The exact public license, covered
files, rights holder and commercial contact method must be finalized before
publication. Third-party reference material is not automatically covered by
the project's future license.

## Current limits

- The external relay, protection, temperature-sensor, and audio ADC hardware
  backend has not been implemented. Changing `MB1_DEMO_MODE=1` to `0` is
  intentionally blocked at compile time until that backend exists.
- Full MB1–PR1 wireless operation requires compatible PR1 firmware and device
  pairing; this repository alone does not provide a complete audio system.

For board-specific details and the remaining hardware work, see
[Waveshare migration notes](docs/waveshare-migration.md).
