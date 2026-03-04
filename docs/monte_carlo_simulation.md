# Monte Carlo Simulation

This code uses a Monte Carlo simulation to model possible future stock prices under the assumption that the stock follows **geometric Brownian motion**.

## Why this is relevant

The change in stock price is modeled as:

$$
dS = \mu S\ dt + \sigma S\ dW 
$$

where:

- $S$ is the stock price
- $\mu$ is the expected return
- $\sigma$ is the volatility
- $dt$ is a small time step
- $dW$ is the Brownian motion term

In Monte Carlo simulation, the random Brownian term is modeled as

$$
dW = \sqrt{dt}\ Z, \qquad Z \sim \mathcal{N}(0,1)
$$

Substituting this into the stock-price equation gives

$$
dS \approx \mu S\ dt + \sigma S\ \sqrt{dt}\ Z 
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
ds = \mu s\ dt + \sigma s\ z\sqrt{dt} 
$$

where $z$ is a newly sampled standard normal random variable:

$$
z \sim \mathcal{N}(0,1)
$$

Then the code updates the stock price by

$$
s \leftarrow s + ds
$$

This is the discrete-time Euler approximation of the continuous-time stochastic differential equation.

---

## Interpretation of the price update

Each simulated daily price change has two parts:

### Drift term

$$
\mu s\ dt
$$

This is the deterministic part, representing the expected growth of the stock over a small time interval.

### Random shock term

$$
\sigma s\ z\sqrt{dt}
$$

This is the stochastic part, representing daily randomness driven by Brownian motion.

Together they produce

$$
ds = \mu s\ dt + \sigma s\ z\sqrt{dt},
$$

which is the simulated daily change in stock price.

---

## Why many simulations are needed

A single simulated path gives only one possible future outcome. Since stock prices are uncertain, the code repeats the simulation many times to generate a distribution of possible ending prices.

That is why the code stores each path's final value in `final_prices`.

After all simulations are complete, it estimates the probability that the stock finishes above its initial price of 100:

$$
\text{Chance of gain} =
\frac{\text{number of simulations with final price } > \text{initial price}}{\text{total number of simulations}}
$$

---

## Exact GBM discretisation

The code uses the **exact log-normal step** rather than the first-order Euler-Maruyama approximation:

$$
S_{t+dt} = S_t \exp\!\left[\left(\mu - \tfrac{1}{2}\sigma^2\right)dt + \sigma\sqrt{dt}\,Z\right]
$$

This is the analytically exact solution to the GBM SDE for a single step, so it eliminates the $O(dt)$ discretisation bias that accumulates in the Euler scheme.

---

## Sharpe ratio

After generating all simulated paths the code computes the **Sharpe ratio** from the cross-sectional distribution of annualised returns:

$$
\text{Sharpe} = \frac{\bar{r}_{\text{annualised}} - r_f}{\sigma_{r_{\text{annualised}}}}
$$

where $r_f$ is the risk-free rate (set to 4 % by default as a demonstration; adjust to the current 10-year Treasury yield). A Sharpe ratio above 1.0 is generally considered good; above 2.0 is excellent, though the appropriate threshold depends on strategy and time horizon.

---

## Exact formulas (closed form)

Instead of always running the full simulation, the expected value and chance of gain can be calculated analytically:

$$
\mathbb{E}[S_T] = S_0 e^{\mu T}
$$

$$
P(S_T > S_0) = \Phi\!\left(\frac{\left(\mu - \frac{\sigma^2}{2}\right)\sqrt{T}}{\sigma}\right)
$$

where $\Phi$ is the standard normal CDF. These match the simulation results to within sampling noise.

