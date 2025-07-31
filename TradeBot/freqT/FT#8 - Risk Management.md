# Part 8: Risk Management and Stoploss Strategies in Freqtrade

Welcome to Part 8 of our Freqtrade journey! Now that your strategy is functional and validated, it’s time to talk about a crucial topic: **risk management**.

Even the best strategy can fail without proper risk controls. In this episode, we’ll explore how to use **stoplosses, trailing stops, position sizing**, and other techniques to **protect your capital**.

---

## Why Risk Management Matters

Risk management helps you:

- Minimize losses on bad trades
- Lock in profits on winning trades
- Survive long-term volatility
- Avoid account blow-ups

---

## Stoploss: The First Line of Defense

Freqtrade lets you define static and dynamic stoplosses.

### Static Stoploss

Defined in the strategy class:

```python
stoploss = -0.05  # 5% maximum loss per trade
```
This means a trade will automatically close if it drops 5% below the entry price.

### Trailing Stoploss: Let Winners Run
Trailing stoplosses adjust as price increases, helping to maximize profits while capping risk.

```python
use_custom_stoploss = True

def custom_stoploss(self, pair: str, trade: 'Trade', current_time: datetime,
                    current_rate: float, current_profit: float, **kwargs) -> float:
    if current_profit > 0.10:
        return -0.02  # Trail tighter
    elif current_profit > 0.05:
        return -0.03
    else:
        return -0.05
```
**Benefit**: Protects gains as price moves up.

## Position Sizing
Control how much capital to use per trade:

### Static Position Sizing
Set in config.json:

```json
"stake_amount": "unlimited",
"stake_currency": "quote",
"dry_run_wallet": 1000
```
You can also set a fixed USD amount per trade:

```json
"stake_amount": 100
```
### Dynamic Position Sizing
Inside the strategy, override:

```python
def custom_stake_amount(self, pair: str, current_price: float, current_time: datetime,
                        proposed_stake: float, min_stake: float, max_stake: float,
                        leverage: float, entry_tag: Optional[str], **kwargs) -> float:
    # Example: Scale based on volatility
    return min(proposed_stake, 0.02 * self.wallets.get_total('USDT'))
```
## Cooldown Periods
Prevent re-entry after a loss:

```python
cooldown = 30  # Minutes before new trade on same pair
```

## Risk Management Guidelines
| Rule                                 | Purpose                       |
| ------------------------------------ | ----------------------------- |
| 1–2% max risk per trade              | Prevent large drawdowns       |
| Use trailing stop for profit lock-in | Avoid giving back gains       |
| Never go all-in                      | Maintain flexibility          |
| Monitor drawdowns                    | Adjust stake or exit strategy |


## Summary
| Feature           | Code / Config Example                  |
| ----------------- | -------------------------------------- |
| Static stoploss   | `stoploss = -0.05`                     |
| Trailing stoploss | `custom_stoploss()` function           |
| Stake amount      | `"stake_amount": 100` in `config.json` |
| Dynamic sizing    | `custom_stake_amount()` method         |
| Cooldown          | `cooldown = 30`                        |
