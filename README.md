# VaR Calculation and Backtesting

Portfolio risk analysis project measuring Value at Risk (VaR) using three industry-standard methods — Historical, Parametric, and Monte Carlo — with model validation performed through backtesting against actual portfolio losses.

## Overview

Value at Risk (VaR) estimates the maximum expected loss on a portfolio over a given time horizon at a given confidence level. This project builds and compares three VaR methodologies on a diversified five-asset portfolio, then backtests each model to evaluate how well its predicted risk matches realized outcomes — a step required in real-world risk management (e.g., under Basel backtesting frameworks) but often skipped in beginner projects.

## Portfolio

- **Assets:** SPY (US equities), BND (US bonds), QQQ (tech-heavy equities), VTI (total US market), GLD (gold)
- **Weights:** Equal-weighted (20% each)
- **Portfolio value:** $1,000,000
- **Data period:** ~15 years of daily adjusted close prices (via `yfinance`)
- **Holding period:** 5 trading days
- **Confidence levels:** 90%, 95%, 99%

## Methods

**1. Historical VaR**
Uses the empirical distribution of past 5-day rolling portfolio returns, taking the relevant percentile directly — no distributional assumptions required.

**2. Parametric VaR (Variance-Covariance Method)**
Assumes portfolio returns are normally distributed. Uses the portfolio's covariance matrix to estimate volatility, then applies the inverse normal distribution (`norm.ppf`) to calculate VaR at each confidence level.

**3. Monte Carlo VaR**
Simulates 10,000 random return scenarios based on the portfolio's expected return and standard deviation, then estimates VaR from the simulated loss distribution.

**4. Backtesting**
Rolls a 500-day estimation window forward through the full 15-year history, recalculating VaR at each step and comparing it against the actual realized 5-day loss. Tracks how often each model's VaR was breached and compares that breach rate to the theoretical expected rate (5% at the 95% confidence level).

## Key Findings

| Method | 95% VaR (5-day) | Backtested Breach Rate |
|---|---|---|
| Historical | ~$23,574 | 6.04% |
| Parametric | ~$29,314 | 3.31% |
| Monte Carlo | ~$24,597 | 5.15% |

*Expected breach rate at 95% confidence: 5%*

- **Monte Carlo** produced the breach rate closest to theoretical expectations.
- **Historical VaR** slightly under-predicted risk (breach rate above 5%), consistent with its sensitivity to the specific historical window used.
- **Parametric VaR** was the most conservative, meaningfully overestimating risk (breach rate well below 5%). This is a well-documented limitation of the normal-distribution assumption — real asset returns typically exhibit fatter tails and skew than a normal distribution captures, so a model calibrated only to standard deviation tends to overstate typical risk while potentially still understating true tail risk in extreme scenarios.

## Limitations & Next Steps

- Monte Carlo simulation currently models the portfolio return as a single normal variable rather than simulating each asset individually via the covariance matrix (e.g., using Cholesky decomposition) — a more rigorous approach for portfolios with less linear correlation structures.
- All methods rely on assumptions of stationarity and i.i.d. returns, which don't fully hold during volatility regime shifts (e.g., 2020, 2022).
- Portfolio weights are static; a production model would need rebalancing logic.
- Planned extensions: Expected Shortfall (CVaR), which is increasingly preferred over VaR under Basel III/FRTB for capturing tail risk beyond the VaR threshold; rolling VaR visualization over time; and a Student's-t or Cornish-Fisher based parametric model to address fat tails.

## Skills Demonstrated

Market risk measurement, portfolio analysis, Python-based quantitative modeling (NumPy, Pandas, SciPy), statistical distribution assumptions and their limitations, and model validation via backtesting.
