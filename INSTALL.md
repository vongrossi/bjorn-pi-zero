# Installation (Raspberry Pi Zero 1 / Debian 13)

This guide documents a reproducible install for Raspberry Pi Zero 1 (ARMv6, 512 MB) on Raspberry Pi OS Lite (Debian 13 / trixie, 32-bit).
It is tuned for Waveshare 2.13" e-Paper V2.

## 1) OS and hardware prerequisites
- Raspberry Pi OS Lite (32-bit), Debian 13 (trixie), kernel 6.6+
- User: `raspberry` (or `bjorn` if you prefer, adjust paths below)
- Waveshare 2.13" e-Paper HAT (V2)

## 2) Enable SPI and I2C
```bash
sudo raspi-config nonint do_spi 0
sudo raspi-config nonint do_i2c 0
sudo reboot
```

## 3) System packages (apt)
```bash
sudo apt-get update
sudo apt-get install -y \
  python3-venv python3-pip \
  python3-rpi.gpio python3-spidev python3-gpiozero \
  python3-pil \
  python3-netifaces python3-ping3 python3-getmac \
  python3-paramiko python3-pymysql python3-sqlalchemy python3-nmap python3-rich \
  python3-cryptography python3-bcrypt python3-pyasn1 python3-pyasn1-modules python3-nacl \
  network-manager wireless-tools nmap lsof
```

Optional (heavier, enables all actions):
```bash
sudo apt-get install -y python3-numpy python3-pandas
```

Update Nmap scripts:
```bash
sudo nmap --script-updatedb
```

## 4) Clone this repository
```bash
cd /home/raspberry
git clone https://github.com/vongrossi/bjorn-pi-zero.git Bjorn
cd Bjorn
```

## 5) Python venv (system packages + pip extras)
```bash
python3 -m venv --system-site-packages .venv
. .venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
```

Notes:
- `smbprotocol` and `pysmb` are installed via pip because they are not present in the armhf apt repo.
- Python 3.13 removes `telnetlib`. Telnet actions are disabled cleanly when missing.

## 6) Configure e-paper (V2)
Edit `config/shared_config.json`:
```json
"epd_type": "epd2in13_V2"
```

## 7) Generate actions and CSVs
```bash
curl -X POST http://127.0.0.1:8000/initialize_csv
```

## 8) systemd service
```bash
sudo tee /etc/systemd/system/bjorn.service >/dev/null <<'UNIT'
[Unit]
Description=Bjorn Service
After=network-online.target
Wants=network-online.target

[Service]
ExecStartPre=/home/raspberry/Bjorn/kill_port_8000.sh
ExecStart=/home/raspberry/Bjorn/.venv/bin/python /home/raspberry/Bjorn/Bjorn.py
WorkingDirectory=/home/raspberry/Bjorn
Environment=PYTHONPATH=/home/raspberry/Bjorn
User=raspberry
Restart=always
RestartSec=5
CapabilityBoundingSet=CAP_NET_RAW CAP_NET_ADMIN
AmbientCapabilities=CAP_NET_RAW CAP_NET_ADMIN

[Install]
WantedBy=multi-user.target
UNIT

sudo systemctl daemon-reload
sudo systemctl enable bjorn.service
sudo systemctl restart bjorn.service
```

## 9) Access web UI
- `http://<PI_IP>:8000/`

If port 8000 is busy, the web server auto-increments to 8001. Restarting the service usually returns it to 8000:
```bash
sudo systemctl restart bjorn.service
```

## 10) Recommended Pi Zero profile (safe mode)
Consider lowering scan intensity for Pi Zero:
- `scan_vuln_running: false`
- `manual_mode: true`
- `scan_interval: 900`
- `image_display_delaymin/max` and `screen_delay` >= 5
- reduce action thread counts in code if needed
