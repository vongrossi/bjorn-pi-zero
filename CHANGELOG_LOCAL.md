# Local Changelog (checkpoint)

This file tracks the changes applied while stabilizing Bjorn for Raspberry Pi Zero 1 (ARMv6).

## 2026-01-05

### Deployment / Pi setup
- Synced repository to the Raspberry Pi at `/home/raspberry/Bjorn/`.
- Enabled SPI and I2C; added user `raspberry` to `spi,gpio,i2c` groups.
- Created systemd service `bjorn.service` using venv interpreter and `PYTHONPATH`.
- Installed system packages needed for runtime (GPIO/SPI, Pillow, NetworkManager, wireless-tools, nmap, lsof, crypto, numpy/pandas, etc.).
- Created venv with `--system-site-packages` and installed missing pip deps (smbprotocol, pysmb, paramiko, pymysql, sqlalchemy, python-nmap, ping3, get-mac, tqdm, typing_extensions).
- Updated `config/shared_config.json` to set `epd_type` to `epd2in13_V2`.

### Web / API fixes
- `/network_data` now returns a friendly JSON when no scan result files exist instead of raising `max()` on empty list.
- Web server port can fall back to 8001 if 8000 is busy; service restarted to bring it back to 8000 when needed.

### Scanner / Actions fixes
- Resolved `NetworkScanner` not loading by ensuring `python3-netifaces` is available and regenerating `actions.json`.
- Added safe handling when `telnetlib` is missing on Python 3.13:
  - `actions/telnet_connector.py` now disables Telnet bruteforce cleanly.
  - `actions/steal_files_telnet.py` now disables Telnet exfil cleanly.

### Loot view
- Filtered `.gitkeep` from file listing (`utils.py`), so it should not appear in loot listing.

### Notes / Observations
- `webapp.py` logs show the port can switch to 8001 if 8000 is occupied; restart restores 8000.
- Some modules still log missing resources (e.g., `usb0` neighbors) when USB gadget is not configured.
