# Quantitative Asset Scoring Metric

**A practical framework for ranking financial assets by how easy they are to model, trade, and maintain with a rule-based quantitative system.**

**TL;DR:** If you are building a personal quant engine with limited time and zero infrastructure budget, start with the S&P 500, Dow Jones, EUR/USD, and USD/CAD. Avoid USD/IDR, NZD/USD, TSLA, NVDA, and Solana until you have dedicated data pipelines and automated execution. Indonesian blue-chips (BBCA, BBRI) sit in the middle: predictable structural trends exist, but daily-only data and local broker friction raise effort. See the Master Score Table at the bottom for the full ranking.

---

## 1. What This Metric Measures

This document scores 28 individual assets across three dimensions that matter for a solo data scientist running a Python-based, rule-based quant engine:

| Dimension | Question Answered | Scale |
|-----------|------------------|-------|
| **Predictability** | How well can statistical or ML models forecast this asset's price direction or volatility? | 1 = nearly random; 10 = strongly forecastable |
| **Effort** | How much data engineering, feature building, broker setup, and model maintenance does this asset require? | 1 = massive effort; 10 = trivial |
| **Speed** | How fast does the market move, how quickly do signals decay, and how forgiving is the reaction window? | 1 = lightning-fast/unforgiving; 10 = slow and forgiving |

**Why these three?** Predictability tells you whether the math can work. Effort tells you whether you will actually finish the pipeline before giving up. Speed tells you whether your signal will survive the time it takes you to notice it, run your model, and place an order — especially important when operating from Indonesia (WIB) on US or 24-hour markets.

### Composite Score

We compute a **Quantitative Modeling Score (QMS)** as a simple average of the three dimensions:

> **QMS = (Predictability + Effort + Speed) / 3**

A QMS of 8.0+ means the asset is an excellent candidate for a first quant model. 6.0–7.9 means viable but requires trade-offs. Below 6.0 means you are fighting the market, the data, or the clock.

**Important caveats:**
- Scores are ordinal and relative, not absolute. A 6/10 Predictability does not mean "60% accurate." It means "moderately forecastable compared to other assets in this list."
- Volatility and ATR figures are representative estimates based on 2024–2025 market conditions. Validate with live data (yfinance, Dukascopy) before trading.
- "Effort" assumes a solo developer with Python, free data sources (yfinance, FRED, Yahoo), and Indonesian brokerage access.

---

## 2. How to Read Each Asset Card

Every asset in Sections 3–7 follows this template:

```
### TICKER - Asset Name
Predictability: X/10 | Effort: X/10 | Speed: X/10 | QMS: X.X

Why the scores:
- Predictability: ...
- Effort: ...
- Speed: ...

Best modeling approach: ...
Key risks: ...
```

---

## 3. Forex (8 Assets)

All forex pairs trade 24 hours (Mon–Fri), settle in T+2, and offer deep liquidity on majors. Indonesian retail access is excellent for G10 pairs (XM, Exness, IC Markets, FBS) but poor for USD/IDR (local futures brokers only, limited API automation).

### 3.1 EUR/USD - Euro / US Dollar
Predictability: 4/10 | Effort: 9/10 | Speed: 6/10 | QMS: 6.3

**Why the scores:**
- **Predictability:** The most efficient market in forex. ARIMA/GARCH show essentially no directional edge (~50–51% accuracy). LSTM and XGBoost on daily bars can reach 52–55% before costs, but overfitting is high. The real value is volatility forecasting (GARCH works well), not direction. Macro signals (ECB vs Fed rate paths, 2Y/10Y spreads) last 2–5 days.
- **Effort:** Extremely easy. ECB and Fed data are free. yfinance/Dukascopy provide free daily/hourly data. Tick data is abundant and cheap. Indonesian retail brokers offer tight spreads and MT4/MT5 automation.
- **Speed:** Moderate. Typical daily ATR 50–80 pips. Signals decay over hours to days. Intraday and swing models are both viable. H1 to H4 bars work best for quant systems.

**Best modeling approach:** Macro regime models (carry, rate differentials) or volatility breakout systems. Avoid pure price-only ML for direction.
**Key risks:** ECB/Fed divergence shocks, NFP surprises, thin liquidity around 00:00 UTC.

---

### 3.2 GBP/USD - British Pound / US Dollar
Predictability: 4/10 | Effort: 8/10 | Speed: 4/10 | QMS: 5.3

**Why the scores:**
- **Predictability:** Similar to EUR/USD but with higher kurtosis and event risk. BOE/CPI surprises create fatter tails. Post-Brexit structural shifts make long-history models less stable. Expect 51–54% directional accuracy with heavy feature engineering.
- **Effort:** BOE and ONS data are free. Broker access is easy. The extra effort comes from tracking UK fiscal events (budget, gilt issuance) and wider stop-loss regimes.
- **Speed:** Faster than EUR/USD. Typical daily ATR 80–120 pips. Weekend gaps are more common. Requires wider stops and faster reaction.

**Best modeling approach:** Event-driven models around BOE/CPI releases; GARCH for tail risk.
**Key risks:** BOE surprise hikes/cuts, UK fiscal policy shocks, weekend gaps.

---

### 3.3 USD/JPY - US Dollar / Japanese Yen
Predictability: 5/10 | Effort: 8/10 | Speed: 5/10 | QMS: 6.0

**Why the scores:**
- **Predictability:** Carry-trade dynamics (JPY as funding currency) create a slow, exploitable drift. Risk-on/risk-off regime switches are identifiable with ML better than for EUR/GBP. BOJ intervention spikes are hard to model but the underlying drift is persistent. GARCH struggles with BOJ-driven tail events.
- **Effort:** BOJ and Fed data are free. JGB yield tracking adds moderate complexity. Indonesian brokers offer the pair widely but swap/rollover costs on JPY shorts can erode carry profits.
- **Speed:** Mixed. Typical daily ATR 70–100 pips, but intervention days see 200+ pips. Carry signals last 1–2 weeks; news signals last hours.

**Best modeling approach:** Regime-switching models (risk appetite proxy + yield spread); carry accumulation with hard stops.
**Key risks:** BOJ yield curve control tweaks, verbal intervention, sudden safe-haven flows.

---

### 3.4 AUD/USD - Australian Dollar / US Dollar
Predictability: 4/10 | Effort: 8/10 | Speed: 6/10 | QMS: 6.0

**Why the scores:**
- **Predictability:** Slightly stronger edge than EUR/GBP if you incorporate China macro (PMI, credit impulse) and iron ore. XGBoost with commodity features can modestly outperform random walk. ARIMA remains ineffective.
- **Effort:** RBA data is free. China data is partially free (NBS PMI). Iron ore and gold prices are free. Must handle China timezone releases (often during Asian session).
- **Speed:** Moderate. Typical daily ATR 50–80 pips. China stimulus announcements cause regime jumps.

**Best modeling approach:** China-commodity proxy models; RBA decision drift.
**Key risks:** China policy surprises, RBA emergency moves, terms-of-trade shocks.

---

### 3.5 USD/CHF - US Dollar / Swiss Franc
Predictability: 3/10 | Effort: 7/10 | Speed: 7/10 | QMS: 5.7

**Why the scores:**
- **Predictability:** Very noisy. The SNB creates extreme tail events that standard models miss (2015 depeg, recurring intervention). GARCH underestimates tail risk. Directional accuracy barely above 50%. SNB sight deposits are a useful intervention proxy but not predictive of timing.
- **Effort:** SNB data is available. Must monitor EUR/CHF correlation and intervention thresholds. Spreads are wider than EUR/USD at some Indonesian retail brokers.
- **Speed:** Normally slow (ATR 40–70 pips), but gaps of 200–500 pips are possible on SNB moves. Daily timeframe is safest.

**Best modeling approach:** Avoid directional modeling. Use as a portfolio diversifier or volatility hedge.
**Key risks:** SNB unannounced intervention, EUR/CHF floor changes, liquidity evaporations.

---

### 3.6 USD/CAD - US Dollar / Canadian Dollar
Predictability: 5/10 | Effort: 8/10 | Speed: 6/10 | QMS: 6.3

**Why the scores:**
- **Predictability:** Oil correlation (~0.5 to 0.7 with WTI) provides a genuine and relatively stable edge. XGBoost/LSTM with energy features outperform baseline more reliably than other G10 pairs. Seasonal patterns (winter heating demand) add minor edge.
- **Effort:** BOC and EIA data are free. Feature engineering is straightforward: lagged oil returns, inventory surprises, BOC/Fed rate spread.
- **Speed:** Moderate. Typical daily ATR 60–90 pips. Oil-driven moves are usually telegraphed by inventory reports.

**Best modeling approach:** Oil-beta model with inventory surprise overlay; BOC decision drift.
**Key risks:** OPEC+ surprises, Canadian employment shocks, US oil production spikes.

---

### 3.7 NZD/USD - New Zealand Dollar / US Dollar
Predictability: 3/10 | Effort: 6/10 | Speed: 4/10 | QMS: 4.3

**Why the scores:**
- **Predictability:** Thin liquidity and high noise make overfitting easy. Directional accuracy rarely exceeds random walk after transaction costs. Dairy auction prices (GDT) are a unique feature but low frequency and noisy.
- **Effort:** RBNZ and GDT data are free, but dairy auctions are biweekly. Must handle illiquidity proxies. Not all Indonesian brokers offer competitive spreads or API support for NZD/USD.
- **Speed:** Fast. ATR 60–100 pips, but effective volatility is higher due to slippage and gaps. Signals decay within hours to a day.

**Best modeling approach:** Generally avoid as a standalone quant target. Use only in multi-pair portfolio models.
**Key risks:** Illiquidity gaps, wide spreads during Asian session, RBNZ surprises.

---

### 3.8 USD/IDR - US Dollar / Indonesian Rupiah
Predictability: 2/10 | Effort: 3/10 | Speed: 3/10 | QMS: 2.7

**Why the scores:**
- **Predictability:** EM FX dominated by central bank reaction functions and political risk. Standard ARIMA/GARCH/LSTM fail to capture Bank Indonesia (BI) intervention timing. Random-walk with drift is the hard baseline to beat. The only persistent signal is the long-term USD/IDR drift driven by inflation differentials, but timing entries is nearly impossible.
- **Effort:** Very high. BI publishes reference rates, but granular tick/quote data is expensive and hard to source. No free high-frequency API. Indonesian residents typically access via local futures brokers (Monex, Central Capital) using manual platforms or PAMM accounts rather than clean REST APIs. Feature engineering must model intervention probability and political cycles.
- **Speed:** Very fast and jumpy. Daily range frequently 1–2% in IDR terms, with non-smooth price action. BI intervention often kills momentum instantly. Intraday quant execution is nearly impossible due to liquidity fragmentation.

**Best modeling approach:** Avoid for directional quant models. Use only as a macro hedge or portfolio risk overlay.
**Key risks:** BI unannounced spot/forward intervention, political/fiscal shocks, regional EM contagion.

---

## 4. Cryptocurrency (4 Assets)

Crypto trades 24/7 with no close. Data is abundant but noisy. On-chain metrics add unique features not available in traditional markets. Volatility is very high, making position sizing and stop-loss discipline critical.

### 4.1 BTC/USD - Bitcoin
Predictability: 4/10 | Effort: 7/10 | Speed: 3/10 | QMS: 4.7

**Why the scores:**
- **Predictability:** Better than altcoins due to deeper history and institutional flow data. On-chain metrics (exchange flows, long-term holder supply, MVRV ratio, NUPL) provide genuine edges that persist 3–7 days. Halving cycles create 4-year regime patterns. However, 60–80% of daily variance is still driven by macro risk appetite and BTC-specific news. Directional models achieve 54–58% accuracy when combining on-chain + macro features. Funding rate perpetual basis is a strong mean-reversion signal.
- **Effort:** Moderate. Exchange APIs (Binance, Bybit, OKX) are free and well-documented. On-chain data (Glassnode, CryptoQuant) requires paid subscriptions for high-resolution feeds. Free on-chain explorers (mempool.space, blockchain.com) suffice for basic features. Data cleaning is significant: bad ticks, exchange outages, and flash crashes require outlier filtering.
- **Speed:** Very fast. Typical daily volatility 3–5% (annualized 60–80%). Intraday swings of 5–10% are common during risk-off events. Signals decay within hours to 1–2 days. A quant system must react within minutes on volatile days.

**Best modeling approach:** On-chain + macro regime model; funding rate mean-reversion; halving cycle positioning.
**Key risks:** Regulatory shocks, exchange failures, macro correlations spiking during risk-off, stablecoin depeg events.

---

### 4.2 ETH/USD - Ethereum
Predictability: 3/10 | Effort: 6/10 | Speed: 3/10 | QMS: 4.0

**Why the scores:**
- **Predictability:** Correlates heavily with BTC (beta ~0.8–0.9) but with higher idiosyncratic risk. On-chain features are richer than BTC (gas usage, validator staking flows, DeFi TVL, L2 activity) but noisier. ETH/BTC ratio has persistent trend regimes driven by narrative shifts ("Ultrasound money," ETF approvals, L2 competition). Directional accuracy without BTC-lead features is poor (~52–54%).
- **Effort:** Moderate-to-high. Must track Ethereum-specific ecosystem metrics (Etherscan, Dune Analytics, DefiLlama). Staking unlock schedules (Shapella, future upgrades) create supply shocks that must be modeled. More data sources than BTC but also more noise.
- **Speed:** Very fast. Daily volatility 4–6% (annualized 70–90%), often exceeding BTC. DeFi contagion or bridge hacks cause rapid decoupling from BTC.

**Best modeling approach:** BTC-lead model with ETH-specific on-chain deviations; ETH/BTC ratio trend; event-driven (upgrade/ETF) positioning.
**Key risks:** Smart contract bugs, L2 competition diluting value, SEC classification risk, staking unlock supply shocks.

---

### 4.3 SOL/USD - Solana
Predictability: 2/10 | Effort: 4/10 | Speed: 2/10 | QMS: 2.7

**Why the scores:**
- **Predictability:** Highly speculative, retail-driven, and narrative-sensitive. Correlation with BTC is unstable (0.6–0.85 depending on risk regime). On-chain data (active addresses, transaction count, TVL) is heavily gamed and less reliable than Ethereum. Network outages have historically caused 10–20% single-day drops. Directional models without heavy NLP/sentiment overlays struggle to beat 51–53%.
- **Effort:** High relative to asset maturity. Must monitor validator health, network upgrade schedules, and FTX estate selling pressure. Data availability is lower than BTC/ETH (fewer free APIs, less historical data).
- **Speed:** Extremely fast. Daily volatility 6–12% (annualized 100–150%). Intraday moves of 10–15% are routine. Signals decay within minutes to hours.

**Best modeling approach:** Only viable as a momentum/sentiment system with very tight risk controls. Avoid for statistical mean-reversion.
**Key risks:** Network outages, FTX estate liquidations, competitor chain launches, whale wallet concentration.

---

### 4.4 XRP/USD - Ripple
Predictability: 2/10 | Effort: 5/10 | Speed: 2/10 | QMS: 3.0

**Why the scores:**
- **Predictability:** Litigation-driven price action (SEC case resolution, appeal cycles) is fundamentally non-stationary and unmodelable with standard quant features. Outside of legal news, XRP trades as a high-beta altcoin with heavy retail/whale concentration. Correlation with BTC is moderate (~0.5–0.7). Exchange listing/delisting rumors cause discrete jumps.
- **Effort:** Moderate. Must track SEC litigation docket, exchange compliance changes, and Ripple ODL (On-Demand Liquidity) corridor announcements. On-chain data is less transparent than BTC/ETH due to partially closed ledger.
- **Speed:** Extremely fast. Daily volatility 5–10% (annualized 90–130%). Legal news gaps of 20–50% have occurred.

**Best modeling approach:** Avoid for rule-based quant. Only tradeable with event-driven NLP and legal-docket monitoring.
**Key risks:** SEC appeal outcomes, exchange delistings, regulatory divergence across jurisdictions, large escrow releases.

---

## 5. US Equity Indices & Blue-Chip Stocks (7 Assets)

US markets trade 09:30–16:00 ET (21:30–04:00 WIB). T+1 settlement as of 2024. Indonesian-based traders face timezone asymmetry: all overnight gaps and pre-market moves happen while you sleep.

### 5.1 ^GSPC - S&P 500 Index
Predictability: 4/10 | Effort: 10/10 | Speed: 8/10 | QMS: 7.3

**Why the scores:**
- **Predictability:** The most efficient equity market in the world. Short-term directional R-squared from macro/technical features is 5–15%. Futures basis (E-mini S&P /ES) and VIX term structure provide marginal edge. FOMC and CPI days cause gap risk but direction is a coin flip; post-event volatility mean-reversion is the more reliable signal. Best suited for regime-switching or risk-parity models, not directional day-trading.
- **Effort:** The easiest asset to model. yfinance (^GSPC), FRED macro data (DGS10, DFF, UNRATE, CPI), CFTC COT, and CBOE VIX are all free. No earnings calendar required. Feature engineering is low complexity. Rebalance monthly or quarterly.
- **Speed:** Slow and forgiving. Typical daily volatility ~0.9–1.3% of spot. Signals decay over hours to days. Minutes-to-hours reaction time is acceptable. Daily or weekly bars work well.

**Best modeling approach:** Macro regime model (rates, earnings yield, credit spreads); volatility targeting; risk-parity allocation.
**Key risks:** Black swan gaps, FOMC/CPI surprise reactions, geopolitical shocks.

---

### 5.2 ^IXIC - NASDAQ Composite Index
Predictability: 5/10 | Effort: 9/10 | Speed: 6/10 | QMS: 6.7

**Why the scores:**
- **Predictability:** Tech-heavy concentration (~45–50% in top 10 names) means it partially behaves like a mega-cap tech basket. Macro rates sensitivity is higher than SPX. Sector rotation (XLK vs XLF/XLE) and semiconductor indices (SOX) add predictive features. Slightly more forecastable than SPX for medium-term trend models because growth/rates beta is stronger and more persistent.
- **Effort:** Very similar to SPX. yfinance (^IXIC) and NQ futures data are free. Add XLK/SOX ratio and 10Y–2Y yield curve for tech sensitivity. No individual earnings calls to track.
- **Speed:** Moderate. Typical daily volatility ~1.2–1.8% of spot. Faster than SPX due to higher beta and concentration. Intraday models need sub-hour reaction on FOMC/earnings days.

**Best modeling approach:** Rates/growth regime model; tech sentiment overlay (SOX, semicap equipment data); momentum on 4h/daily bars.
**Key risks:** Mega-cap tech earnings weeks (AAPL, MSFT, NVDA, AMZN, GOOGL), FOMC duration risk, semiconductor cycle turns.

---

### 5.3 ^DJI - Dow Jones Industrial Average
Predictability: 5/10 | Effort: 9/10 | Speed: 9/10 | QMS: 7.7

**Why the scores:**
- **Predictability:** Price-weighted construction creates mild inefficiency and rebalancing effects. Less algo/ETF arbitrage coverage than SPX. Industrial and value tilt can be exploited via sector rotation signals. However, 30-stock sample means idiosyncratic shocks have disproportionate impact.
- **Effort:** Very easy. yfinance (^DJI), YM futures data, standard macro features. Only 30 earnings dates to track. Index constituent changes are rare.
- **Speed:** The slowest and most forgiving of the seven assets. Typical daily volatility ~0.8–1.1% of spot. Signals last days. Can trade on daily close with minimal slippage.

**Best modeling approach:** Slow trend/macro model; value-rotation proxy; daily/weekly regime switching.
**Key risks:** Single-name shocks (UNH, BA), price-weighting anomalies, index rebalancing gaps.

---

### 5.4 AAPL - Apple Inc.
Predictability: 6/10 | Effort: 7/10 | Speed: 5/10 | QMS: 6.0

**Why the scores:**
- **Predictability:** Highly liquid with rich derivatives data. Strong seasonal and event-driven patterns (post-earnings drift, iPhone cycle pre-announcement). Options flow and institutional holding changes are moderately predictive. ~50% of variance is market beta, leaving limited uncorrelated edge. Post-earnings drift is a well-documented 3–10 day effect.
- **Effort:** Moderate. yfinance, Alpha Vantage, Polygon all excellent. Earnings calendars freely available. Must add services revenue growth proxy, China market sentiment, USD/CNY, and smartphone shipment data for best results. Extended-hours data useful around earnings.
- **Speed:** Moderate. Typical daily volatility ~1.5–2.2%. Intraday signals decay within hours; swing signals last 3–10 days around product/earnings cycles.

**Best modeling approach:** Post-earnings drift capture; iPhone cycle seasonality; services revenue momentum.
**Key risks:** China demand shocks, App Store regulatory pressure, CEO health/transition risk, product launch flops.

---

### 5.5 MSFT - Microsoft Corp.
Predictability: 6/10 | Effort: 7/10 | Speed: 6/10 | QMS: 6.3

**Why the scores:**
- **Predictability:** Similar to AAPL but with stronger enterprise/AI recurring revenue visibility. Azure growth and AI capex spend provide fundamental momentum signals that persist longer than consumer-hardware cycles. Less retail noise than TSLA/NVDA, making statistical models cleaner.
- **Effort:** Moderate. Same data abundance as AAPL. Add cloud capex proxies (data center spending reports, server shipment data), GitHub activity trends, and enterprise software sentiment. Extended-hours useful for earnings but less critical than NVDA/TSLA.
- **Speed:** Moderate-to-slow for a single stock. Typical daily volatility ~1.3–1.8%. Swing models work well on 3–10 day horizons.

**Best modeling approach:** Azure growth proxy; AI sentiment trend; Copilot adoption milestones.
**Key risks:** OpenAI partnership disruption, Azure slowdown, antitrust action, AI capex bubble deflation.

---

### 5.6 NVDA - NVIDIA Corp.
Predictability: 3/10 | Effort: 5/10 | Speed: 2/10 | QMS: 3.3

**Why the scores:**
- **Predictability:** Extremely difficult to forecast directionally. 10–20% post-earnings moves are common. Narrative shifts on AI demand (data center capex, China export bans, Blackwell/Rubin chip cycles) dominate statistical signals. Volatility clustering and options skew are the most reliable quant features; directional price models have low Sharpe unless restricted to specific regimes.
- **Effort:** High despite abundant data. Must track: TSMC revenue/monthly sales, server ODM build plans, China export rule changes, AI training cluster deployment news, competitor AMD/Intel product launches. Options flow is highly relevant. Pre-market/after-hours data is required due to frequent overnight gaps.
- **Speed:** Very fast and unforgiving. Typical daily volatility ~3.0–5.0% (higher around earnings). Signals decay within minutes to hours. Sub-hour reaction time required on news days.

**Best modeling approach:** Options skew/volatility regime model; semiconductor inventory cycle; event-window only trading.
**Key risks:** Earnings guidance misses, China semiconductor restrictions, AI demand bubble burst, competitor product leapfrogs.

---

### 5.7 TSLA - Tesla Inc.
Predictability: 2/10 | Effort: 4/10 | Speed: 1/10 | QMS: 2.3

**Why the scores:**
- **Predictability:** The most sentiment-driven, retail/option-flow dominated name in this list. Traditional statistical/ML models perform poorly for directional forecasting. NLP/sentiment and options gamma exposure models show slightly better results, but Sharpe ratios remain low. CEO-related headline risk is non-stationary and unmodelable with standard features.
- **Effort:** Very high relative to predictability. Must track: EV delivery estimates, energy/storage business splits, autonomous driving regulatory milestones, competitor pricing (BYD, Li Auto), raw material costs (lithium), and CEO social-media activity. Social sentiment APIs (Reddit, X/Twitter) add cost and complexity.
- **Speed:** The fastest and least forgiving asset in this set. Typical daily volatility ~3.0–5.5%. Intraday swings of 8–12% occur around delivery numbers or product announcements. Signals decay within minutes.

**Best modeling approach:** Avoid for rule-based quant unless building dedicated high-frequency gamma/sentiment systems.
**Key risks:** Delivery miss shocks, autonomous driving accidents/regulatory blocks, CEO headline risk, EV price war margin compression.

---

## 6. Indonesian Equity Stocks (7 Assets)

IDX stocks trade 09:00–15:00 WIB only, Monday–Friday. No pre-market or after-hours. Settlement is T+2. The market is retail-heavy, creating momentum and herding patterns that differ from institutional-driven US markets. Foreign fund flows are a major driver.

### 6.1 BBCA - Bank Central Asia
Predictability: 6/10 | Effort: 6/10 | Speed: 7/10 | QMS: 6.3

**Why the scores:**
- **Predictability:** The most liquid and institutionally held IDX stock. Foreign inflow/outflow patterns are moderately predictable using BI rate differentials, JCI index trend, and USD/IDR drift. BBCA exhibits strong momentum persistence (2–4 week horizons) due to its status as a foreign anchor name. Earnings are stable and seasonal deposit patterns create minor cyclicality.
- **Effort:** Moderate. IDX daily data is available via free sources (idx.co.id historical, some Yahoo coverage). Must track: BI 7-day reverse repo, Indonesia GDP/credit growth, foreign ownership percentage, and JCI breadth. No intraday data for feature engineering.
- **Speed:** Slow and forgiving for IDX. Typical daily volatility ~1.5–2.5%. No overnight gap risk (market is closed). Swing signals last 1–3 weeks.

**Best modeling approach:** JCI momentum + foreign flow proxy; BI rate cycle positioning; quarterly earnings drift.
**Key risks:** Sudden foreign fund exodus, BI emergency rate hike, banking sector regulatory caps.

---

### 6.2 BBRI - Bank Rakyat Indonesia
Predictability: 5/10 | Effort: 6/10 | Speed: 7/10 | QMS: 6.0

**Why the scores:**
- **Predictability:** Similar to BBCA but with higher government-policy sensitivity (UMKM lending mandates, interest rate caps on microloans). Foreign ownership is significant but less stable than BBCA. Microfinance portfolio quality (NPL trends) is a unique predictive feature. Political cycle sensitivity is higher due to state-ownership and social-mandate lending.
- **Effort:** Same data sources as BBCA. Add government microfinance policy announcements and subsidized loan program calendars.
- **Speed:** Similar to BBCA. Daily volatility ~1.5–2.5%. Swing signals viable.

**Best modeling approach:** Microfinance NPL trend model; government lending mandate cycle; JCI momentum.
**Key risks:** Government-mandated interest rate caps, UMKM NPL spikes, political pressure on dividend policy.

---

### 6.3 BMRI - Bank Mandiri
Predictability: 5/10 | Effort: 6/10 | Speed: 7/10 | QMS: 6.0

**Why the scores:**
- **Predictability:** State-owned, diversified across corporate and retail banking. More exposed to commodity-sector corporate loans (coal, palm oil) than BBCA/BBRI, adding a commodity-cycle beta. Foreign flows are less sticky. Earnings are more volatile due to corporate NPL cycles.
- **Effort:** Same as BBCA. Add coal/palm oil price proxies for corporate loan quality forecasting.
- **Speed:** Similar to BBCA. Daily volatility ~1.5–2.5%.

**Best modeling approach:** Commodity cycle proxy (coal/palm oil); corporate loan growth momentum; JCI trend.
**Key risks:** Commodity sector NPL surges, state-ownership dividend policy changes, SOE governance concerns.

---

### 6.4 TLKM - Telkom Indonesia
Predictability: 5/10 | Effort: 6/10 | Speed: 8/10 | QMS: 6.3

**Why the scores:**
- **Predictability:** Defensive telecom utility with stable cash flows. Lower volatility than banks. Predictable quarterly earnings and dividend policy. However, growth is limited (Indonesia mobile market saturation), making momentum signals weaker. Data center/digital business (Telkomsel, IndiHome) adds a minor growth overlay.
- **Effort:** Low-to-moderate. Standard IDX data plus Indonesian telecom subscriber metrics (APJII reports).
- **Speed:** Slow. Daily volatility ~1.2–2.0%. Excellent for slow swing models and dividend-capture strategies.

**Best modeling approach:** Dividend yield + growth proxy; defensive rotation model; data center revenue tracking.
**Key risks:** Regulatory tariff cuts, data center competition, satellite/mobile ARPU decline.

---

### 6.5 ASII - Astra International
Predictability: 4/10 | Effort: 6/10 | Speed: 6/10 | QMS: 5.3

**Why the scores:**
- **Predictability:** Conglomerate with auto (Toyota), mining, agribusiness, and financial services exposure. Diversification reduces single-sector edge but adds macro sensitivity (auto sales, coal, CPO). Foreign flows are significant but volatile. Earnings are complex to model due to segment heterogeneity.
- **Effort:** Moderate. Must track: Indonesia auto sales data (Gaikindo), coal/CPO prices, and banking subsidiary performance.
- **Speed:** Moderate. Daily volatility ~1.8–2.8%. Multi-segment exposure creates noisy price action.

**Best modeling approach:** Auto sales momentum + commodity proxy; conglomerate NAV discount model.
**Key risks:** Auto sales cyclical downturn, coal price collapse, subsidiary earnings miss.

---

### 6.6 GOTO - GoTo Gojek Tokopedia
Predictability: 2/10 | Effort: 4/10 | Speed: 3/10 | QMS: 3.0

**Why the scores:**
- **Predictability:** Growth-tech stock with no current profitability, making traditional valuation models irrelevant. Price is driven by GMV growth narratives, funding runway, and path-to-profitability commentary. Correlation with JCI is weak; it trades more like a speculative growth equity with high retail participation. Post-IPO lockup expiries and convertible bond dynamics create additional non-stationarity.
- **Effort:** High relative to signal quality. Must track: GMV growth proxies, ride-hailing/food delivery competitive dynamics (Grab, ShopeeFood), e-commerce regulatory changes, and cash-burn runway.
- **Speed:** Very fast for IDX. Daily volatility ~3–5%. Retail-driven momentum can swing 10%+ in a week. Signals decay within days.

**Best modeling approach:** Avoid for rule-based quant. Only viable with high-risk momentum and narrative-tracking overlays.
**Key risks:** Cash runway depletion, competitive share loss, regulatory fee caps on delivery/payments, foreign exit from growth names.

---

### 6.7 BYAN - Bayan Resources
Predictability: 3/10 | Effort: 5/10 | Speed: 4/10 | QMS: 4.0

**Why the scores:**
- **Predictability:** Pure-play coal miner. Price is essentially a leveraged play on Newcastle coal prices, USD/IDR, and Indonesian mining policy (export bans, DMO quotas). These drivers are exogenous and hard to time. Earnings are highly cyclical. Foreign ownership is significant but flows are tied to commodity supercycle sentiment.
- **Effort:** Moderate. Must track: Newcastle coal futures, Indonesia DMO policy, mining export license changes, and USD/IDR.
- **Speed:** Fast. Daily volatility ~2.5–4.0%. Coal price gaps of 5–10% in a day are possible during supply shocks.

**Best modeling approach:** Coal price momentum + USD/IDR overlay; commodity supercycle regime model.
**Key risks:** Coal price collapse, DMO/export ban policy shifts, ESG divestment flows, mining permit revocation.

---

## 7. Commodities (2 Assets)

Commodities trade nearly 24 hours via futures but have distinct drivers (supply/demand, geopolitics, inventory) that differ from equity/forex markets.

### 7.1 XAU/USD - Gold
Predictability: 4/10 | Effort: 8/10 | Speed: 7/10 | QMS: 6.3

**Why the scores:**
- **Predictability:** Real rates (10Y TIPS yield) and USD index explain ~60–70% of gold variance over multi-month horizons. This is a genuine, stable edge. However, short-term directional forecasting is poor (noise dominates). Safe-haven flows (geopolitical shocks) create jumps that models miss. Seasonal patterns (Indian wedding season, Chinese New Year) add minor edge.
- **Effort:** Low-to-moderate. yfinance (GC=F), FRED (TIPS, DXY, real rates), and World Gold Council data are free. Feature engineering is straightforward: real rate, DXY, VIX, geopolitical risk index.
- **Speed:** Slow-to-moderate. Typical daily volatility ~0.8–1.2%. Signals persist for days to weeks. Intraday noise is high; daily/weekly models work best.

**Best modeling approach:** Real-rate regime model; USD strength overlay; seasonal demand proxy.
**Key risks:** Central bank buying/selling surprises, crypto substitution narrative, sharp USD rallies, geopolitical de-escalation.

---

### 7.2 WTI/USD - Crude Oil (West Texas Intermediate)
Predictability: 3/10 | Effort: 6/10 | Speed: 4/10 | QMS: 4.3

**Why the scores:**
- **Predictability:** EIA inventory reports (weekly) create predictable 1–2 day mean-reversion or momentum depending on the surprise magnitude. OPEC+ announcements cause discrete jumps. Long-term supply/demand models are decent (shale productivity, SPR releases, China oil imports) but short-term price is dominated by geopolitical noise that is unmodelable.
- **Effort:** Moderate. EIA data is free. Must track: OPEC+ meeting calendars, SPR release schedules, Iran/Russia sanction evasion volumes, and China oil import data. Futures curve (contango/backwardation) is a useful but complex feature.
- **Speed:** Fast. Typical daily volatility ~2.0–3.5%. Inventory days see 3–5% moves. Signals decay within 1–2 days.

**Best modeling approach:** EIA inventory surprise mean-reversion; futures curve shape; OPEC+ event windows only.
**Key risks:** OPEC+ surprise cuts, geopolitical supply disruptions (Iran, Russia, Venezuela), recession demand collapse, energy transition policy shocks.

---

## 8. Master Score Table

| Rank | Asset | Category | Predictability | Effort | Speed | QMS | Verdict |
|------|-------|----------|----------------|--------|-------|-----|---------|
| 1 | ^DJI | US Index | 5 | 9 | 9 | **7.7** | Best first target |
| 2 | ^GSPC | US Index | 4 | 10 | 8 | **7.3** | Easiest data |
| 3 | EUR/USD | Forex | 4 | 9 | 6 | **6.3** | Best forex starter |
| 3 | USD/CAD | Forex | 5 | 8 | 6 | **6.3** | Oil edge |
| 3 | BBCA | IDX Stock | 6 | 6 | 7 | **6.3** | Best IDX stock |
| 3 | MSFT | US Stock | 6 | 7 | 6 | **6.3** | Best single-stock |
| 3 | XAU/USD | Commodity | 4 | 8 | 7 | **6.3** | Best commodity |
| 3 | TLKM | IDX Stock | 5 | 6 | 8 | **6.3** | Defensive IDX |
| 9 | ^IXIC | US Index | 5 | 9 | 6 | **6.7** | Tech macro |
| 10 | USD/JPY | Forex | 5 | 8 | 5 | **6.0** | Carry edge |
| 10 | AUD/USD | Forex | 4 | 8 | 6 | **6.0** | China proxy |
| 10 | AAPL | US Stock | 6 | 7 | 5 | **6.0** | Event drift |
| 10 | BBRI | IDX Stock | 5 | 6 | 7 | **6.0** | Policy sensitivity |
| 10 | BMRI | IDX Stock | 5 | 6 | 7 | **6.0** | Commodity beta |
| 15 | GBP/USD | Forex | 4 | 8 | 4 | **5.3** | Event risk |
| 15 | ASII | IDX Stock | 4 | 6 | 6 | **5.3** | Conglomerate noise |
| 17 | USD/CHF | Forex | 3 | 7 | 7 | **5.7** | SNB tail risk |
| 18 | BTC/USD | Crypto | 4 | 7 | 3 | **4.7** | On-chain edge |
| 19 | ETH/USD | Crypto | 3 | 6 | 3 | **4.0** | BTC-lead required |
| 19 | BYAN | IDX Stock | 3 | 5 | 4 | **4.0** | Coal leverage |
| 19 | WTI/USD | Commodity | 3 | 6 | 4 | **4.3** | Inventory only |
| 22 | NZD/USD | Forex | 3 | 6 | 4 | **4.3** | Illiquidity |
| 23 | XRP/USD | Crypto | 2 | 5 | 2 | **3.0** | Litigation noise |
| 23 | GOTO | IDX Stock | 2 | 4 | 3 | **3.0** | Speculative |
| 25 | NVDA | US Stock | 3 | 5 | 2 | **3.3** | AI narrative risk |
| 26 | SOL/USD | Crypto | 2 | 4 | 2 | **2.7** | Network risk |
| 26 | USD/IDR | Forex | 2 | 3 | 3 | **2.7** | BI intervention |
| 28 | TSLA | US Stock | 2 | 4 | 1 | **2.3** | Sentiment chaos |

---

## 9. Practical Recommendations by Phase

### Phase 1: Build the Engine (Month 1–2)
Target the highest QMS assets to prove your pipeline works before tackling harder problems.

**Recommended basket:** ^GSPC, ^DJI, EUR/USD, USD/CAD, BBCA, TLKM
- All have free, abundant data.
- All tolerate daily-bar modeling (no need for expensive tick data or 24/7 infrastructure).
- BBCA and TLKM let you test the model on IDX without the complexity of GOTO or BYAN.
- ^GSPC and ^DJI let you build macro features that transfer to almost every other asset.

### Phase 2: Add Alpha (Month 3–4)
Once daily pipelines are stable, add assets with higher predictability but more effort.

**Recommended adds:** AAPL, MSFT, USD/JPY, AUD/USD, XAU/USD, BBRI
- AAPL and MSFT add single-stock earnings event dynamics.
- USD/JPY and AUD/USD add macro-differential modeling.
- XAU/USD introduces real-rate features that are highly transferable.
- BBRI adds Indonesian policy sensitivity without the pure-play risk of BYAN.

### Phase 3: Advanced / Specialist (Month 5+)
Only attempt these if you have automated execution, real-time data, and time to maintain complex pipelines.

**Conditional adds:** BTC/USD (on-chain pipeline required), ETH/USD (DeFi TVL feeds), NVDA (options flow + semiconductor data), ^IXIC (sector rotation complexity)

### Avoid Until Further Notice
These assets destroy capital or sanity for solo rule-based quants:
- **TSLA** — sentiment-driven, unmodelable with standard features.
- **SOL/USD** — too volatile, too retail-driven, network-outage risk.
- **USD/IDR** — data scarcity, intervention opacity, no reliable API automation for Indonesians.
- **XRP/USD** — litigation-driven, non-stationary.
- **GOTO** — no earnings, narrative-only, retail casino.

---

## 10. Data Source Map

| Asset Type | Free Daily Data | Free Intraday Data | Macro/Alt Data | Indonesian Broker Access |
|-----------|----------------|-------------------|----------------|---------------------------|
| US Indices | yfinance (^GSPC, ^IXIC, ^DJI) | yfinance (1m, limited history) | FRED, CBOE VIX | Offshore brokers (XM, etc.) |
| US Stocks | yfinance, Alpha Vantage | yfinance (1m) | FRED, earnings calendars | Offshore brokers |
| Forex G10 | Dukascopy, FRED | Dukascopy (1m tick) | FRED, CFTC COT | XM, Exness, IC Markets, FBS |
| USD/IDR | BI reference rate | None free | BI, BPS | Local futures brokers (limited API) |
| Crypto | Yahoo, CoinGecko, Binance API | Binance/Bybit API (1m) | Glassnode (partial free), CryptoQuant | Indodax, Tokocrypto, Binance P2P |
| IDX Stocks | idx.co.id (historical), Yahoo (some) | None (daily only) | BI, BPS, BKPM | IPOT, Ajaib, Stockbit, Mirae |
| Gold/Oil | yfinance (GC=F, CL=F) | yfinance (1m) | EIA, FRED, World Gold Council | XM, Exness (as CFDs) |

---

## 11. Modeling Approach Quick Reference

| Asset | Best Model Type | Key Features | Typical Holding Period |
|-------|----------------|-------------|----------------------|
| ^GSPC | Macro regime / risk-parity | Rates, VIX, credit spreads, earnings yield | 1–4 weeks |
| ^DJI | Slow trend / value rotation | Macro, sector rotation, price-weight effects | 1–4 weeks |
| ^IXIC | Rates/growth regime | 10Y-2Y, SOX, XLK, tech earnings calendar | 3–10 days |
| AAPL | Event drift / seasonality | Post-earnings drift, iPhone cycle, China PMI | 3–10 days |
| MSFT | Fundamental momentum | Azure growth proxy, AI capex, enterprise sentiment | 3–10 days |
| NVDA | Volatility regime / event only | Options skew, TSMC sales, AI demand narrative | Hours to 2 days |
| TSLA | **Avoid** | Sentiment, gamma, social NLP | Minutes to hours |
| EUR/USD | Carry / macro regime | ECB-Fed spread, COT, macro calendar | 2–5 days |
| GBP/USD | Event-driven / GARCH | BOE/CPI surprises, UK fiscal events | 1–3 days |
| USD/JPY | Carry / regime switch | US-JGB spread, risk appetite, BOJ thresholds | 1–2 weeks |
| AUD/USD | China-commodity proxy | Iron ore, China PMI, RBA decision | 2–4 days |
| USD/CAD | Oil-beta | WTI returns, EIA inventory, BOC spread | 1–3 days |
| USD/CHF | **Avoid directional** | SNB intervention proxy | Daily only |
| NZD/USD | **Avoid standalone** | GDT dairy, RBNZ | Hours to 1 day |
| USD/IDR | **Avoid directional** | BI rate, intervention probability | Daily/weekly |
| BTC/USD | On-chain + macro | Exchange flows, MVRV, funding rate, halving | 3–7 days |
| ETH/USD | BTC-lead + on-chain | Gas, staking, DeFi TVL, ETH/BTC ratio | 2–5 days |
| SOL/USD | **Avoid** | Network health, retail sentiment | Hours |
| XRP/USD | **Avoid** | Legal docket, exchange listing rumors | Event-driven |
| BBCA | JCI momentum + foreign flow | JCI trend, BI rate, foreign ownership % | 1–3 weeks |
| BBRI | Policy cycle + microfinance | Government lending mandates, NPL trend | 1–3 weeks |
| BMRI | Commodity cycle proxy | Coal/CPO prices, corporate loan growth | 1–3 weeks |
| TLKM | Defensive / dividend | Subscriber growth, data center revenue, yield | 2–4 weeks |
| ASII | Multi-segment proxy | Auto sales, coal/CPO, banking subsidiary | 1–2 weeks |
| GOTO | **Avoid** | GMV narrative, cash runway | Days |
| BYAN | Commodity supercycle | Newcastle coal, DMO policy, USD/IDR | 3–10 days |
| XAU/USD | Real-rate regime | TIPS yield, DXY, VIX, geopolitical risk | 1–4 weeks |
| WTI/USD | Inventory surprise | EIA report, futures curve, OPEC+ calendar | 1–2 days |

---

*Last updated: June 2026. Validate all volatility figures and broker terms with live data before deploying capital.*
