---
sidebar_position: 4
---

# Hardware Targets

## Supported Boards

| Board | Platform | Audio Output | Stereo |
|-------|----------|-------------|--------|
| ESP32 (esp32dev) | pioarduino | Internal DAC (GPIO 25) or I2S DAC | Yes (I2S DAC) |
| Arduino Uno | ATmega328P | PWM output on pin 9 | No (mono only) |

Configuration is in `platformio.ini`. ESP32 is the primary target.

## Choosing the Audio Pin

```bash
python3 -m dsl.compiler song.jam --pin 26 -o src/main.cpp
```

Pins `25`/`26` select the ESP32 internal I2S DAC; any other pin emits PWM output on that pin. The web editor's `/api/upload` endpoint accepts the same `pin` parameter. Without `--pin`, Mozzi's platform default is used.

## Control Pins

Generated sketches read a play/stop button, a restart button, and a master volume pot. Pin assignments are selected at C++ compile time per platform:

| Function | ESP32 | Arduino Uno (AVR) |
|----------|-------|-------------------|
| `BTN_PLAY` (play/stop toggle) | GPIO 18 | D2 |
| `BTN_RESTART` (restart from top) | GPIO 19 | D3 |
| `POT_VOL` (master volume) | GPIO 32 | A0 |
| `POT_FREQ` (reserved) | GPIO 34 | A1 |

The volume pot is read against the platform's native ADC range (12-bit on ESP32, 10-bit on AVR).

## Stereo Features

`PAN` and `LFO PAN` require ESP32 with an I2S DAC. When any instrument uses `LFO PAN`, the compiler emits `MOZZI_STEREO` and `MOZZI_OUTPUT_I2S_DAC` config macros and guards with `#ifdef __AVR__ #error`.

## ESP32 Upload (WSL2)

Requires `usbipd-win` for USB passthrough and user in `dialout` group. PlatformIO must be on PATH (`pip install platformio`).
