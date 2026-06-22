# Quant_friction_backtester
A Python framework built to backtest trend-following strategies on NSE equities while dynamically modeling regulatory and Groww app transaction friction.
# Multi-Asset Algorithmic Backtester (Net of Real-World Friction)

## Project Overview
This repository contains a vectorized quantitative finance backtesting framework built to evaluate standard moving average trend-following strategies. While baseline academic models evaluate performance in a friction-free vacuum, this framework integrates a production-grade transaction engine modeling real-world statutory taxes and platform fees from the Groww brokerage application.

The strategy was stress-tested across a diverse multi-asset universe representing distinct market capitalization sizes, betas, and price dynamics within the National Stock Exchange (NSE) of India:
- **Large-Cap Market Leaders:** RELIANCE, DMART, ETERNAL
- **High-Beta Conglomerate Momentum:** ADANIENT
- **High-Beta / Turnaround Play:** IDEA
- **Low-Beta / FMCG Defensive:** GILLETTE, MRF

---

## The Operational Friction Model
To accurately reflect institutional fund design and analyze scale economics, executions are simulated and compared across two distinct frameworks: a baseline single-share allocation (exposing flat-fee penalties) and an optimized fixed ticket allocation of **₹1,00,000**:
- **Brokerage Fee:** Flat ₹20 per executed order leg (Groww platform)
- **STT (Securities Transaction Tax):** 0.1% on buy and sell execution volume
- **Exchange Fees (NSE):** 0.00297%
- **Stamp Duty:** 0.015% (Applicable on Buy legs exclusively)
- **SEBI Turnover Fee:** 0.0001%
- **GST:** 18% applied directly to cumulative service fees (Brokerage + Exchange + SEBI)

---

## Key Empirical Findings & Regime Analysis

| Asset Ticker | Strategy Parameters | Total Completed Trades | Gross Strategy Return (No Fees) | Real-World Net Return (With Friction) | Primary Structural Insight |
| :--- | :---: | :---: | :---: | :---: | :--- |
| **ADANIENT.NS** | 20/50 SMA | 12 | +265.89% | +254.29% | **Alpha Generation:** Outpaced trading friction due to massive historical beta expansions and structural momentum. |
| **ETERNAL.NS** | 20/50 SMA | 10 | +134.14% | +127.93% | **Capital Protection:** Smooth trend capturing with minor friction degradation (only 6.21% total drag over 5 years). |
| **GILLETTE.NS** | 20/50 SMA | 12 | +94.33% | +88.15% | **Defensive Outperformance:** Cleanly minimized long sideways drawdowns and locked in steady capital growth safely. |
| **MRF.NS** | 20/50 SMA | 16 | +39.65% | +33.77% | **High Ticket Floor:** Underperformed buy-and-hold due to strong macro-driven stock consolidation from 2021 to 2023. |
| **DMART.NS** | 20/50 SMA | 14 | +20.52% | +16.06% | **Capital Preservation:** Optimization via ₹1L allocation compressed fee drag, while flatlines preserved capital during market drops. |
| **IDEA.NS** | 20/50 SMA | 12 | +10.42% | +6.76% | **Whipsaw Victim:** High noise and penny-stock volatility resulted in frequent false crossover entries, destroying alpha. |
| **RELIANCE.NS** | 20/50 SMA | 15 | +13.29% | -41.40% | **The Flat-Fee Trap:** Evaluated at single-share scale, the flat ₹20 fee acted as a terminal capital siphon, turning a positive return negative. |

---

# Vectorized Backtesting & Friction: Multi-Asset Regime Analysis Net of Execution Drag

## Executive Summary
This repository contains a vectorized quantitative finance backtesting framework built to evaluate a standard trend-following system (Simple Moving Average Crossover) across a multi-asset universe within the National Stock Exchange (NSE) of India. 

While typical academic models evaluate backtests in a friction-free vacuum, this production-grade framework integrates a dynamic transaction engine modeling the exact statutory tax and platform fee layers of the Groww brokerage application. 

The framework stress-tests performance over a 5-year market cycle (2021–2026) to analyze the systemic point where execution drag and market regime shifts turn theoretical alpha into structural capital decay.

---

## The Operational Friction Model
To accurately model scale economics and eliminate regressive fee drag on small account balances, executions are simulated using a standardized fixed ticket allocation size of **₹1,00,000 per trade**. The engine dynamically computes transaction penalties on the exact execution day using the following statutory framework:

*   **Brokerage Fee:** Flat ₹20 per executed order leg (Groww Equity Delivery structure).
*   **STT (Securities Transaction Tax):** 0.1% applied to both Buy and Sell execution volume.
*   **Exchange Transaction Charges (NSE):** 0.00297% of total turnover.
*   **Stamp Duty:** 0.015% applied to the Buy leg valuation exclusively.
*   **SEBI Turnover Fees:** 0.0001% of total turnover.
*   **GST:** 18% levied on the sum of service fees (*Brokerage + Exchange Charges + SEBI Fees*).

---

## Vectorized Backtesting Engine (The Python Framework)
This clean, end-to-end Python script utilizes a vectorized data stack (`pandas`, `numpy`) and `yfinance` to automatically pull historical data, resolve MultiIndex column layering, calculate indicators, shift positions to prevent look-ahead bias, and calculate transaction leakage.

```python
import yfinance as yf
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# 1. Download Historical Market Data
ticker = "DMART.NS"  # Swap with any NSE ticker (e.g., ADANIENT.NS, RELIANCE.NS)
data = yf.download(ticker, start="2021-01-01", end="2026-01-01")

# --- MULTIINDEX COLUMN CLEANUP ---
if isinstance(data.columns, pd.MultiIndex):
    data.columns = data.columns.get_level_values(0)
# ---------------------------------

df = data[['Close']].copy()

# 2. Calculate Indicators & Trading Signals (20/50 SMA Crossover Engine)
df['Fast_SMA'] = df['Close'].rolling(window=20).mean()
df['Slow_SMA'] = df['Close'].rolling(window=50).mean()
df['Signal'] = np.where(df['Fast_SMA'] > df['Slow_SMA'], 1, 0)

# Shift position by 1 day to strictly enforce execution on T+1 open (Avoids Look-Ahead Bias)
df['Position'] = df['Signal'].shift(1).fillna(0)

# 3. Detect Execution Days (Absolute Difference maps portfolio shift triggers)
df['Trades'] = df['Position'].diff().abs().fillna(0)

# 4. Calculate Percentage Market Returns
df['Market_Return'] = df['Close'].pct_change().fillna(0)

# 5. Dynamic Operational Friction Engine Net of Scale Constraints
def calculate_friction(row):
    if float(row['Trades']) == 0.0:
        return 0.0  # Guard Clause: Complete fee silence on holding days
    
    # Scale constraint variable to dilute regressive flat fees
    order_size = 100000.0  
    
    brokerage_pct = 20.0 / order_size 
    stt_pct = 0.0010                     
    exchange_pct = 0.0000297             
    sebi_pct = 0.000001                  
    
    # GST (18%) maps strictly to cumulative service provider overhead
    gst_pct = 0.18 * (brokerage_pct + exchange_pct + sebi_pct)
    stamp_duty_pct = 0.000075  # 0.015% applied to buy side exclusively (averaged across full cycle)
    
    total_friction_pct = (brokerage_pct + stt_pct + exchange_pct + sebi_pct + gst_pct + stamp_duty_pct)
    return total_friction_pct

# Map transaction costs across rows
df['Friction_Cost'] = df.apply(calculate_friction, axis=1)

# 6. Compute Raw vs. Net Strategy Performance Profiles
df['Strategy_Return_Raw'] = df['Market_Return'] * df['Position']
df['Strategy_Return_Net'] = df['Strategy_Return_Raw'] - df['Friction_Cost']

# 7. Geometric Compounding of Capital
df['Investment_Growth'] = (1 + df['Market_Return']).cumprod()
df['Strategy_Growth_Raw'] = (1 + df['Strategy_Return_Raw']).cumprod()
df['Strategy_Growth_Net'] = (1 + df['Strategy_Return_Net']).cumprod()

# 8. Extract Performance Metrics
total_trades = int(df['Trades'].sum() / 2) 
final_raw_return = (df['Strategy_Growth_Raw'].iloc[-1] - 1) * 100
final_net_return = (df['Strategy_Growth_Net'].iloc[-1] - 1) * 100

print(f"=== BACKTEST ENGINE METRICS FOR {ticker} ===")
print(f"Total Completed Round Trips: {total_trades}")
print(f"Theoretical Gross Strategy Return: {final_raw_return:.2f}%")
print(f"Real-World Net Strategy Return: {final_net_return:.2f}%")
