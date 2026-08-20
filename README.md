# homekit-controller-knob

Physical hardware controller (rotary encoder + buttons) based on ESP32-S3 that sends HTTP requests to a custom backend server. The backend (Python, aiohomekit) pairs directly with an existing HomeKit AirPlay accessory (Belkin Soundform AirPlay2 Adapter) as an independent HomeKit controller and controls play/pause/next/prev/volume on an active AirPlay stream.

## Architecture

### 1. Backend Service (Python, running on Pi5/homelab)
- `aiohomekit` library for HAP pairing and characteristic control
- Pairs with the accessory using the setup code (available on device label)
- FastAPI REST wrapper over HAP characteristics:
  - `POST /play-pause`
  - `POST /next`
  - `POST /previous`
  - `POST /volume/{value}`
- Persistent storage of pairing state between restarts

### 2. Firmware (ESP32-S3)
- Toolchain TBD: embassy-rs (embedded Rust) / esp-idf / Arduino+PlatformIO
- Rotary encoder (KY-040 or mechanical with button) → volume control
- 2–3 buttons: play/pause, next, previous
- WiFi client → HTTP calls to backend endpoints
- Config (WiFi SSID/password, backend URL) in a separate file, excluded from git

### 3. Hardware / Enclosure
- TBD after first breadboard prototype
- Aesthetic direction: dark forest / nature

## Phases

- [ ] Phase 0: Verify aiohomekit pairing with the accessory; enumerate available characteristics
- [ ] Phase 1: Backend REST API over HAP; manual testing with curl (play/pause/next/prev/volume)
- [ ] Phase 2: ESP32 firmware — breadboard prototype, WiFi + HTTP client, single button (play/pause)
- [ ] Phase 3: Add rotary encoder (volume) and remaining buttons (next/prev)
- [ ] Phase 4: Custom PCB / final enclosure

## Notes
- This project uses its own independent HomeKit pairing — it does not interfere with the main Home Assistant setup.
- Accessory model info, serial, and pairing credentials are stored in backend config files that are `.gitignore`d.
