# Bjorn Pi Zero Guide

This guide explains how Bjorn works on Raspberry Pi Zero and what each brute-force module does.

## How Bjorn works (high level)
1) **Startup**
   - systemd launches `Bjorn.py`.
   - `SharedData` loads config, assets, and CSV state.
   - Threads start: Display, Bjorn main loop, Web UI.

2) **Network discovery**
   - `NetworkScanner` performs discovery (ping + Nmap host scan) and builds `data/netkb.csv`.
   - Hosts are tracked with ports and status.

3) **Orchestrator loop**
   - Orchestrator loads actions from `config/actions.json`.
   - For each discovered host, it checks open ports and runs compatible actions.
   - Actions can be parent/child (e.g., brute-force -> steal files).

4) **Web UI**
   - Use the UI to toggle manual mode, run manual attacks, view loot, and inspect logs.

## Brute-force modules (what they do)
All brute-force modules follow this pattern:
- Read target IPs and open ports from `netkb.csv`.
- Read username/password dictionaries from `data/input/dictionary/`.
- Attempt logins in a loop with limited concurrency.
- On success, store credentials in a CSV file under `data/output/`.

### SSH Brute Force
- **Module:** `actions/ssh_connector.py`
- **Port:** 22
- **Method:** Tries SSH logins using Paramiko.
- **Output:** `data/output/crackedpwd/ssh.csv`

### FTP Brute Force
- **Module:** `actions/ftp_connector.py`
- **Port:** 21
- **Method:** Tries FTP logins using Python’s FTP client.
- **Output:** `data/output/crackedpwd/ftp.csv`

### SMB Brute Force
- **Module:** `actions/smb_connector.py`
- **Port:** 445
- **Method:** Tries SMB credentials using `pysmb`.
- **Output:** `data/output/crackedpwd/smb.csv`

### RDP Brute Force
- **Module:** `actions/rdp_connector.py`
- **Port:** 3389
- **Method:** Attempts RDP authentication.
- **Output:** `data/output/crackedpwd/rdp.csv`

### SQL Brute Force
- **Module:** `actions/sql_connector.py`
- **Port:** 3306 (MySQL)
- **Method:** Tries SQL logins using PyMySQL.
- **Output:** `data/output/crackedpwd/sql.csv`

### Telnet Brute Force (disabled on Python 3.13)
- **Module:** `actions/telnet_connector.py`
- **Port:** 23
- **Status:** Disabled automatically when `telnetlib` is missing (Python 3.13).
- **Output (when available):** `data/output/crackedpwd/telnet.csv`

## Data exfiltration modules (after success)
These modules use successful credentials to copy or read files:
- **StealFilesSSH** (`actions/steal_files_ssh.py`)
- **StealFilesFTP** (`actions/steal_files_ftp.py`)
- **StealFilesSMB** (`actions/steal_files_smb.py`)
- **StealFilesRDP** (`actions/steal_files_rdp.py`)
- **StealFilesTelnet** (`actions/steal_files_telnet.py`, disabled on Python 3.13)

## Manual mode
- Manual mode lets you choose an IP, port, and action from the UI.
- It uses the same action pipeline as automatic mode, but targets only the selected host.

## Files and directories
- `config/shared_config.json` – main settings
- `config/actions.json` – generated list of actions
- `data/netkb.csv` – discovered hosts and ports
- `data/output/` – loot and results
- `data/input/dictionary/` – username/password lists

## Safety notes
- Use on networks you own or have explicit permission to test.
- Lower scan intensity and thread counts on Pi Zero to avoid instability.
