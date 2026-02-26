# Fourier Modeling
## `fit_fourier_series`

This function fits a truncated Fourier series to sampled data.

Inputs are: days (int format); prices; number of harmonics; period (optional)

## Logic

Given data points

$$
(x_1, y_1), (x_2, y_2), \dots, (x_m, y_m),
$$

the function approximates the signal with a finite Fourier series of the form

$$
f(x) = a_0 + \sum_{n=1}^{N} \left[ a_n \cos(n \omega x) + b_n \sin(n \omega x) \right],
$$

where:

- $N$ is the number of harmonics (`num_harmonics`)
- $T$ is the period
- $\omega = \dfrac{2\pi}{T}$ is the fundamental angular frequency

This is the real-valued form of a Fourier series, written as a sum of cosine and sine terms.

---

## Period and angular frequency

If no period is provided, the function uses

$$
T = \max(x_{\text{data}}) - \min(x_{\text{data}}).
$$

It then computes

$$
\omega = \frac{2\pi}{T}.
$$

This makes each basis function periodic with period $T$.

---

## Fourier basis functions

The approximation is built from the following basis functions:

$$
1,\quad \cos(\omega x),\quad \sin(\omega x),\quad \cos(2\omega x),\quad \sin(2\omega x),\quad \dots,\quad \cos(N\omega x),\quad \sin(N\omega x).
$$

So for each harmonic $n = 1, 2, \dots, N$, the model includes:

- a cosine term $a_n \cos(n\omega x)$
- a sine term $b_n \sin(n\omega x)$

The constant term $a_0$ is the DC offset, or average level of the signal.

---

## Matrix form

For data values $x_1, x_2, \dots, x_m$, the function constructs a design matrix $A$ whose rows are

$$
\begin{bmatrix}
1 &
\cos(\omega x_i) &
\sin(\omega x_i) &
\cos(2\omega x_i) &
\sin(2\omega x_i) &
\cdots &
\cos(N\omega x_i) &
\sin(N\omega x_i)
\end{bmatrix}.
$$

So the full system can be written as

$$
A \mathbf{c} \approx \mathbf{y},
$$

where

$$
\mathbf{c} =
\begin{bmatrix}
a_0 \\
a_1 \\
b_1 \\
a_2 \\
b_2 \\
\vdots \\
a_N \\
b_N
\end{bmatrix},
\qquad
\mathbf{y} =
\begin{bmatrix}
y_1 \\
y_2 \\
\vdots \\
y_m
\end{bmatrix}.
$$

The matrix $A$ has size $m \times (2N+1)$.

---

## Least-squares fit

In general, there is no exact solution to

$$
A\mathbf{c} = \mathbf{y},
$$

so the function solves the least-squares problem

$$
\min_{\mathbf{c}} \|A\mathbf{c} - \mathbf{y}\|^2.
$$

Equivalently, it minimizes the total squared error

$$
\sum_{i=1}^{m} \left( f(x_i) - y_i \right)^2.
$$

This produces the coefficient vector $\mathbf{c}$ that best fits the data in the least-squares sense.

---

## Reconstructing the fitted curve

Once the coefficients are found, the fitted values are computed by

$$
\mathbf{y}_{\text{fit}} = A \mathbf{c}.
$$

---

## Notes
- Keep in mind Fourier series is built of sine and cosine functions, so it works best with assets have seasonal behavior (soybeans, corn)
- The `fit_fourier_with_trend` does the same thing but add a first degree polynomial, not good for extrapolating as it will trend to infinity or negative infinity
- There are 252 trading days in a year, not 365. Keep this in mind if you hardset the period
- Ideal range for harmonics is 2-5, overfitting will cause it to pick up random "noise"





