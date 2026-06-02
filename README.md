# Bitcoin Time Series Analysis: Stationarity, Volatility Clustering, and the ADF Test

This project examines the statistical properties of Bitcoin's price and return series. The focus is on three things that matter a lot in practice: whether the series is stationary (which determines what models you can apply to it), whether returns are truly independent (they are not), and how to formally confirm both with hypothesis testing.

Understanding these properties is a prerequisite for any serious time series modeling. Fitting an ARIMA, GARCH, or any volatility model to a non-stationary series without first checking will produce unreliable results. This project goes through the diagnostic steps you would do before modeling.

## Repository Structure

```
├── FinancialDataAnalysis2.ipynb             # Main analysis notebook
└── data/
    └── bitcoin_data.csv                    # Bitcoin adjusted close prices
```

## Analysis

### 1. Price Series vs. Log Returns: A Study in Stationarity

The Bitcoin price series and its log returns are plotted side by side to build intuition before running any tests.

The price series is clearly non-stationary. It trends upward by orders of magnitude, with both the mean and variance shifting dramatically over time. There is no constant level the series reverts to.

The log return series looks very different. It fluctuates around zero with no visible long-term drift, which suggests stationarity in the mean. But it is not independent. Large swings tend to cluster together, with calm periods followed by turbulent ones and vice versa. This is volatility clustering, and it means the conditional variance is changing over time even if the unconditional mean is not.

### 2. ACF Analysis: Where the Dependence Actually Lives

The autocorrelation function (ACF) is computed up to 50 lags for three transformations of the return series.

**Log returns:** ACF is near zero at all lags beyond zero. Returns themselves show almost no linear dependence, which is consistent with weak-form market efficiency. You cannot reliably predict tomorrow's return from yesterday's.

**Absolute returns:** ACF decays slowly and stays significantly positive across many lags. Large moves, regardless of direction, are followed by more large moves.

**Squared returns:** Same pattern as absolute returns. Persistent positive autocorrelation across all lags, which is the classic signature of ARCH effects.

The takeaway is that the dependence in Bitcoin is not in the direction of returns but in their magnitude. Returns are unpredictable; volatility is not. This is why GARCH-type models were developed, and this kind of ACF analysis is exactly the diagnostic that motivates their use.

### 3. Augmented Dickey-Fuller Test: Formal Stationarity Test

The ADF test is applied to both series to confirm what the plots suggest.

The null hypothesis is that the series has a unit root (non-stationary). The alternative is that it is stationary. We reject the null when the p-value falls below 0.05.

| Series | ADF Statistic | p-value | Result |
|---|---|---|---|
| Price | -0.3379 | 0.9197 | Non-stationary |
| Log Returns | -34.9058 | ~0.0000 | Stationary |

The price series fails the test by a wide margin, p = 0.92, giving no grounds to reject the unit root hypothesis. The log return series passes decisively, ADF statistic of -34.91 is far below every critical value and the p-value is essentially zero.

This confirms the standard financial result: asset prices are integrated processes (I(1)), and taking log differences produces a stationary series. For modeling purposes, you should always work with returns, not prices.

## Key Takeaways

- Bitcoin prices are non-stationary. Fitting any time series model directly to price levels is statistically invalid.
- Log returns are stationary in mean, confirmed by both visual inspection and the ADF test.
- Returns show almost no linear autocorrelation, consistent with weak-form market efficiency.
- Absolute and squared returns have strong, persistent autocorrelation across 50+ lags. This is volatility clustering and motivates GARCH-family models.
- The ADF test is a necessary diagnostic step before any time series modeling, not an optional one.

## Dependencies

```bash
pip install numpy pandas matplotlib statsmodels
```

## Usage

Rename `bitcoin_data(1).csv` to `bitcoin_data.csv`, then run:

```bash
jupyter notebook FinancialDataAnalysis2.ipynb
```
