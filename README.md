# Quant_friction_backtester
A Python framework built to backtest trend-following strategies on NSE equities while dynamically modeling regulatory and Groww app transaction friction.
# 📊 Multi-Asset Algorithmic Backtester (Net of Real-World Friction)

## 📌 Project Overview
This repository contains a vectorized quantitative finance backtesting framework built to evaluate standard moving average trend-following strategies. While baseline academic models evaluate performance in a friction-free vacuum, this framework integrates a production-grade transaction engine modeling real-world statutory taxes and platform fees from the Groww brokerage application.

The strategy was stress-tested across a diverse multi-asset universe representing distinct market capitalization sizes, betas, and price dynamics within the National Stock Exchange (NSE) of India:
- **Large-Cap Market Leaders:** RELIANCE, DMART, ETERNAL
- **High-Beta Conglomerate Momentum:** ADANIENT
- **High-Beta / Turnaround Play:** IDEA
- **Low-Beta / FMCG Defensive:** GILLETTE, MRF

---

## ⚙️ The Operational Friction Model
To accurately reflect institutional fund design and analyze scale economics, executions are simulated and compared across two distinct frameworks: a baseline single-share allocation (exposing flat-fee penalties) and an optimized fixed ticket allocation of **₹1,00,000**:
- **Brokerage Fee:** Flat ₹20 per executed order leg (Groww platform)
- **STT (Securities Transaction Tax):** 0.1% on buy and sell execution volume
- **Exchange Fees (NSE):** 0.00297%
- **Stamp Duty:** 0.015% (Applicable on Buy legs exclusively)
- **SEBI Turnover Fee:** 0.0001%
- **GST:** 18% applied directly to cumulative service fees (Brokerage + Exchange + SEBI)

---

## 🔍 Key Empirical Findings & Regime Analysis

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

## 🚀 Core Quantitative Conclusions
1. **The Capital Scale Imperative:** Testing proved that without scaling order ticket inputs (e.g., to ₹1,00,000), flat-fee execution layers ($₹20$ per trade) capture an unviable percentage of low-unit allocations. 
2. **Regime Compatibility:** Trend-following mechanics yield immense alpha in structural momentum profiles (Adani) and excellent drawdown mitigation in correcting markets (DMART/Eternal), but suffer extreme decay in range-bound environments (Reliance/Vodafone Idea).
