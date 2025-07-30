# Part 9: Running Freqtrade in Dry Run and Live Trading Modes

Welcome to Part 9! After designing, optimizing, and validating your strategy, it's time to **run it in the real world**.

But before risking real funds, Freqtrade offers a brilliant feature: **dry-run mode**. It simulates live trading without using actual money, making it perfect for final testing.

In this episode, you'll learn how to start Freqtrade in both **dry-run** and **live trading** modes—and how to monitor, manage, and troubleshoot your bot in real time.

---

## What Is Dry Run Mode?

**Dry-run mode** simulates live trading using fake funds. The bot makes virtual trades using real-time market data—no money is lost or earned.

It’s the final safety check before live deployment.

---

## Configure Your Bot for Dry Run

Open your `config.json` file and make sure you have:

```json
"dry_run": true,
"dry_run_wallet": 1000,
"stake_currency": "USDT",
"stake_amount": 50
```
This means:

- You're simulating trades
- Starting with $1000 virtual balance
- Trading $50 per position

Start the Bot in Dry Run Mode
In your terminal, run:

```bash
freqtrade trade --strategy MyStrategy
```
You’ll see logs of trades being opened and closed based on live data. This is a great way to test performance under real-time market conditions.

## Switching to Live Mode
Once you're confident, you can go live.

### 1. Update config.json:
```json
"dry_run": false,
```

### 2. Set your API keys (Binance, KuCoin, etc.):
```json
"api_key": "YOUR_API_KEY",
"api_secret": "YOUR_API_SECRET"
```
⚠️ **Important**: Make sure your API keys are restricted for trading only and not for withdrawals!

### 3. Run in Live Mode:
```bash
freqtrade trade --strategy MyStrategy
```
The bot will now place real trades with real funds.

## Monitoring the Bot
Freqtrade offers real-time monitoring via logs and a web UI.

### View Live Logs
```bash
tail -f logs/freqtrade.log

### Use the Web UI (optional)
If enabled in your config:
```json
"api_server": {
    "enabled": true,
    "listen_ip_address": "127.0.0.1",
    "listen_port": 8080,
    "username": "admin",
    "password": "password"
}
```
Launch the UI:

```bash
freqtrade webserver
```
Then visit:
http://127.0.0.1:8080

## Stopping the Bot Safely
To stop the bot gracefully:

```bash
CTRL + C
```
Or, if running in a screen or tmux session:

```bash
kill <PID>
```

Always wait for it to shut down cleanly to avoid order mismatches.

## Summary
| Task                  | Command / Config                        |
| --------------------- | --------------------------------------- |
| Dry run mode          | `"dry_run": true`                       |
| Start dry run         | `freqtrade trade --strategy MyStrategy` |
| Enable live trading   | `"dry_run": false` + API keys           |
| Monitor logs          | `tail -f logs/freqtrade.log`            |
| Use web UI (optional) | Enable and launch `freqtrade webserver` |
