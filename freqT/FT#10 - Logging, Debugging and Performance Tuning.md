# Part 10: Custom Logging, Debugging, and Performance Tuning

Welcome to Part 10! So far, you’ve learned to design, test, optimize, and run your trading strategies. Now it’s time to go **under the hood**.

In this part, we’ll cover how to:
- Create custom log messages for strategy insights
- Debug common errors
- Profile and optimize your strategy for speed and reliability

---

## Custom Logging in Your Strategy

You can log custom messages in your strategy using Python’s `logging` module.

### Setup a Logger

```python
import logging
logger = logging.getLogger(__name__)
```
## Example Usage
```python
def populate_buy_trend(...):
    ...
    if conditions_met:
        logger.info(f"Buying signal triggered at {row['date']} with RSI: {row['rsi']}")
```

Log levels:

- `logger.debug()` — detailed info for development
- `logger.info()` — general events
- `logger.warning()` — unexpected but non-breaking
- `logger.error()` — critical issues

Logs are stored in `logs/freqtrade.log`.

## Debugging Common Issues
### Strategy Not Entering Trades?
Check:
- Are indicators being calculated correctly?
- Are buy/sell conditions ever `True`?
- Did you enable the pair in `config.json`?

Use debug logs to print DataFrame conditions:

```python
logger.debug(dataframe[['rsi', 'ema', 'buy']].tail(10))
```

### Backtest/Trade Fails to Start?
Check:
- `config.json` for missing keys
- `strategy.py` for syntax or import errors
- Are dependencies installed?

Run with debug logging:

```bash
freqtrade trade -v
```

## Profiling Strategy Performance
Slow strategy? Profile its performance.

## Enable Strategy Timer
```json
"strategy_logging": {
  "enabled": true,
  "log_interval": 60
}
```
Or manually time your code:

```python
import time

start = time.time()
# Your logic
logger.debug(f"populate_buy_trend ran in {time.time() - start:.4f} seconds")
```

## Unit Testing Strategies
Create unit tests using pytest:

```python
def test_rsi_buy_condition():
    df = create_test_dataframe()
    strat = MyStrategy(config)
    df = strat.populate_indicators(df, metadata={'pair': 'BTC/USDT'})
    df = strat.populate_buy_trend(df, metadata={'pair': 'BTC/USDT'})
    assert df['buy'].sum() > 0
```
Run tests with:

```bash
pytest tests/
```

## Continuous Improvement Tips
| Habit                     | Benefit                            |
| ------------------------- | ---------------------------------- |
| Add detailed logging      | Easier debugging                   |
| Write unit tests          | Catch errors early                 |
| Monitor logs during live  | Spot live issues quickly           |
| Profile long-running code | Optimize slow indicators           |
| Review trade history      | Improve strategy logic iteratively |


## Summary
| Task                 | Tool / Tip                         |
| -------------------- | ---------------------------------- |
| Log events           | `logger.info()` in strategy        |
| Debug slow code      | `time.time()` and `logger.debug()` |
| Analyze errors       | `freqtrade.log` or `-v` mode       |
| Write unit tests     | Use `pytest`                       |
| Tune for performance | Profile critical indicator blocks  |



