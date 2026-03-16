# Quantum Monte Carlo Simulation

This notebook extends the classical GBM Monte Carlo by sourcing its random numbers from a **quantum circuit** rather than a classical pseudo-random number generator.  The financial model is identical to `monte_carlo_simulation.ipynb`; only the random number generation step changes.

---

## Motivation

Classical Monte Carlo relies on pseudo-random number generators (PRNGs), which are deterministic algorithms that approximate randomness.  A quantum circuit in superposition produces genuinely indeterminate measurement outcomes — each shot is physically random, not algorithmically approximated.  This makes quantum-generated samples a natural fit for stochastic simulation.

---

## Quantum Random Number Generation

### Circuit construction

A circuit of $n = 100$ qubits is prepared:

1. All qubits start in $|0\rangle$
2. A Hadamard gate is applied to each qubit, placing the register in an equal superposition of all $2^n$ basis states:

$$H|0\rangle = \frac{|0\rangle + |1\rangle}{\sqrt{2}}$$

3. All qubits are measured simultaneously.  Each qubit independently collapses to 0 or 1 with equal probability $p = 0.5$.

The result is a uniformly random bitstring of length 100.

### Converting to a standard normal sample

Let $X$ = number of 1s in the bitstring.  Since each qubit is an independent Bernoulli(0.5) trial:

$$X \sim \text{Binomial}(n,\, p = 0.5)$$

By the Central Limit Theorem, for large $n$:

$$Z = \frac{X - np}{\sqrt{np(1-p)}} \xrightarrow{d} \mathcal{N}(0,1)$$

With $n = 100$, $np = 50$, and $\sqrt{np(1-p)} = 5$, giving:

$$Z = \frac{X - 50}{5}$$

To sample from an arbitrary $\mathcal{N}(\mu, \sigma^2)$, the result is scaled:

$$\tilde{Z} = \sigma Z + \mu$$

---

## GBM Price Path

The quantum sample $Z_{\text{quantum}}$ is dropped directly into the exact log-normal GBM step:

$$S_{t+dt} = S_t \exp\!\left[\left(\mu - \tfrac{1}{2}\sigma^2\right)dt + \sigma\sqrt{dt}\,Z_{\text{quantum}}\right]$$

This is the same discretisation used in the classical notebook — analytically exact for GBM, with no Euler-Maruyama bias.

---

## Simulation Parameters

| Parameter | Value | Meaning |
|---|---|---|
| `s0` | 100 | Initial stock price |
| `mu` | 0.34 | Expected annual return |
| `sigma` | 0.18 | Annual volatility |
| `T` | 1 | Horizon in years |
| `time` | 252 | Trading days per year |
| `dt` | 1/252 | One trading day as a fraction of a year |
| `shots` | 100 | Number of simulated price paths |
| `n_qubits` | 100 | Qubits per quantum sample |

Each of the 100 price paths requires 252 quantum samples (one per trading day), so 25,200 quantum circuit executions are run in total.

---

## Backends

### Simulator (`AerSimulator`)

```python
simulator = AerSimulator()
result = simulator.run(qc, shots=1, memory=True).result()
```

Runs entirely locally.  No IBM account needed.  Used by default because real-hardware execution is slow at the scale required for a full simulation.

### Real Quantum Hardware (commented out)

```python
service = QiskitRuntimeService()
backend = service.least_busy(operational=True, simulator=False)
sampler = Sampler(mode=backend)
```

Selects the least-busy IBM quantum device.  Requires IBM Quantum credentials saved via:

```python
QiskitRuntimeService.save_account(channel="ibm_quantum", token="YOUR_TOKEN")
```

The circuit is transpiled with `optimization_level=3` before submission.  Results are retrieved via `SamplerV2`.

> **Note:** Running 25,200 hardware shots is impractical for routine use.  The real-hardware path is provided for demonstration and can be used with a smaller `shots` or `time` value.

---

## Output

The notebook plots all 100 simulated price paths and reports:

- **Chance of gain** — fraction of paths ending above `s0`
- **Empirical Mean** — average final price across simulations
- **Empirical Std** — standard deviation of final prices

These can be compared directly with the classical Monte Carlo results to assess whether quantum-generated randomness produces materially different distributional properties at small sample sizes.

---

## Requirements

```
qiskit
qiskit-aer
qiskit-ibm-runtime
numpy
matplotlib
scipy
```

Install with:

```bash
pip install qiskit qiskit-aer qiskit-ibm-runtime numpy matplotlib scipy
```
