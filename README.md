# Bjorn Pi Zero (Debian 13 / armhf)

This repository is a Raspberry Pi Zero 1 focused fork of Bjorn, tuned for Debian 13 (armhf) and Waveshare 2.13" e-Paper V2.

Upstream project: https://github.com/infinition/Bjorn

## Quick start
- Install guide: `INSTALL.md`
- Changelog / checkpoint: `CHANGELOG_LOCAL.md`
- Troubleshooting: `TROUBLESHOOTING.md`

## Supported platform
- Raspberry Pi Zero 1 (ARMv6, 512 MB)
- Raspberry Pi OS Lite, Debian 13 (trixie), 32-bit armhf
- Waveshare 2.13" e-Paper HAT V2

## Usage
- Start/stop:
  ```bash
  sudo systemctl start bjorn.service
  sudo systemctl stop bjorn.service
  sudo systemctl restart bjorn.service
  ```
- Logs:
  ```bash
  journalctl -u bjorn.service -n 200 --no-pager
  ```
- Web UI:
  - `http://<PI_IP>:8000/`
  - If port 8000 is busy, it may move to 8001.

## Notes for this fork
- Uses a venv with `--system-site-packages` and apt-first strategy for heavy packages.
- Python 3.13 removes `telnetlib`. Telnet actions are disabled cleanly when unavailable.
- Web UI auto-increments to port 8001 if 8000 is busy.
