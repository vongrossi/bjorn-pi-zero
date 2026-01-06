# Project Context (Pi Zero Variant)

## Purpose
This fork targets Raspberry Pi Zero 1 (ARMv6) on Debian 13 (armhf) with Waveshare 2.13" e‑Paper V2.
It documents a reproducible install and includes stability fixes for low‑resource hardware.

## Current Runtime Behavior
- Web UI binds to port 8000 and retries if busy.
- Manual panel no longer auto‑stops the Orchestrator; use explicit Stop/Start controls if needed.
- Loot list hides `.gitkeep` from API results.
- Wi‑Fi scan returns empty results instead of 500 errors.
- Missing `temp_log.txt` is handled by creating an empty file.

## PiSugar 3 Status
- Power Manager installs but binaries segfault on ARMv6.
- I2C scan currently shows no devices on bus 1.
- Next steps: re‑seat PiSugar, confirm I2C address, implement I2C read + low‑battery shutdown.

## Known Hotspots
- `utils.py` contains multiple helper functions; changes must be consistent in both definitions.
- `webapp.py` manages the web server and port binding logic.
- `web/index.html` manual panel interactions can affect Orchestrator state.

## Future Work
- Replace PiSugar binaries with I2C‑based battery reader (smbus) once device appears on bus.
- Add battery status to display and Web UI.
- Implement low‑battery shutdown hook.
