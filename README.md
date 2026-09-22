# Markowitz Portfolio Optimization

A reproducible empirical study of **Modern Portfolio Theory (MPT)** across a diversified nine-ETF universe. The project implements, optimizes, and backtests three portfolio-allocation strategies, then evaluates how they perform when the market regime changes.

> **Main finding:** optimization produced strong risk-adjusted results in the 2005-2020 training period, but bond-heavy optimized allocations performed poorly out of sample during the 2021-2025 interest-rate regime shift. The work highlights the gap between in-sample optimization and real-world robustness.

## Research question

If an investor had optimized a portfolio at the end of 2020 using all available historical data, would it have outperformed simpler allocation rules over the following four years?

The analysis tests this question against the Federal Reserve's 2022-2023 tightening cycle, a structural break that challenged the historical relationship between bonds and equities.

## Portfolio universe

Daily adjusted closing prices are sourced from Yahoo Finance for January 2005 to January 2025.

| Ticker | Asset | Asset class |
| --- | --- | --- |
| SPY | S&P 500 Index | US large-cap equity |
| VGK | Vanguard FTSE Europe | European equity |
| FEZ | SPDR EURO STOXX 50 | Eurozone equity |
| VWO | Vanguard FTSE Emerging Markets | Emerging-market equity |
| AGG | iShares Core US Aggregate Bond | Investment-grade bonds |
| IEF | iShares 7-10 Year Treasury Bond | US Treasury bonds |
| GLD | SPDR Gold Shares | Gold |
| VNQ | Vanguard Real Estate Index | US REITs |
| IYR | iShares US Real Estate | US REITs |

The risk-free rate uses the dynamic 13-week US Treasury Bill yield (`^IRX`).

## Methodology

The dataset is split into two non-overlapping periods:

- **Training / in-sample:** January 2005 to December 2020
- **Testing / out-of-sample:** January 2021 to January 2025

Target weights are optimized only on training data, then applied unchanged to the test period. Backtests rebalance quarterly (every 63 trading days).

### Strategies

- **Maximum Sharpe Ratio:** finds the tangency portfolio by minimizing the negative Sharpe ratio.
- **Minimum Volatility:** minimizes portfolio standard deviation.
- **Risk Parity:** equalizes asset-level contributions to total portfolio risk.
- **Benchmarks:** equal weight (1/N) and SPY buy-and-hold.

Constrained optimizations enforce weight bounds of **2% to 30%** per asset to avoid extreme concentration.

## In-sample results: 2005-2020

| Strategy | Annual return | Volatility | Sharpe ratio | Main exposures |
| --- | ---: | ---: | ---: | --- |
| Maximum Sharpe | 6.43% | 5.03% | 1.04 | IEF 40%, AGG 31.9%, SPY 18.6% |
| Minimum Volatility | 5.90% | 4.63% | 1.01 | IEF 40%, AGG 40% |
| Risk Parity | 6.40% | 6.30% | 0.82 | AGG 36.2%, IEF 31.4% |
| Equal Weight | 8.82% | 16.24% | 0.47 | All assets 11.1% |
| SPY only | 11.02% | 19.62% | 0.50 | SPY 100% |

The optimized portfolios substantially reduced volatility and achieved the highest in-sample Sharpe ratios. This was driven largely by a 60-80% combined allocation to bonds, which had unusually favorable risk-adjusted performance during the declining-rate training period.

## Out-of-sample results: 2021-2025

| Strategy | Total return | Annual return | Volatility | Sharpe ratio | Max drawdown |
| --- | ---: | ---: | ---: | ---: | ---: |
| Maximum Sharpe | 4.08% | 1.01% | 7.16% | -0.28 | -17.79% |
| Minimum Volatility | -0.18% | -0.04% | 6.96% | -0.44 | -17.88% |
| Risk Parity | 1.38% | 0.34% | 7.44% | -0.36 | -19.41% |
| Equal Weight | 19.31% | 4.53% | 11.68% | 0.13 | -24.56% |
| SPY only | 68.27% | 13.95% | 16.48% | 0.66 | -24.50% |

The optimized strategies reversed sharply out of sample. SPY returned 13.95% annualized, compared with 1.01% for Maximum Sharpe. The result is not a failure of the optimization routine: it reflects a failure of the historical regime to persist.

## Interpretation

The 2005-2020 sample rewarded bond exposure through declining interest rates, low volatility, and diversification benefits. Between 2021 and 2025, aggressive monetary tightening reversed that environment. The historically optimal 60-80% bond allocations became a material drag on performance.

The project also illustrates **estimation error amplification**: mean-variance optimizers treat uncertain historical estimates as precise inputs and can produce fragile allocations. Simpler benchmarks avoided this form of overfitting.

## Practical takeaways

1. **Out-of-sample testing is essential.** Strong in-sample metrics do not establish investability.
2. **Economic judgment complements optimization.** Algorithms do not detect structural regime shifts on their own.
3. **Simplicity can be robust.** Equal weight and SPY buy-and-hold outperformed all optimized strategies in the test period.
4. **Diversify risk factors, not only assets.** Concentration in interest-rate duration can dominate portfolio outcomes.

## Technical implementation

- Python 3.8+
- NumPy 1.21+, Pandas 1.3+, SciPy 1.7+
- Matplotlib 3.4+, yfinance 0.1.70+
- SLSQP optimization
- 10,000 Monte Carlo portfolios
- 252 trading days per year

The notebook includes data preparation, portfolio optimization, efficient-frontier analysis, Monte Carlo simulation, and backtesting.

## Repository contents

- `progetto_markowitz_2.ipynb` - analysis, optimization, visualizations, and backtests
- `Report.pdf` - accompanying report

## Disclaimer

This project is for educational and research purposes only. It is not investment advice. Past performance and historical backtests do not guarantee future results.
