# Monte Carlo simulation of stock prices

This code uses a Monte Carlo simulation to model possible future stock prices under the assumption that the stock follows **geometric Brownian motion**.

## Why this is relevant

Stock-price dynamics are modeled as:

$$
dS = \mu S\dt + \sigma S\dW,
$$

where:

- $S$ is the stock price
- $\mu$ is the expected return
- $\sigma$ is the volatility
- $dt$ is a small time step
- $dW$ is the Brownian motion term

In Monte Carlo simulation, the random Brownian term is modeled as

$$
dW = \sqrt{dt}\,Z, \qquad Z \sim \mathcal{N}(0,1).
$$

Substituting this into the stock-price equation gives

$$
dS \approx \mu S\,dt + \sigma S\,\sqrt{dt}\,Z.
$$

This is exactly the equation used in the code.

---

## What the code is doing

The code simulates many possible one-year price paths for a stock, starting from an initial value:

- `shots = 10000` means it generates 10,000 simulated price paths
- `time = 252` means each path has 252 time steps, representing 252 trading days
- `dt = 1/252` means each step is one trading day as a fraction of a year
- `s0 = 100` is the starting stock price
- `mu = 0.08` is the assumed annual expected return
- `sigma = 0.25` is the assumed annual volatility

For each simulated path, the stock price is updated each day using

$$
ds = \mu s\,dt + \sigma s\,z\sqrt{dt},
$$

where $z$ is a newly sampled standard normal random variable:

$$
z \sim \mathcal{N}(0,1).
$$

Then the code updates the stock price by

$$
s \leftarrow s + ds.
$$

This is the discrete-time Euler approximation of the continuous-time stochastic differential equation.

---

## Interpretation of the price update

Each simulated daily price change has two parts:

### Drift term

$$
\mu s\,dt
$$

This is the deterministic part, representing the expected growth of the stock over a small time interval.

### Random shock term

$$
\sigma s\z\sqrt{dt}
$$

This is the stochastic part, representing daily randomness driven by Brownian motion.

Together they produce

$$
ds = \mu s\,dt + \sigma s\,z\sqrt{dt},
$$

which is the simulated daily change in stock price.

---

## Why many simulations are needed

A single simulated path gives only one possible future outcome. Since stock prices are uncertain, the code repeats the simulation many times to generate a distribution of possible ending prices.

That is why the code stores each path's final value in `final_prices`.

After all simulations are complete, it estimates the probability that the stock finishes above its initial price of 100:

$$
\text{Chance of gain} =
\frac{\text{number of simulations with final price } > 100}{\text{total number of simulations}}.
$$

So the output

```python
print(f"Chance of gain is {100*count/shots}")
