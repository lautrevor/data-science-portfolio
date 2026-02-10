# Project 2: Do Technical Indicators Add Predictive Value? (VFV)

## Overview
This project evaluates whether commonly used technical indicators provide meaningful predictive value for next-day returns of VFV (Vanguard S&P 500 Index ETF), compared to simple baseline models. The goal is not to build a trading strategy, but to test whether widely believed signals offer real out-of-sample information.

## Research Question
Do common technical indicators improve out-of-sample prediction of next-day returns relative to naive return-based baselines?

## Data
- Asset: VFV.TO (Vanguard S&P 500 Index ETF)
- Source: Yahoo Finance
- Frequency: Daily adjusted close prices
- Time range: 2013–present

## Target Variable
- Next-day return, defined as the percentage change in adjusted close price from day *t* to day *t+1*

## Baseline Features
- Previous-day return
- Rolling mean of past returns

These features represent simple, information-light baselines that any model should outperform to be considered useful.

## Technical Indicators
- Simple moving averages (short and medium windows)
- Momentum
- Rolling volatility
- (Optional) Relative Strength Index (RSI)

Indicators are intentionally limited to avoid feature bloat and overfitting.

## Methodology
- Feature engineering performed using only information available at time *t*
- Time-based train/test split (no random shuffling)
- Linear regression models used for interpretability
- Performance evaluated using out-of-sample error metrics (RMSE)

## Results
(To be completed)

## Key Findings
(To be completed)

## Limitations
- Daily returns are highly noisy and difficult to predict
- Linear models may not capture nonlinear market dynamics
- Small improvements in error may not translate to tradable advantages

## Next Steps
- Test regularized models (Ridge/Lasso)
- Evaluate stability across different time periods
- Extend analysis to additional ETFs or assets
