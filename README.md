# Entropy & Equity — Quantitative Financial Modeling

A physics-meets-finance research project applying mathematical modeling techniques — Fourier analysis, Monte Carlo simulation, quantum-enhanced Monte Carlo, and Black-Scholes options pricing — to real commodity and equity markets.


---

## Overview

Financial markets exhibit behaviour that is partly deterministic (seasonal cycles, drift) and partly stochastic (volatility, random shocks).  This project explores that boundary using three complementary modeling approaches, each grounded in the mathematics used by professional quant researchers.

| Model | Core Idea | Best Applied To |
|---|---|---|
| Fourier Series | Decompose prices into periodic cycles + linear trend | Seasonal commodities (corn, soybeans) |
| Monte Carlo (GBM) | Simulate thousands of price paths via stochastic calculus | Any equity or commodity with known μ, σ |
| Quantum Monte Carlo | GBM paths driven by quantum-generated random numbers via Qiskit | Research / hardware-backed randomness |
| Black-Scholes / Greeks | Price options and estimate sensitivity (Δ, Γ, Θ) | Equity options |

---

## Repository Structure

```
entropy-and-equity/
├── src/
│   ├── Fourier_Modeling.ipynb       # Fourier series fitting and backtesting
│   ├── monte_carlo_simulation.ipynb # GBM Monte Carlo with Sharpe ratio
│   ├── quantum_monte_carlo.ipynb    # Quantum-enhanced GBM Monte Carlo via Qiskit
│   └── Options_Modeling.ipynb       # Black-Scholes pricing + Greeks
├── tools/
│   └── CSV_Handling.ipynb           # Download price data via yfinance
├── data/
│   ├── corn_price.csv
│   ├── soy_price.csv
│   ├── oil_price.csv
│   ├── gold_price.csv
│   ├── soyb_etf.csv
│   └── soybean_trading_data.csv
├── docs/
│   ├── fourier_modeling.md          # Full mathematical derivation
│   ├── monte_carlo_simulation.md    # GBM derivation and interpretation
│   ├── quantum_monte_carlo.md       # Quantum RNG approach and circuit design
│   └── options_modeling.md          # Black-Scholes PDE and Greeks
└── requirements.txt
```

---

## Notebooks

### `src/Fourier_Modeling.ipynb`

Fits a truncated Fourier series to commodity price time series using least-squares regression on a sinusoidal basis:

$$f(x) = a_0 + \sum_{n=1}^{N} \left[ a_n \cos(n\omega x) + b_n \sin(n\omega x) \right]$$

The `fit_fourier_with_trend` variant removes a linear trend first, which prevents the oscillatory terms from absorbing long-run drift.

**Backtesting** is performed on corn, soybeans, oil, and the SOYB ETF.  Each backtest reports four metrics:
- **MAE** — mean absolute error
- **RMSE** — root mean squared error (penalises large misses)
- **MAPE** — mean absolute percentage error
- **R²** — proportion of variance explained by the model

> **Note:** use `period = 252` (trading days per year), not 365.  Ideal harmonic count is 2–5; higher values overfit noise.

---

### `src/monte_carlo_simulation.ipynb`

Simulates equity price paths under **geometric Brownian motion** using the exact log-normal step:

$$S_{t+dt} = S_t \exp\!\left[\left(\mu - \tfrac{1}{2}\sigma^2\right)dt + \sigma\sqrt{dt}\,Z\right], \quad Z \sim \mathcal{N}(0,1)$$

This is the analytically exact discretisation of the GBM SDE, avoiding the first-order Euler-Maruyama bias present in the naive `S += dS` update.

The simulation reports:
- Empirical distribution of final prices vs. the theoretical log-normal PDF
- Probability of gain (closed-form and Monte Carlo estimate)
- Exact expected value via $S_0 e^{\mu T}$
- **Sharpe ratio** computed from the cross-sectional distribution of simulated total returns

---

### `src/quantum_monte_carlo.ipynb`

Replaces the classical pseudo-random normal samples in GBM with **quantum-generated randomness** sourced from a Qiskit circuit.

Each random draw is produced by:
1. Placing 100 qubits in equal superposition with Hadamard gates
2. Measuring to obtain a uniformly random bitstring
3. Counting the number of 1s and applying the CLT normalisation (binomial → standard normal):

$$Z = \frac{X - np}{\sqrt{np(1-p)}}, \qquad X \sim \text{Binomial}(n, 0.5)$$

4. Scaling to any target $(\mu, \sigma)$

The quantum samples are then substituted directly into the exact GBM step used in the classical simulator:

$$S_{t+dt} = S_t \exp\!\left[\left(\mu - \tfrac{1}{2}\sigma^2\right)dt + \sigma\sqrt{dt}\,Z_{\text{quantum}}\right]$$

Two backends are supported:
- **Simulator** (`AerSimulator`) — runs locally with no IBM account required
- **Real hardware** (commented out) — runs on the least-busy IBM quantum device via `QiskitRuntimeService`

> **Requirements:** `qiskit`, `qiskit-aer`, `qiskit-ibm-runtime`. For real hardware, IBM Quantum credentials must be saved via `QiskitRuntimeService.save_account(...)`.

---

### `src/Options_Modeling.ipynb`

Models option prices using two complementary methods:

1. **Greek approximation** — second-order Taylor expansion for short-horizon changes:

$$dV \approx \Theta \cdot dt + \Delta \cdot dS + \tfrac{1}{2}\Gamma (dS)^2$$

2. **Full Black-Scholes repricing** — recalculates the call price each day using the closed-form solution, which is exact under the GBM assumption and more accurate for longer holding periods.

---

## Data

CSV files follow a two-column format:

| Date (YYYY-MM-DD) | Adj Close (float) |
|---|---|

Use `tools/CSV_Handling.ipynb` to download fresh data for any ticker via `yfinance`.

---

## Getting Started

```bash
pip install -r requirements.txt
jupyter notebook
```

Open any notebook in `src/` and run all cells.  Data files are included in `data/` so no API calls are needed for the pre-loaded assets.

---

## Mathematical Background

Full derivations and explanations are in `docs/`:
- `docs/fourier_modeling.md` — Fourier series, least-squares matrix formulation
- `docs/monte_carlo_simulation.md` — GBM SDE, Euler-Maruyama, log-normal distribution
- `docs/quantum_monte_carlo.md` — quantum RNG circuit, CLT normalisation, hardware vs. simulator
- `docs/options_modeling.md` — Black-Scholes PDE, call price formula, Greeks

## Author Information

**Morik Aghayan**  
Physics Major w/ Computer Science Minor | Researcher & Qiskit Advocate

📧 morik.aghayan@gmail.com  
🌐 [GitHub: morik04](https://github.com/morik04)  
🔗 [LinkedIn](https://www.linkedin.com/in/morik-aghayan/)
