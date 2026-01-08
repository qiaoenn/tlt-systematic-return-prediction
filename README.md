# Systematic Return Prediction for US Treasury ETF (TLT) 

A time-series case study that builds a **weekly return prediction model** for the **iShares 20+ Year Treasury Bond ETF (TLT)** using a mix of **macroeconomic**, **market sentiment**, and **technical/behavioural** features. Predictions are then converted into a simple **long/flat trading strategy**, and evaluated out-of-sample.

> Educational / research project (not financial advice).

---

## What this notebook does

### 1) Data collection
- **TLT daily prices** from `yfinance`, transformed into **weekly (W-FRI)**.
- **Macroeconomic variables** from **FRED** via `pandas_datareader` (also transformed to weekly).
- **SPY weekly returns** as a market risk-on/off proxy.

### 2) Target (Outcome) construction
- Computes **weekly TLT returns**.
- Constructs **next-week return** as the prediction target:  
  **`Return Next Wk` = Return shifted by -1**

### 3) Feature engineering
**Macro features (weekly, transformed):**
- `CPI_yoy` (YoY inflation, %)
- `INDPRO_yoy` (YoY industrial production, %)
- `SLOPE_10Y2Y` (10Y–2Y yield curve slope)
- `DGS10_chg` (weekly change in 10Y yield)
- `DGS2` (2Y yield level)
- `BEI_10Y_chg` (weekly change in 10Y breakeven inflation)
- `VIX_chg` (weekly change in VIX)

**Market feature:**
- `SPY_ret` (weekly SPY return)

**TLT technical/behavioural:**
- `mom_12w` (12-week momentum)
- `vol_12w` (12-week rolling volatility)

**Important:** Predictors are **lagged by 1 week** (`shift(1)`) to avoid look-ahead bias.

### 4) Modelling (time-series train/test split)
Models implemented:
- **Baseline:** OLS Linear Regression
- **Main:** Ridge Regression (regularised linear model)
- **Extension:** LassoCV for **model-based variable selection**

### 5) Trading strategy construction
- Convert predicted next-week return into a signal:
  - **Long** if prediction > 0 (or > threshold in the robustness extension)
  - **Flat** otherwise
- Compute out-of-sample strategy returns and compare against **Buy & Hold**.

### 6) Performance evaluation
Reports common metrics (weekly → annualised):
- Annualised return
- Annualised volatility
- Sharpe ratio
- Max drawdown
- Number of observations

---

## Example results (from the notebook output)

Out-of-sample performance table (weekly, annualised):

| Strategy | Ann Return | Ann Vol | Sharpe | Max Drawdown | Obs |
|---|---:|---:|---:|---:|---:|
| Buy & Hold | -0.033 | 0.155 | -0.214 | -0.466 | 289 |
| OLS Long/Flat | 0.017 | 0.127 | 0.137 | -0.301 | 289 |
| Ridge Long/Flat | -0.004 | 0.128 | -0.031 | -0.301 | 289 |

Robustness extension (thresholded trading) is also included in the notebook.

> Results will change depending on data updates, sample period, and parameters.

---
