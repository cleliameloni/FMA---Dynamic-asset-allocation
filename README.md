# Dynamic Asset Allocation

Project for the *Financial Markets Analytics* course (Variant B), M.Sc. in Data Science, University of Milano-Bicocca. Exam session: 22/09/2026.

**Author:** Clelia Meloni

## Research question
In the difficult years after 2020 (the 2020 pandemic crash and the 2022 energy shock linked to the war in Ukraine), which portfolio optimization approach gives the most robust risk-adjusted allocation: classical Markowitz, Michaud resampling or Black-Litterman? And how far do they beat the naive 1/N and 60/40 benchmarks?

## Data
- **Universe:** 19 multi-asset ETFs, split into equities, government bonds, cash proxy (BIL), credit and real assets
- **Prices:** adjusted closes from Yahoo Finance (`yfinance`), converted to monthly log returns, 2008–2026 (224 months)
- **Risk-free rate:** 3-month US T-Bill (FRED series `TB3MS`)

## Methodology
- Rolling monthly **out-of-sample backtest** starting in 2013, with a **60-month estimation window**
- **Markowitz:** maximum Sharpe and minimum variance portfolios
- **Michaud resampling:** 100 Monte Carlo resamples at each rebalancing date
- **Black-Litterman:** market-equilibrium prior, with market weights rebuilt month by month from prices, plus a **defensive view triggered by volatility** (3-month volatility compared with 60-month volatility)
- **Benchmarks:** equally weighted (1/N) and 60/40 (equities and US government bonds)
- **Evaluation:** CAGR, volatility, Sharpe ratio, maximum drawdown, crisis windows (2020, 2022), turnover and performance net of transaction costs (10 bps per side)

## Key results (net of 10 bps/side costs)
| Strategy | CAGR % | Vol % | Sharpe | Max DD % | Turnover %/mo |
|---|---|---|---|---|---|
| Markowitz Max-Sharpe | 8.06 | 8.50 | 0.75 | -15.37 | 9.48 |
| Markowitz Min-Variance | 1.68 | 0.58 | -0.19 | -0.26 | 0.73 |
| Michaud | 6.21 | 7.50 | 0.60 | -15.49 | 11.80 |
| Black-Litterman (dynamic prior) | 7.73 | 9.05 | 0.67 | -17.91 | 7.28 |
| 1/N | 5.91 | 9.00 | 0.49 | -15.46 | 1.56 |
| 60/40 | 6.12 | 9.37 | 0.49 | -21.80 | 1.42 |

- Over the whole sample, all three optimized models beat both benchmarks on Sharpe ratio. Min-Variance is the exception: it ends up the same as the cash proxy.
- **No absolute winner across crises.** Max-Sharpe and Michaud were protected in 2022 mostly by accident, because after 2020 they were concentrated in cash (BIL). That same position kept them out of the market in 2020–2021.
- **Black-Litterman** behaves the most consistently: it stays invested and has the lowest turnover of the three models. Its protection is only partial in the slow 2022 decline.
- **Look-ahead bias:** a static Black-Litterman prior built on current market capitalizations reached a Sharpe ratio of 0.86. When the market weights are rebuilt month by month, it falls to 0.69.
- The **60/40** portfolio is the worst in 2022, when equities and bonds fell together.

## Limitations
- The crisis conclusions rest on only two episodes (2020 and 2022)
- The cash proxy (BIL) is included among the optimized assets, which distorts Max-Sharpe, Michaud and Min-Variance
- Only the sample covariance estimator is used, and there are no weight caps
- Differences in Sharpe ratio are not tested for statistical significance


## How to run
```bash
pip install yfinance pandas numpy matplotlib
jupyter notebook FMA_B_Meloni_895982.ipynb
```
Data is downloaded at runtime from Yahoo Finance and FRED, so an internet connection is required. Results may differ slightly from the report because Yahoo Finance updates its price history.

## License
Released under the MIT License. See `LICENSE`.
