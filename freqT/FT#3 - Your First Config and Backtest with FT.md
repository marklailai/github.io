# Part 3: Your First Config and Backtest with Freqtrade

Now that Freqtrade is installed and running in WSL2, it's time to configure the bot and run your **first real backtest** with your own market pairs and parameters. This episode guides you through creating and editing your `config.json`, selecting trading pairs, and analyzing backtest results.

---

## Step 1: Generate a New Config File

If you haven’t already created one, run:

```bash
freqtrade new-config --config config.json
```

This will generate a `config.json` file in your current directory. It’s the heart of your bot and defines everything from exchange settings to strategies and protections.

---

## Step 2: Edit `config.json`

Open it in your editor:

```bash
nano config.json
```

### Key Sections to Edit:

- `"exchange"`: Add your desired exchange, e.g., Binance.
- `"pair_whitelist"`: List the trading pairs to include.
- `"stake_currency"`: Typically `"USDT"` or `"BTC"`.
- `"timeframe"`: Candle timeframe (e.g., `"5m"`, `"1h"`).
- `"strategy"`: Use `"SampleStrategy"` for testing.

### Example Exchange Section:

```json
"exchange": {
  "name": "binance",
  "key": "your_api_key",
  "secret": "your_api_secret",
  "ccxt_config": {},
  "ccxt_async_config": {},
  "pair_whitelist": ["BTC/USDT", "ETH/USDT"],
  "pair_blacklist": ["BEAR/USDT", "BULL/USDT"]
}
```

> For backtesting, your API keys can be empty. Just make sure your configuration is valid.

---

## Step 3: Download Historical Data

Choose your exchange and timeframe:

```bash
freqtrade download-data --exchange binance --timeframe 5m
```

You can also specify pairs:

```bash
freqtrade download-data --exchange binance --timeframe 5m --pairs BTC/USDT ETH/USDT
```

---

## Step 4: Run Your First Backtest

Use the built-in strategy as a sanity check:

```bash
freqtrade backtesting --strategy SampleStrategy
```

Add `--config config.json` if you’re not in the userdir or want to test a specific config.

---

## Step 5: Analyze Backtest Results

You’ll get output like:

```
Backtesting Report:
===================
Pair: BTC/USDT
Profit %: 2.45%
Total Trades: 12
Wins: 7
Losses: 5
```

Use additional flags to save or export results:

```bash
freqtrade backtesting --strategy SampleStrategy --export trades
```

You can also visualize it with:

```bash
freqtrade plot-dataframe
```

---

## Summary

| Task                    | Command                                           |
|-------------------------|---------------------------------------------------|
| Create config           | `freqtrade new-config`                            |
| Download historical data| `freqtrade download-data`                         |
| Run backtest            | `freqtrade backtesting --strategy SampleStrategy` |
| Export results          | `--export trades`                                 |

---

##  Tips

-  Use `--config` to specify alternate config files
-  Backtest with small pair sets first
-  Use `--timeframe` consistently between data download and backtest
-  Don’t use real API keys unless you're going live

---
