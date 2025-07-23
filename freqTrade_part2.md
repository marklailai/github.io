# Part 2: Setting Up Freqtrade on Windows with WSL2

Before you can run Freqtrade on your Windows machine, you need a Linux environment. The recommended way is to use **WSL2 (Windows Subsystem for Linux 2)**, which lets you run a full Linux kernel inside Windows—perfect for Python-based tools like Freqtrade.

In this episode, you'll learn how to install WSL2, set up Ubuntu, and prepare your system for Freqtrade.

---

## Step 1: Enable WSL2 on Windows

### Quick Setup (Windows 10 2004+ or Windows 11)

1. Open **PowerShell as Administrator** and run:
   ```powershell
   wsl --install
   ```

2. Wait for the installation to complete and restart your computer.

3. When prompted, choose **Ubuntu** as your default Linux distribution.

---

## Step 2: Verify Installation

After rebooting, open a new terminal (Ubuntu from Start Menu), and run:

```bash
wsl --list --verbose
```

You should see something like:

```
NAME      STATE           VERSION
Ubuntu    Running         2
```

If it says VERSION 1, upgrade it:

```powershell
wsl --set-version Ubuntu 2
```

To make WSL2 default for future installs:

```powershell
wsl --set-default-version 2
```

---

## Step 3: Update and Install System Dependencies

In the Ubuntu terminal:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install git python3 python3-pip python3-venv -y
```

---

## Step 4: Install Freqtrade

Clone the repo and install Freqtrade:

```bash
git clone https://github.com/freqtrade/freqtrade.git
cd freqtrade
./setup.sh -i
```

---

## Step 5: Create Your User Directory

This is where you’ll store your config, strategies, and data:

```bash
freqtrade create-userdir --userdir freqtrade_user
cd freqtrade_user
freqtrade new-config
```

Open and edit `config.json` to suit your exchange and preferences.

---

## Step 6: Test Your Installation

Try running a basic backtest using the built-in strategy:

```bash
freqtrade download-data --timeframe 5m --exchange binance
freqtrade backtesting --strategy SampleStrategy
```

If all goes well, you’ll see a performance report in your terminal.

---

## Optional: Connect to Real Exchange

To prepare for live or dry-run trading:

1. Edit `config.json` and add your API keys (keep them secure!)
2. Enable `dry_run: true` until you're ready for live trading.

---

## Summary

| Task                         | Command or Action                      |
|------------------------------|----------------------------------------|
| Install WSL2                 | `wsl --install`                        |
| Upgrade distro to WSL2       | `wsl --set-version Ubuntu 2`          |
| Install dependencies         | `sudo apt install ...`                |
| Install Freqtrade            | `./setup.sh -i`                        |
| Create config and user dir   | `freqtrade create-userdir`            |
| Run backtest                 | `freqtrade backtesting`               |

---

