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

## `calc_opt_price_change`

This function uses a second-order Taylor approximation to estimate the change in an option’s value:

$$
dV \approx \Theta \cdot \text{t} + \Delta \cdot dS + \frac{1}{2}\Gamma (dS)^2
$$

where:

- $dV$ is the estimated change in option price
- $\Theta$ measures time decay per day
- $\Delta$ measures sensitivity to changes in the underlying asset price
- $\Gamma$ measures how delta changes as the underlying price moves
- $dS$ is the change in the underlying asset price
- t is the number of days elapsed

Keep in mind this is a very rough estimate. Time decay ($\Theta$) tends to increase as the option nears its expiration date. 
