# TAIEX-Gamma-Momentum

### Predicting TAIEX Intraday Momentum via Market Maker Gamma Hedging: A Comparison of Black-Scholes and Risk-Neutral Density (RND) Models

## 📌 Abstract

This project implements a high-frequency quantitative strategy that predicts intraday momentum in the TAIEX (Taiwan Stock Index) futures market. By estimating the **Net Gamma Exposure (NGE)** of option market makers, the model identifies periods of involuntary hedging demand. We contrast the classical **Black-Scholes (BS)** framework with a **Mixture of Lognormal (MLN)** approach to recover the true **Risk-Neutral Density (RND)**, providing superior accuracy in predicting price acceleration during negative Gamma regimes.

## 🛠 Framework & Methodology

### 1. Net Gamma Exposure (NGE) Estimation

The core signal is derived from the aggregated Gamma exposure of market makers. We utilize **raw tick data (Code 9)** from the Taiwan Futures Exchange (TAIFEX) to reconstruct precise inventory levels.

$$NGE_{t} = \left( \sum_{i=1}^{N} \Gamma_{i,t} \cdot OI_{i,t} \cdot M \cdot F_{t} \right) \cdot \frac{F_{t}}{100 \cdot \text{VOL}_{t-1}}$$

Where:

* **$\Gamma_{i,t}$**: Option Gamma estimated via BS or MLN.
* **$OI_{i,t}$**: Open Interest reconstructed from market maker transaction logs.
* **$F_t$**: TAIEX Futures price.
* **$\text{VOL}_{t-1}$**: 21-day rolling average volume.

### 2. Risk-Neutral Density (RND) via MLN

To overcome the limitations of constant volatility in the BS model, we implement the **Mixture of Lognormal (MLN)** distribution (Bondarenko, 2003) to fit the volatility smile:


$$f_Q(s) = \sum_{j=1}^{m} \pi_j \cdot Lognormal(s; \mu_j, \sigma_j)$$


This allows for the recovery of non-parametric Gamma ($\Gamma^{\text{MLN}}$) that accounts for fat tails and skewness in the TAIEX market.

### 3. Intraday Momentum Model

The trading day is partitioned into **30-minute intervals**. The strategy exploits the predictive power of lagged NGE on future returns:


$$r_{t} = \beta_0 + \beta_1 r_{t-1} + \beta_2 NGE_{t-1} + \beta_3 (1_{NGE_{t-1}<0} \cdot NGE_{t-1} \cdot r_{t-1}) + \epsilon_t$$

## 📊 Key Features

* **Tick-Level Precision:** Processing of TAIFEX "Code 9" data to distinguish market maker actions from retail noise.
* **Robust Statistics:** All regression outputs use **Newey-West** standard errors to account for heteroskedasticity and autocorrelation.
* **Comparison Engine:** Side-by-side backtesting of BS-based vs. RND-based hedging signals.

## 📂 Data Sources

* **TAIFEX Intraday Files:** Futures/Options quote and trade logs (2019 - 2025).
* **TAIBOR:** Taipei Interbank Offered Rate for risk-free rate interpolation.
* **Taiwan VIX:** Used for initial volatility surface calibration.

## 🚀 Getting Started

```bash
# Clone the repository
git clone https://github.com/yourusername/TAIEX-Gamma-Momentum.git

# Install dependencies
pip install numpy pandas scipy numba

```

## 📖 References

* **Baltussen et al. (2021):** Hedging demand and market intraday momentum. *Journal of Financial Economics*.
* **Bondarenko (2003):** Estimation of risk-neutral densities using positive convolution approximation. *Journal of Econometrics*.
* **Breeden & Litzenberger (1978):** Prices of state-contingent claims implicit in option prices. *Journal of Business*.

## ⚖️ Disclaimer

This project is for academic and research purposes only. Trading futures and options involves significant risk. Past performance is not indicative of future results.
