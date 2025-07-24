# Part 4: Writing Your First Custom Strategy in Freqtrade

Welcome to Episode 4! Now that you’ve successfully run backtests using the built-in strategy, it’s time to write your **own custom strategy** using Python.

Freqtrade strategies are Python classes that define **buy/sell logic** using technical indicators like RSI, EMA, MACD, etc. In this episode, you’ll create your first strategy and backtest it.

---

## Step 1: Create a New Strategy File

Navigate to your `freqtrade_user/strategies` directory:

```bash
cd freqtrade_user/strategies
freqtrade new-strategy --strategy MyFirstStrategy
```

This will generate a new file: `MyFirstStrategy.py`.

---

## Step 2: Understand the Strategy Template

Open the file in your editor:

```bash
nano MyFirstStrategy.py
```

You’ll see a class that inherits from `IStrategy`. The two most important functions are:

```python
def populate_indicators(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
    # Add your indicators here
    return dataframe

def populate_buy_trend(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
    # Define buy conditions
    return dataframe

def populate_sell_trend(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
    # Define sell conditions
    return dataframe
```

---

## Step 3: Add Indicators (e.g., RSI and EMA)

Modify `populate_indicators()` like this:

```python
import talib.abstract as ta

def populate_indicators(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
    dataframe['rsi'] = ta.RSI(dataframe)
    dataframe['ema'] = ta.EMA(dataframe, timeperiod=14)
    return dataframe
```

---

## Step 4: Define Buy/Sell Logic

Add a simple condition: Buy when RSI < 30 and price is above EMA.

```python
def populate_buy_trend(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
    dataframe.loc[
        (dataframe['rsi'] < 30) &
        (dataframe['close'] > dataframe['ema']),
        'buy'] = 1
    return dataframe

def populate_sell_trend(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
    dataframe.loc[
        (dataframe['rsi'] > 70),
        'sell'] = 1
    return dataframe
```

---

## Step 5: Backtest the Strategy

Return to the `freqtrade_user` directory and run:

```bash
freqtrade backtesting --strategy MyFirstStrategy
```

You’ll get a performance report similar to previous episodes.

---

##  Tips

- Use `talib`, `pandas_ta`, or write your own math
- Add logging with `self.logger.info()`
- Backtest with different timeframes and pairs

---

##  Summary

| Task                  | Command                                              |
|-----------------------|------------------------------------------------------|
| Create a strategy     | `freqtrade new-strategy --strategy MyFirstStrategy`  |
| Add indicators        | `ta.RSI`, `ta.EMA`, etc.                             |
| Define buy/sell logic| In `populate_*_trend` methods                         |
| Run backtest          | `freqtrade backtesting --strategy MyFirstStrategy`   |

---

