# Basic of Trading

A practical primer for trading stocks, forex, and crypto — written for a personal investment analysis project.

---

## 1. What Is Trading?

Trading is buying and selling financial assets to profit from price movements. Unlike investing (buy-and-hold for years), trading focuses on shorter timeframes — days, hours, or even minutes.

**Key difference:**
- **Investor:** Cares about company fundamentals, holds through ups and downs, thinks in years
- **Trader:** Cares about price direction and timing, cuts losses fast, thinks in days/weeks

A good analysis engine should serve both — but the tools needed differ.

---

## 2. Trading Timeframes

| Style | Holding Period | Frequency | Goal |
|-------|---------------|-----------|------|
| **Scalping** | Seconds to minutes | Very high (100+ trades/day) | Tiny profits per trade, high volume |
| **Day Trading** | Minutes to hours | High (1-20 trades/day) | Close all positions before market close |
| **Swing Trading** | Days to weeks | Low (1-5 trades/week) | Capture medium-term swings |
| **Position Trading** | Weeks to months | Very low (1-2 trades/month) | Ride major trends |

**For your analysis engine:** Swing and position trading are the most model-amenable. Scalping and day trading require tick-level data and low-latency execution.

---

## 3. Asset Classes & Their Characteristics

### Stocks
- **Hours:** 09:00-15:00 WIB (IDX) or 09:30-16:00 ET (US)
- **Liquidity:** High for blue chips, low for penny stocks
- **Volatility:** Moderate (1-3% daily moves typical for IDX blue chips)
- **Settlement:** T+2 (IDX), T+1 (US starting 2024)
- **Key drivers:** Earnings, macro news, sector trends, fund flows

### Forex
- **Hours:** 24 hours (Mon-Fri), broken into sessions: Asia, London, US
- **Liquidity:** Highest of any market (EUR/USD most liquid)
- **Volatility:** Lower than stocks (0.5-1% daily typically)
- **Leverage:** Common (1:30 to 1:500 depending on broker/regulation)
- **Key drivers:** Interest rates, economic data, geopolitics, central bank policy

### Crypto
- **Hours:** 24/7
- **Liquidity:** Concentrated in top coins (BTC, ETH); thin on alts
- **Volatility:** Very high (5-20% daily moves not unusual)
- **Leverage:** Available on most exchanges (up to 1:100+)
- **Key drivers:** Regulatory news, adoption, network metrics, BTC dominance

---

## 4. Order Types

| Order Type | How It Works | When To Use |
|-----------|-------------|-------------|
| **Market** | Execute immediately at current best price | When speed matters more than price |
| **Limit** | Execute only at specified price or better | When you want to buy a dip or sell a rip |
| **Stop Loss** | Triggers a market sell when price hits a level | To cap losses (essential risk management) |
| **Stop Limit** | Triggers a limit order when price hits a stop | To avoid slippage on stop loss |
| **Take Profit** | Automatically closes position at profit target | To lock in gains without watching |
| **Trailing Stop** | Stop that moves with price as it goes in your favor | To let winners run while protecting profits |

**Minimum viable order types for any strategy:** Market, Limit, Stop Loss, Take Profit.

---

## 5. Analysis Approaches

### Technical Analysis (Price-Based)

Uses past price and volume data to predict future direction.

**Core concepts:**
- **Support & Resistance:** Price levels where buying/selling pressure emerges
- **Trend:** Direction (up, down, sideways) — "the trend is your friend"
- **Volume:** Confirms whether price moves are genuine
- **Indicators:** Mathematical formulas applied to price/volume

**Useful indicators (pick 2-3, don't overload):**

| Indicator | What It Measures | How To Use |
|-----------|-----------------|------------|
| Moving Average (SMA/EMA) | Average price over N periods | Trend direction; crossovers signal entries |
| RSI (Relative Strength Index) | Speed & magnitude of price changes (0-100) | Overbought (>70) / oversold (<30) |
| MACD | Trend momentum & direction | Signal line crossovers; divergence |
| Bollinger Bands | Volatility (standard deviation bands) | Bounce off bands = reversal; squeeze = breakout |
| Volume | Number of shares/contracts traded | Confirms trends (rising price + rising volume = healthy) |

**Chart timeframes:**
- 1m, 5m, 15m, 1h = Day trading
- 4h, Daily = Swing trading
- Weekly, Monthly = Position trading

### Fundamental Analysis (Value-Based)

Evaluates an asset's intrinsic value.

**For stocks:**
| Metric | What It Means | Signal |
|--------|--------------|--------|
| P/E (Price to Earnings) | Price per share / earnings per share | Low = undervalued; High = overvalued/growth |
| P/B (Price to Book) | Price per share / book value per share | Low < 1 = potential bargain |
| EPS Growth | Earnings growth YoY | Rising = healthy company |
| Debt-to-Equity | Total liabilities / shareholder equity | High = risky; Low = conservative |
| Dividend Yield | Annual dividend / share price | Income-focused; compare to bond yields |
| ROE (Return on Equity) | Net income / shareholder equity | High = efficient capital use |

**For forex:**
- Interest rate differentials (central bank rates)
- CPI, GDP, employment data
- Trade balance, current account

**For crypto:**
- Network activity (active addresses, transaction count)
- Hashrate (for PoW coins)
- Tokenomics (supply schedule, inflation rate)
- Staking yield

---

## 6. Risk Management (Most Important Section)

This is what separates professional traders from gamblers.

### Position Sizing
**The 1% Rule:** Never risk more than 1% of your total capital on a single trade.

Example: If you have IDR 10,000,000 capital and buy a stock at 5,000 with a stop loss at 4,800 (200 point risk):
- Max loss allowed = 1% × 10,000,000 = IDR 100,000
- Risk per share = 200
- Max shares = 100,000 / 200 = 500 shares

### Risk-to-Reward Ratio (R:R)
Always know your target before entering.

| R:R Ratio | Description | Viability |
|-----------|-------------|-----------|
| 1:1 | Risk same as reward | Breakeven after fees |
| 1:2 | Risk 1, target 2 | Profitable with 40% win rate |
| 1:3 | Risk 1, target 3 | Profitable with 25% win rate |

**Rule of thumb:** Minimum 1:2 R:R before entering any trade.

### Win Rate vs R:R

| Win Rate | Needed R:R for Profit |
|----------|----------------------|
| 60% | 1:0.7 |
| 50% | 1:1 |
| 40% | 1:1.5 |
| 30% | 1:2.3 |
| 20% | 1:4 |

Don't obsess over win rate. A 30% win rate with 1:3 R:R is profitable.

### Drawdown Management

| Drawdown | Recovery Needed |
|----------|----------------|
| -10% | +11% |
| -20% | +25% |
| -30% | +43% |
| -50% | +100% |
| -75% | +300% |

**Rules:**
- If drawdown hits 20%, stop trading. Re-evaluate strategy.
- Compound wins, don't compound losses.
- A "hot streak" is dangerous — it makes you increase position size right before a drawdown.

---

## 7. Common Trading Strategies

### Trend Following
- **Concept:** Identify trend direction, enter in trend direction, exit when trend reverses
- **Entry:** Price bounces off moving average or breaks above resistance
- **Exit:** Trendline break, MA cross, trailing stop
- **Best for:** Stocks (especially strong uptrends), forex majors
- **Analysis needed:** Trend identification, MA, volume confirmation

### Mean Reversion
- **Concept:** Prices that move too far from average tend to revert back
- **Entry:** RSI < 30 (oversold) or Bollinger Band bottom touch
- **Exit:** Return to mean (middle BB or MA)
- **Best for:** Range-bound markets, stocks with tight spreads
- **Analysis needed:** RSI, Bollinger Bands, support/resistance

### Breakout Trading
- **Concept:** Price breaks through support/resistance with volume — momentum continues
- **Entry:** Price closes above resistance with above-average volume
- **Exit:** Trailing stop or next resistance level
- **Best for:** High-volatility stocks, crypto, earnings plays
- **Analysis needed:** Support/resistance levels, volume analysis

### Swing Trading (Sweet Spot for Analysis Engine)
- **Concept:** Hold for 2-10 days, capturing medium-term price swings
- **Entry:** Technical setup (e.g., bullish flag, MA bounce) + fundamental catalyst
- **Exit:** Profit target or stop loss
- **Best for:** IDX stocks, US large caps, forex pairs
- **Analysis needed:** Daily/4H chart patterns, RSI, MACD, fundamental filter

---

## 8. Trading Psychology

**Common psychological pitfalls:**

| Pitfall | What It Looks Like | Fix |
|---------|-------------------|-----|
| **Revenge trading** | Lose money, immediately double down to "get it back" | Step away for 24 hours after a loss |
| **FOMO (Fear Of Missing Out)** | Chase a breakout that's already run 10%+ | Wait for pullback; there's always another trade |
| **Holding losers** | "It'll come back" — drops 5%, then 10%, then 20% | Pre-set stop loss, never move it further away |
| **Cutting winners short** | Profit 2%, sell, watch it go to 20% | Let winners run; use trailing stop |
| **Over-trading** | Too many trades, too many fees, too much noise | Max 1 trade per asset per day; written thesis required |
| **Confirmation bias** | Only look at data that supports your position | Force yourself to list reasons the trade will fail |

**The trading journal:** Track every trade. Date, entry, exit, size, reason, outcome, emotion. Review weekly. This is the single fastest way to improve.

---

## 9. Setting Up a Trading Workflow

### Pre-Market (15 min)
- Check overnight news (macro, sector, specific stocks)
- Review economic calendar (CPI, interest rate decisions, earnings)
- Identify key support/resistance levels
- Pre-set limit orders if applicable

### Market Hours
- Monitor open positions (don't watch constantly)
- Execute planned entries/exits
- Don't make impulsive decisions

### Post-Market (15 min)
- Log trades in journal
- Review what worked/didn't
- Scan for tomorrow's opportunities
- Update analysis models with new data

---

## 10. Technical Terms Glossary

| Term | Definition |
|------|------------|
| **Bid / Ask** | Bid = price buyer will pay; Ask = price seller wants |
| **Spread** | Difference between bid and ask |
| **Slippage** | Difference between expected and actual fill price |
| **Liquidity** | How easily an asset can be bought/sold without moving price |
| **Volatility** | How much price moves over a given period |
| **Drawdown** | Peak-to-trough decline during a period |
| **Sharpe Ratio** | Return per unit of risk (higher = better risk-adjusted returns) |
| **Beta** | Asset's volatility relative to market (beta > 1 = more volatile) |
| **Correlation** | How two assets move relative to each other (-1 to +1) |
| **Divergence** | Price and indicator moving in opposite directions (reversal signal) |
| **Candlestick** | Chart showing open, high, low, close for a period |
| **Gap** | Price opens significantly different from previous close |
| **Cup and Handle** | Bullish continuation pattern |
| **Head and Shoulders** | Reversal pattern (bearish at top, bullish at bottom) |
| **Double Top / Bottom** | Price tests same level twice then reverses |
| **Flag / Pennant** | Short-term continuation patterns |
| **Ascending Triangle** | Bullish continuation (higher lows, flat resistance) |

---

## Quick Reference: First Steps for a New Trader

1. **Learn one asset class first** — don't trade stocks, forex, and crypto simultaneously
2. **Paper trade** — practice with fake money for at least 1 month
3. **Master one strategy** — pick one of the four strategies above, trade it 50 times in paper
4. **Track everything** — every trade in a journal
5. **Start small** — risk 0.5% per trade, not 1%, until you're consistently profitable
6. **Fees matter** — a 0.3% fee on a 1% gain is 30% of your profit; minimize fees ruthlessly
7. **No strategy works all the time** — accept losses as part of the process
8. **Systems beat emotions** — a rules-based engine (like what you're building) eliminates psychology

---

*This is a foundational reference for a personal investment analysis project. Markets change, strategies evolve — the basics of risk management and discipline don't.*
