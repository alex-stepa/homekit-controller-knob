# airplay-knob

Raspberry Pi as a native AirPlay (classic / v1) receiver, replacing a dedicated AirPlay adapter. Audio from iPhone or Mac streams via AirPlay to the Pi; play/pause/next/prev/volume are controlled by a physical rotary encoder and buttons wired directly to Pi GPIO. Because the Pi holds the DACP token as the receiver, it can send transport commands back to the source device — exactly like Control Center does.

## Why AirPlay 1, not AirPlay 2

- AirPlay 2 remote control (DACP forwarding to the source) is unsolved in every open-source receiver, including shairport-sync
- AirPlay 1 streams lossless ALAC — better quality than AirPlay 2, which transcodes to AAC 256 kbps
- Trade-offs accepted: no multi-room, no HomeKit / Home app integration

## Architecture

### 1. Raspberry Pi (Zero 2 W or similar)

- **shairport-sync** in classic AirPlay 1 mode (AirPlay 2 disabled)
- D-Bus / MPRIS interface for transport commands (playpause, nextitem, previtem)
- I2S DAC (PCM5102A) → analog / optical output to Yamaha HTR-6230

### 2. Physical controller (GPIO — no separate microcontroller)

- Rotary encoder with push button → volume + select
- 2–3 tactile buttons: play/pause, next, previous
- Wired directly to Pi GPIO — no ESP32 / WiFi hop, simpler and more reliable
- **pi-daemon**: Python or Go process reads GPIO, sends commands to shairport-sync via D-Bus / MPRIS

## Phases

- [ ] **Phase 0** — Install shairport-sync on Pi; verify AirPlay 1 discovery from iPhone/Mac; confirm audio plays
- [ ] **Phase 1** — Verify D-Bus/MPRIS remote control: send playpause/next/prev manually (`dbus-send` or mpris CLI); confirm it affects the source device
- [ ] **Phase 2** — Wire rotary encoder + buttons to GPIO; breadboard prototype
- [ ] **Phase 3** — Write pi-daemon (GPIO → shairport-sync D-Bus/MPRIS commands)
- [ ] **Phase 4** — DAC + audio output to Yamaha; verify quality and latency
- [ ] **Phase 5** — Enclosure — dark forest / nature aesthetic; final assembly

## Hardware

| Component | Notes |
|-----------|-------|
| Raspberry Pi Zero 2 W | or any available Pi |
| I2S DAC — PCM5102A | same as DAP project, known pinout |
| Rotary encoder (KY-040 or mechanical) | volume + push-to-select |
| 2–3 tactile buttons | play/pause, next, previous |
| Power supply | for Pi + DAC |

## Directory Structure

```
airplay-knob/
├── pi-daemon/      # GPIO daemon (Python or Go) → shairport-sync D-Bus/MPRIS
├── hardware/       # Schematics, pin assignments, enclosure notes
└── README.md
```

## What was cancelled and why

| Cancelled | Reason |
|-----------|--------|
| ESP32-S3 as receiver | Wanted AirPlay 2 source compat, but AirPlay 2 has no working remote control anywhere |
| Python backend + aiohomekit talking to Belkin Soundform | HAP does not expose transport characteristics; DACP token is held by the receiver (Belkin), not a bystander controller |
| Independent HomeKit pairing to Belkin | Not needed — Pi is now the receiver; no dependency on the existing HomeKit accessory |
