# Black-Scholes Equation and Options Modeling

## Black-Scholes Equation

The Black-Scholes equation is a partial differential equation used to model the price of options over time, and it is given by:

$$\frac{\partial V}{\partial t} + \frac{1}{2} \sigma^2 S^2 \frac{\partial^2 V}{\partial S^2} + rS \frac{\partial V}{\partial S} - rV = 0$$

Where:
* $V$ is the price of the option
* $S$ is the current price of the underlying asset
* $r$ is the risk-free interest rate
* $\sigma$ is the volatility of the underlying asset
* $t$ is time

## Options Pricing Models

Several models are used for option pricing including:

### European Options

The price of a European call option can be calculated using the Black-Scholes formula:

$$C = S_0 N(d_1) - Xe^{-rT} N(d_2)$$

Where:

$$d_1 = \frac{\ln(S_0/X) + (r + \sigma^2/2)T}{\sigma\sqrt{T}}$$

$$d_2 = d_1 - \sigma\sqrt{T}$$

And $N(d)$ is the cumulative distribution function of the standard normal distribution, $C$ is the call option price, $S_0$ is the current stock price, $X$ is the strike price, and $T$ is the time to expiration.

### American Options

American options can be exercised at any time before expiration, which makes their pricing more complex. In practice, numerical methods such as binomial trees or Monte Carlo simulations are used to estimate their values.
