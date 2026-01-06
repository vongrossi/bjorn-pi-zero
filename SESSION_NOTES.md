# Session Notes (Pi Zero + PiSugar)

## Current State
- Bjorn running on Raspberry Pi Zero 1 at `192.168.68.80`.
- Web UI OK on `http://192.168.68.80:8000/` (falls back to 8001 if 8000 busy).
- E-paper configured: `epd2in13_V2` in `config/shared_config.json`.
- Actions loaded; NetworkScanner works; Telnet actions disabled cleanly (Python 3.13 has no `telnetlib`).
- `loot.html` still may show `.gitkeep` if browser cache is stale; backend filters it in `utils.py`.

## PiSugar 3 Status (blocked)
- PiSugar Power Manager installed via:
  - `wget https://cdn.pisugar.com/release/pisugar-power-manager.sh`
  - `bash pisugar-power-manager.sh -c release`
- Service `pisugar-server` **crashes with SEGV** on Pi Zero 1 (ARMv6).
- `pisugar-programmer` and `pisugar-poweroff` also **segfault** on ARMv6.
- I2C bus scan on Pi Zero:
  - `sudo i2cdetect -y 1` shows **no devices**.
  - Modules `i2c_dev` and `i2c_bcm2835` are loaded.

## Likely Cause
- Hardware/I2C connection: PiSugar not visible on bus.
- PiSugar binaries likely not compatible with ARMv6; need I2C-level integration.

## Next Steps (when you can re-seat hardware)
1) Power down Bjorn and re-seat PiSugar (ensure pogo pins contact).
2) Ensure PiSugar battery is charged/connected.
3) Boot Pi, then run:
   ```bash
   sudo i2cdetect -y 1
   ```
4) If a device appears (note address, e.g., `0x75`), proceed with I2C-based battery read.

## Planned Integration (after I2C address is known)
- Write a small Python reader using `smbus`/`smbus2`.
- Display battery percent/voltage on Bjorn display.
- Add low-battery shutdown hook (systemd timer or Bjorn thread).

## Useful Commands
- PiSugar service status:
  ```bash
  systemctl status pisugar-server --no-pager -l
  journalctl -u pisugar-server -n 100 --no-pager
  ```
- Bjorn service:
  ```bash
  systemctl status bjorn.service --no-pager -l
  journalctl -u bjorn.service -n 200 --no-pager
  ```
- Web UI ports:
  ```bash
  ss -ltnp | grep -E '8000|8001'
  ```
