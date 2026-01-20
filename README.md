# SMA 50/200 Golden Cross Strategy - Reliance Industries

A Python implementation of the classic "Golden Cross / Death Cross" trading strategy using 50-period and 200-period Simple Moving Averages on Reliance Industries stock.

## 🎯 What Is This Strategy?

The SMA 50/200 crossover is one of the most famous trading strategies in the world:
- **Golden Cross** - When SMA50 crosses ABOVE SMA200 → BUY signal
- **Death Cross** - When SMA50 crosses BELOW SMA200 → SELL signal

This strategy only trades on **crossover days** - the exact moment when the lines cross, not every day they're above/below each other.

## 📊 Strategy Logic

### How It Works

**BUY Signal (+1):**
- Today: SMA50 > SMA200
- Yesterday: SMA50 < SMA200
- **Action**: Golden Cross detected! Buy on this day only.

**SELL Signal (-1):**
- Today: SMA50 < SMA200
- Yesterday: SMA50 > SMA200
- **Action**: Death Cross detected! Sell on this day only.

**No Signal (0):**
- No crossover happening
- **Action**: Do nothing, stay in cash.

### Key Difference from Other Strategies
Most moving average strategies hold positions as long as one MA is above another. **This strategy only trades ON THE CROSSOVER DAY**, then exits to cash.

## 🚀 Quick Start

### Prerequisites
```bash
pip install pandas numpy yfinance matplotlib
```

### Run the Strategy
```bash
python sma_crossover_reliance.py
```

## 📈 What Makes This Code Special

### 1. Uses Log Returns
```python
df['cc_returns'] = np.log(df['Adj Close'] / df['Adj Close'].shift(1))
```
- More accurate for compounding calculations
- Better for statistical analysis
- Industry standard for financial returns

### 2. 5 Years of Daily Data
```python
start1 = end1 - pd.Timedelta(days=365*5)
interval='1D'
```
- Long enough history to calculate SMA200 properly
- Daily timeframe (not intraday) - perfect for this strategy
- Captures multiple market cycles

### 3. Detects Only Crossover Points
```python
df['position'] = np.where((df['sma50'] > df['sma200'])
                        & (df['previous_day_sma50'] < df['previous_day_sma200']), 1, 0)
```
- Checks today vs yesterday to find the exact crossover moment
- Avoids holding positions continuously
- Only trades when crossover happens

### 4. Shifted SMAs to Avoid Look-Ahead Bias
```python
df['sma50'] = df['sma50'].shift(1)
df['sma200'] = df['sma200'].shift(1)
```
- Uses yesterday's SMA values for today's decision
- Prevents unrealistic backtesting results
- Realistic trading simulation

### 5. Cumulative Returns Visualization
```python
df[['cc_returns', 'strategy_return']].cumsum().plot()
```
- Shows compounding effect over 5 years
- Easy visual comparison of strategy vs buy & hold

## 🔍 What the Code Shows You

### 1. Cumulative Performance Chart
Visual comparison of:
- **Blue line**: Buy & Hold returns (if you just held Reliance for 5 years)
- **Orange line**: Strategy returns (only trading on crossover days)

### 2. Total Returns
```
Buy and hold returns: X.XX
Strategy returns: Y.YY
```
Shows which approach performed better over 5 years.

### 3. Number of Signals
Use `df['position'].value_counts()` to see:
- How many Golden Cross signals (1)
- How many Death Cross signals (-1)
- How many no-signal days (0)

Typically: **Very few signals** over 5 years (maybe 5-15 total)

## 💡 Why Reliance Industries?

- **Highly liquid**: Easy to buy/sell large quantities
- **Blue-chip stock**: Less manipulation, cleaner trends
- **Long history**: 5 years of data available
- **Trending stock**: Benefits from long-term trend strategies

## 🎓 What You'll Learn

### Technical Concepts
- How Golden Cross / Death Cross works in practice
- Difference between crossover trading vs trend-following
- Why daily timeframe matters for this strategy
- Importance of look-ahead bias prevention

### Coding Concepts
- Working with date ranges in Python
- Calculating rolling averages with pandas
- Detecting crossover events using conditional logic
- Log returns vs simple returns
- Cumulative sum for return visualization

### Key Python Techniques
```python
# Rolling window calculation
df['sma50'] = df['Adj Close'].rolling(window=50).mean()

# Shift for look-ahead bias prevention
df['sma50'] = df['sma50'].shift(1)

# Multiple conditions with np.where
np.where((condition1) & (condition2), value_if_true, value_if_false)

# Log returns
np.log(price_today / price_yesterday)

# Cumulative sum for plotting
df['returns'].cumsum()
```

## 📊 Expected Behavior

### Typical Results Over 5 Years
- **Golden Crosses**: 2-4 signals
- **Death Crosses**: 2-4 signals
- **Total trading days**: Maybe 4-8 out of 1,250 days (0.5%)
- **Holding period**: Mostly in cash!

### This Strategy Is Extremely Selective
Unlike 5-EMA or 12-SMA strategies that trade frequently, this one:
- Only acts on major trend reversals
- Sits in cash 99%+ of the time
- Very low activity, high patience required

## 📈 When Does This Strategy Work?

### Works Best:
✅ Strong trending markets (clear multi-month trends)  
✅ When Reliance has major bull/bear cycles  
✅ Low-noise, smooth price movements  
✅ Patient, long-term traders  

### Struggles:
❌ Choppy/sideways markets (false crossovers)  
❌ When trends are short-lived  
❌ High volatility periods (whipsaws)  
❌ When you need frequent trading  

## 🛠️ Customization Guide

### Change the Stock
```python
# Try other Indian stocks
df = yf.download('TCS.NS', ...)  # TCS
df = yf.download('INFY.NS', ...)  # Infosys
df = yf.download('HDFCBANK.NS', ...)  # HDFC Bank
df = yf.download('^NSEI', ...)  # NIFTY 50
```

### Adjust Time Period
```python
# Test different periods
start1 = end1 - pd.Timedelta(days=365*3)  # 3 years
start1 = end1 - pd.Timedelta(days=365*10)  # 10 years
```

### Try Different SMA Periods
```python
# Faster crossovers (more signals)
m = 20
n = 50

# Slower crossovers (fewer signals)
m = 100
n = 300

# Classic stays the same
m = 50
n = 200
```

### Make It Hold Positions (Not Just Crossover Days)
```python
# Current: Only trades on crossover day
df['position'] = np.where((df['sma50'] > df['sma200'])
                        & (df['previous_day_sma50'] < df['previous_day_sma200']), 1, 0)

# Alternative: Hold position while SMA50 > SMA200
df['position'] = np.where(df['sma50'] > df['sma200'], 1, 0)
```

## ⚠️ Important Notes

### This Is a Crossover-Only Strategy
- Only takes positions on the exact day of crossover
- **Not** a trend-following hold strategy
- Sits in cash most of the time
- Very different from typical moving average strategies!

### Signals Are Rare
Over 5 years (1,250 trading days), you might only get:
- 3-4 Golden Crosses
- 3-4 Death Crosses
- Total active trading days: ~8
- That's **99.4% in cash!**

### Log Returns Explained
```python
# Simple return: (new - old) / old
# Log return: ln(new / old)

# Why use log returns?
✅ Better for compounding over time
✅ Symmetric (50% gain + 50% loss ≠ 0 with simple, but closer with log)
✅ Mathematically easier to work with
✅ Industry standard
```

## 🔧 Advanced Experiments

Once you understand the basics:
- Add volume confirmation (crossover + high volume)
- Test on different market conditions (bull vs bear markets)
- Combine with RSI or MACD for confirmation
- Add stop-loss/take-profit levels
- Test holding the position until next crossover (not just one day)

## 📁 Code Structure

```python
1. Import libraries
2. Set date range (5 years)
3. Download Reliance data (daily)
4. Calculate log returns
5. Calculate SMA50 and SMA200
6. Shift SMAs to avoid look-ahead bias
7. Create previous day SMA columns
8. Detect crossover points
9. Calculate strategy returns
10. Plot cumulative returns
11. Print performance comparison
```

## 🤝 Contributing

Feel free to:
- Test on different stocks
- Try different SMA periods
- Experiment with hold-until-next-crossover logic
- Add more performance metrics
- Share your results!

## ⚖️ Disclaimer

**FOR EDUCATIONAL PURPOSES ONLY**

This project demonstrates:
- Classic crossover trading strategy
- Python programming for finance
- Proper backtesting techniques (avoiding look-ahead bias)

This is NOT:
- Financial advice
- A guaranteed money-making system
- Ready for live trading

Always do your own research!


## 👨‍💻 Author

[Prince Saini]

---

**⭐ If this helped you understand crossover strategies, please star the repo!**
