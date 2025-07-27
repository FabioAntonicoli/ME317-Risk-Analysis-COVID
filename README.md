# ME317 Project Report

## What Happened During the COVID-19 Pandemic?

**Author**: Fabio Antonicoli  
**Date**: July 27, 2025

---

## Introduction

Early 2020 saw one of the most volatile periods in recent financial history due to the COVID-19 outbreak. Global stock markets reacted with extreme uncertainty. This report analyzes ten diversified US stocks during and after the pandemic shock, using techniques from the ME317 course. We explore return dynamics, develop and backtest a Value-at-Risk (VaR) model, and analyze interdependencies between financial institutions using copulas.

---

## 1. Data Collection and Preparation

We selected 10 major US stocks across multiple sectors to ensure a diversified view:

- Financials: JPMorgan (JPM), Bank of America (BAC), Goldman Sachs (GS)  
- Tech/Industrial/Consumer: Apple (AAPL), Microsoft (MSFT), Boeing (BA), Walmart (WMT), Procter & Gamble (PG), ExxonMobil (XOM), Chevron (CVX)

Daily adjusted prices from **January 1, 2019, to December 31, 2022** were collected using the `tidyquant` package.

---

## 2. Market Dynamics During the Pandemic

A sharp decline in adjusted prices occurred between **February–March 2020**, coinciding with global lockdowns.

**Figure 1**: Adjusted stock prices (2019–2022)  
> _[Line plot of 10 stock prices over time]_

Daily **log-returns** show extreme spikes in volatility during early 2020. For instance, Boeing (BA) and JPMorgan (JPM) had single-day losses >10%.

**Figure 2**: Daily log-returns (2019–2022)  
> _[Line plot of daily log-returns for each stock]_

---

## 3. Sector Relationships and Diversification Effects

To evaluate co-movement, we plotted scatterplots between:

- **JPM vs BAC** (same sector): strong linear relationship  
- **JPM vs MSFT** (different sectors): lower correlation

**Figure 3**: Scatter plot — JPM vs BAC  
**Figure 4**: Scatter plot — JPM vs MSFT  

**Observation**: During crises, correlations across sectors increase, reducing the benefits of diversification.

---

## 4. Portfolio Construction and VaR Estimation

An **equally weighted portfolio** was created by investing \$1000 in each stock. Daily portfolio return:

\[
R_{t}^{\text{port}} = \frac{1}{N} \sum_{i=1}^{N} R_{i,t}
\]

Two 1-day VaR estimates at the 95% confidence level:

- **Empirical VaR** (historical simulation):  
  \[
  \text{VaR}_{\text{emp}} = \text{Quantile}_{0.05}(R_{t}^{\text{port}}) = -2.24\%
  \]
- **Parametric VaR** (assuming normal distribution):  
  \[
  \text{VaR}_{\text{norm}} = \mu - z_{\alpha} \cdot \sigma = \mu - 1.645 \cdot \sigma = -2.66\%
  \]

**Figure 5**: Daily log-return of the portfolio  
> _[Line plot of portfolio returns with volatility spikes in March 2020]_

---

## 5. VaR Backtesting on 2023

We used **2023 portfolio data** to evaluate out-of-sample performance. The number of days with returns below the empirical VaR was counted.

\[
I_t = \mathbb{1}(R_{t}^{\text{port}} < \text{VaR}_{\alpha})
\]

Only **1 violation** occurred out of 251 days (0.4% vs expected 5%), suggesting the model was conservative post-COVID.

---

## 6. Dependence Analysis with Copulas

We fitted **multivariate copulas** to pseudo-observations of returns from JPM, BAC, and GS.

**Table 1**: AIC values for different copulas

| Copula Model | AIC     |
|--------------|---------|
| Gaussian     | -3219   |
| Student-t    | **-3423** |
| Clayton      | -2499   |
| Gumbel       | -2993   |

**Observation**: The **Student-t copula** provides the best fit, capturing joint tail risk.

---

### 6.1 Additional Copula Analysis (JPM vs BAC)

Using the `QRM` and `tseries` packages:

- Pseudo-observations via empirical CDF
- Fitted copulas: Gaussian, Student-t, Gumbel, Clayton

**Table 2**: Log-likelihood values

| Copula Model | Log-Likelihood |
|--------------|----------------|
| Gaussian     | 928            |
| Student-t    | **990**        |
| Gumbel       | 952            |
| Clayton      | 752            |

**Spearman’s ρ**: 0.91  
**Kendall’s τ**: ≈ 0.925  

**Figure 6**: Pseudo-observations plot (JPM vs BAC)  
> _[Scatterplot of uniform pseudo-observations]_

---

## 7. Backtesting the Empirical VaR: Kupiec Test

To statistically assess the 1-day 95% VaR:

- Only **1 violation in 251 days**
- Kupiec Likelihood Ratio Test:

\[
\text{LR}_{\text{POF}} = -2 \cdot \ln \left( \frac{(1 - \hat{p})^{T - x} \hat{p}^x}{(1 - \alpha)^{T - x} \alpha^x} \right)
\]

Where:

- \( x = 1 \), \( T = 251 \), \( \hat{p} = x/T = 0.004 \)

Test result:

- \( \text{LR}_{\text{POF}} = 11.83 \)
- p-value = 0.0006

**Conclusion**: The null hypothesis of correct coverage is **rejected** — the model **overestimates risk**.

---

## Conclusion

- The COVID-19 crisis created **extreme volatility**, **surging correlations**, and **systemic risk**, especially in financials.
- **Diversification helped** during normal times, but **failed in crises**.
- **Empirical VaR** produced realistic forecasts but **overestimated** risk in 2023.
- The **Student-t copula** effectively modeled **joint tail dependence**, especially for financial stocks.

---
