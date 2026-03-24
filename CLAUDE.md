# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

ESPHome configuration repository for ESP32-based IoT devices integrated with Home Assistant. Currently contains configuration for an M5 StickC Plus2 acting as a BLE Gateway.

## Build & Deploy

This project uses ESPHome with ESP-IDF framework. Common commands:

```bash
# Validate configuration
esphome config M5_StickC-Plus2/config.yaml

# Compile firmware
esphome compile M5_StickC-Plus2/config.yaml

# Upload via USB (115200 baud)
esphome upload M5_StickC-Plus2/config.yaml

# Upload via OTA (device must be on network)
esphome upload --device <IP> M5_StickC-Plus2/config.yaml

# View device logs
esphome logs M5_StickC-Plus2/config.yaml
```

## Secrets

WiFi credentials are stored in a `secrets.yaml` file (not tracked in git) referenced via `!secret wifi_ssid` and `!secret wifi_password`. This file must exist alongside the config or in the ESPHome config directory.

## Architecture

Each device has its own subdirectory containing a `config.yaml`. The M5 StickC Plus2 config uses:

- **Framework:** ESP-IDF (required for BLE proxy support)
- **PSRAM:** Enabled with cache-issue workaround build flags
- **Key peripherals:** ST7789V display (SPI), MPU6886 IMU (I2C), battery ADC, buzzer, LED
- **Primary function:** Bluetooth Low Energy proxy for Home Assistant

## ESPHome Conventions

- Lambda code blocks are C++ — follow ESPHome's C++ API (e.g., `id()` to reference components, `it` for display draw calls)
- Deprecated APIs should be tracked against ESPHome release notes. Example: `IPAddress::str()` was deprecated in favor of `str_to(buf)` pattern (removal in 2026.8.0)
- Display lambdas use `it.print()` / `it.printf()` with font and color IDs
- Sensor update intervals are set conservatively (60s) to save power on battery devices
