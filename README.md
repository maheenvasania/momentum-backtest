# Cross-Sectional Momentum on the S&P 500

A backtest of a 12-1 cross-sectional momentum strategy on the S&P 500 universe, implemented in Python. The book goes long the top-quintile and short the bottom-quintile of trailing 12-month returns (skipping the most recent month), equal-weighted and rebalanced monthly. The notebook builds the strategy, benchmarks it against the market, stress-tests it under costs and regimes, and refines it via a rolling beta hedge and volatility scaling.

## Table of Contents
1. [Project Overview](#project-overview)
2. [Key Features](#key-features)
3. [Setup Instructions](#setup-instructions)
4. [Dependencies](#dependencies)
5. [Results](#results)
6. [Future Work](#future-work)

## Project Overview
The strategy is built and evaluated on 2011-2026 monthly data. The workflow moves from a raw equal-weighted long-short book to a series of refinements, with each step motivated by a specific issue in the diagnostics. The notebook emphasizes methodological care: all inputs are lagged to avoid lookahead, Heteroskedasticity and Autocorrelation Consistent (HAC) standard errors are used, and statistical significance is tested via a permutation test on the cross-sectional signal.

Core components:
- **Signal construction**: 12-1 cross-sectional momentum, top/bottom quintile long-short
- **Benchmarking**: market regression, cost sensitivity, permutation testing
- **Regime analysis**: bull/bear and high/low volatility splits
- **Refinements**: volatility-managed momentum, rolling beta hedge, inverse-vol weighting
- **Robustness**: lookback-window sensitivity, sub-period stability

## Key Features
- **Rolling market-beta hedge**: Overlays a monthly index hedge sized to a 36-month rolling beta (lagged), neutralizing market exposure without distorting the signal.
- **Volatility-managed momentum**: Scales exposure by inverse trailing volatility, keeping total risk constant for a like-for-like comparison.
- **Cost sensitivity**: Sweeps per-side transaction costs to identify the break-even threshold.
- **Regime diagnostics**: Decomposes performance across market direction and volatility regimes to distinguish signal effects from market exposure effects.
- **Reproducibility**: All external price pulls are cached to parquet so results are deterministic across reruns.

## Setup Instructions
1. Clone the repository:
   ```bash
   git clone https://github.com/maheenvasania/momentum-backtest.git
   cd momentum-backtest
   ```
2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
3. Launch the notebook:
   ```bash
   jupyter notebook momentum_backtest.ipynb
   ```

## Dependencies
- `numpy`
- `pandas`
- `matplotlib`
- `statsmodels`
- `yfinance` (initial price pulls; results cached to parquet after)
- `pyarrow` (parquet I/O)

Install all with:
```bash
pip install -r requirements.txt
```

## Results
Key findings across the 2011–2026 sample:
- **Raw strategy**: Gross Sharpe 0.29, market-neutral Sharpe 0.47, persistent negative beta (−0.19), max drawdown −32.9%.
- **Rolling beta hedge**: Gross Sharpe rises to 0.48, residual beta essentially zero.
- **Volatility scaling**: Cuts max drawdown from −32.9% to −22.5%; most of the gross Sharpe gain reflects reduced market exposure, not a better signal.
- **Cost sensitivity**: Raw net Sharpe breaks even at ~36 bps/side; market-neutral edge survives to ~57 bps/side.
- **Robustness**: The 12-month lookback is not a lucky peak - market-neutral Sharpe is roughly flat across 10–12 month windows.

## Future Work
- Decompose returns by volatility bucket to test whether the momentum premium concentrates in high-vol names. 
- Point-in-time universe construction to eliminate survivorship bias. 
- Test alternative momentum constructions (residual momentum, industry-neutral momentum) to reduce sector and market-beta concentration. 
- Combine the signal with other factors (value, quality, low-vol) in a multi-factor book.
- Holding-period sensitivity: test how quickly the signal decays post-formation and whether less frequent rebalancing sacrifices much edge.
