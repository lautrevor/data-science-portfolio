# Project 2: Do Technical Indicators Add Predictive Value? (VFV)

## Overview

This project examines whether simple, commonly used return-based technical indicators provide meaningful predictive value for next-day returns of VFV (Vanguard S&P 500 Index ETF). Rather than attempting to construct a trading strategy, the focus is on evaluating signal versus noise using a disciplined, out-of-sample modeling framework.

The analysis emphasizes proper target construction, time-aware train/test splits, and honest performance evaluation to assess whether historical price-derived features contain reliable short-horizon predictive information.

---

## Research Question

Do commonly used historical return-based indicators improve out-of-sample prediction of next-day returns relative to naive baseline models?

---

## Data

* **Asset:** VFV.TO (Vanguard S&P 500 Index ETF)
* **Source:** Yahoo Finance (via `yfinance`)
* **Frequency:** Daily adjusted closing prices
* **Time Range:** January 2013 – present

Adjusted close prices are used to ensure that returns account for dividends and corporate actions.

---

## Target Variable

* **Next-day return**, defined as the percentage change in adjusted closing price from day *t* to day *t+1*

The target is constructed by shifting the daily return series backward by one day, ensuring that all predictive features are based solely on information available at time *t* and preventing look-ahead bias.

---

## Features

All features are derived from historical returns only and are intentionally kept simple to avoid feature bloat.

### Lagged Returns

* 1-day lagged return
* 5-day lagged return
* 10-day lagged return

### Rolling Statistics

* 5-day rolling mean of returns
* 20-day rolling mean of returns
* 5-day rolling standard deviation (volatility proxy)
* 20-day rolling standard deviation (volatility proxy)

These features represent commonly cited price-based indicators that are frequently assumed to contain short-term predictive signal.

---

## Methodology

* Feature engineering uses only information available at time *t*
* Time-based train/test split (no random shuffling)
* Baseline model: predicts the mean of training-period returns
* Linear regression used for interpretability and transparency
* Performance evaluated using out-of-sample RMSE and MAE

This setup mirrors a realistic forecasting scenario and avoids data leakage.

---

## Results

The linear regression model does **not** demonstrate a meaningful improvement over the baseline model. While the RMSE is numerically slightly lower than the baseline, the difference is negligible relative to the scale and inherent volatility of daily returns. In practical terms, the change is indistinguishable from noise.

The MAE of the linear regression model is slightly worse than the baseline, further reinforcing the conclusion that the engineered historical return features do not add reliable predictive value for next-day returns.

---

## Key Findings

* Daily VFV returns are highly noisy and centered near zero
* Simple lagged returns and rolling statistics fail to outperform a naive mean-based predictor
* Linear regression predictions shrink toward the mean and fail to capture extreme return movements
* Apparent patterns in historical price data do not translate into actionable short-term predictive power when evaluated rigorously out of sample

These results support the signal-versus-noise framing of the project.

---

## Limitations

* Daily equity returns exhibit extremely low signal-to-noise ratios
* Linear models cannot capture complex nonlinear dynamics
* Small statistical improvements may not translate into economically meaningful gains
* The analysis is limited to a single ETF and asset class

---

## Next Steps

* Evaluate regularized linear models (Ridge, Lasso) to test coefficient stability
* Explore alternative targets such as return direction instead of magnitude
* Test longer prediction horizons where signal may be stronger
* Extend the analysis to additional ETFs or asset classes

