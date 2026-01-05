# Troubleshooting (Pi Zero / Debian 13)

## Web UI not responding on port 8000
- The web server auto-increments to 8001 if 8000 is busy.
- Check listening ports:
  ```bash
  ss -ltnp | grep -E '8000|8001'
  ```
- Restart to return to 8000:
  ```bash
  sudo systemctl restart bjorn.service
  ```

## NetworkScanner not running
- Ensure `python3-netifaces` is installed:
  ```bash
  sudo apt-get install -y python3-netifaces
  ```
- Regenerate actions and CSVs:
  ```bash
  curl -X POST http://127.0.0.1:8000/initialize_csv
  ```

## Telnet errors on Python 3.13
- `telnetlib` was removed in Python 3.13.
- This fork disables Telnet actions automatically if `telnetlib` is missing.

## loot.html shows `.gitkeep`
- The backend filters `.gitkeep` files.
- Clear browser cache or open a private window if it still appears.

## No data in /network.html
- If there are no scan results yet, `/network_data` returns an empty state message.
- Wait for the first scan to finish.

## apt lock errors
- If apt is stuck, clear locks and fix dpkg:
  ```bash
  sudo rm -f /var/lib/apt/lists/lock /var/lib/dpkg/lock /var/lib/dpkg/lock-frontend
  sudo dpkg --configure -a
  ```
