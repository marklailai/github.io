# Part 6: Strategy Optimization with Hyperparameters (Hyperopt)

Welcome to Episode 6! Now that you’ve created your custom strategy and explored technical indicators, it’s time to **optimize your strategy parameters** using Freqtrade’s built-in tool: **Hyperopt**.

Hyperopt allows you to automatically search for the best combinations of parameters to maximize profitability or reduce risk—saving you hours of manual trial and error.

---

##  What Is Hyperopt?

Hyperopt is a **parameter optimization framework** integrated into Freqtrade. It systematically tests many combinations of parameters (like RSI thresholds, EMA periods, ROI targets, etc.) to find the most effective settings for your strategy.

It uses machine learning search algorithms (like TPE or random search) to efficiently explore the space.

---

##  Step 1: Define Hyperoptable Parameters in Your Strategy

In your strategy file (`MyFirstStrategy.py`), add `@parameter` decorators to expose settings:

```python
from freqtrade.strategy import IntParameter, CategoricalParameter

class MyFirstStrategy(IStrategy):
    rsi_buy = IntParameter(10, 40, default=30, space="buy")
    rsi_sell = IntParameter(60, 90, default=70, space="sell")
    
    def populate_buy_trend(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
        dataframe.loc[
            (dataframe['rsi'] < self.rsi_buy.value),
            'buy'] = 1
        return dataframe

    def populate_sell_trend(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
        dataframe.loc[
            (dataframe['rsi'] > self.rsi_sell.value),
            'sell'] = 1
        return dataframe
```
## Step 2: Run Hyperopt
From the root of your user directory, run:

```bash
freqtrade hyperopt --strategy MyFirstStrategy --hyperopt-loss SharpeHyperOptLoss --epochs 100
```
- -strategy: your strategy name
- -hyperopt-loss: the optimization goal (e.g., Sharpe, Sortino, Basic)
- -epochs: number of iterations (more = better)

## Supported Loss Functions
Loss Function	Purpose
SharpeHyperOptLoss	Maximize Sharpe Ratio
SortinoHyperOptLoss	Focus on downside risk
BasicHyperOptLoss	Maximize profit
Custom class	You can write your own!

## Step 3: Review the Best Results
After Hyperopt runs, it will display the best performing parameters. Save these values and optionally hardcode them into your strategy for production.

You can also inspect the result file:

```bash
cat user_data/hyperopt_results.json
```
⚡ Pro Tips
Always use a long enough backtest period (at least several months)

Try both --spaces buy, sell, and roi, or all

Avoid overfitting by testing on unseen data (Episode 7!)

Use --random-state to get reproducible results

## Summary
Task	Command / Code
Define parameters	IntParameter, CategoricalParameter
Run hyperopt	freqtrade hyperopt ...
Optimization goal	--hyperopt-loss SharpeHyperOptLoss
Avoid overfitting	Use validation and multiple datasets
