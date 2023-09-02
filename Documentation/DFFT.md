# DFFT Manual

**Original Author:** Carrick L. Talmadge, Ph.D.

## Overview

DFFT, which stands for “Double-precision Fast Fourier Transform”, is a general
purpose command line tool developed by Carrick Talmadge. This software both
implements and generalizes the Welch periodogram (Welch 1967). [The Welch
periodogram](https://www.osti.gov/servlets/purl/5688766 "PSD Computations
Using Welch’s Method") is a method for
computing the power spectral density (PSD) of a
signal.

Welch's method, developed by Peter D. Welch, is a technique used for
estimating spectral density. It is used to estimate the power-levels a signal
has across various
frequencies. Essentially, it converts a signal from time to frequency domain and
uses what's called periodogram spectrum estimates.

Welch's method manages to reduce the
amount of noise in the estimated power spectra while trading off some frequency
resolution. This noise reduction can be important when the available data is
imperfect or doesn't go on indefinitely.

## Theory

### A. Discrete Fourier Transform

We note that the FFT refers a class of algorithms for efficiently ( $O(n \log(n))$ )
computing the discrete Fourier transform (DFT). The DFT converts an
equally-spaced real-valued time series $x_n$ of points into an equivalent
equally-spaced frequency–domain series of points $X_m$. The underlying tool is the
Fourier amplitude, which is computed for a frequency $f$ from the time series $x_n$:

(1)

$$
y(t_n) = a e^{-2πift_n} + a^* e^{2}πiftn, n=0,1,…N-1
$$

(2)

$$
E(a, a^\ast) = \sum_{n=0}^{N-1} W_n \left(ae^{-2πift_n} + a^\ast e^{2πift_n}-x_n \right)^2,
$$

where $E(a, a^\ast)$ is the optimization function for weighted least-squares fit. We note that $y(t_n)$ is a real-valued
function.
Here $W_n$ is referred to as a “window function”, but we see also has an interpretation as the weighting function for
$E(a, a^\ast)$. Minimizing $E(a, a^\ast)$ gives:

(3)

$$
\frac{∂E}{∂a^\ast} = 2 \sum_{n=0}^{N-1} W_n e^{-2ift_n} \left(ae^{-2ift_n} + a^\ast e^{2ift_n} - x_n \right) = 0,
$$

(4)

$$
a = \frac{1}{S_w} \sum_{n=0}^{N-1} W_n e^{-2πift_n}x_n,
$$

(5)

$$
S_w = \sum_{n=0}^{N-1} W_n.
$$

We recover the DFT by writing $t_n=nΔt$, $T=N Δt$, $Δf=\frac{1}{T}$, $f_m=mΔf$, so

(6)

$$
2πif_m t_n = 2πi \left(\frac{m}{NΔt} \right) \left(nΔt \right) = 2πimn/N,
$$

and

(7)

$$
X_m = \frac{1}{S_w} \sum_{n=0}^{N-1} W_n e^{2πimn/N} x_n.
$$

We note that the coefficients $X_m$ are periodic with period $N$. Only the frequencies in the range $-\frac{f_s}{2}\leq
f \leq \frac{f_2}{2}$ can be represented without aliasing.
Without loss of generality, we can identify:

(8)

$$
\\{f_m\\} = \\{0, Δf, 2Δf, …, \left(\frac{N}{2} -1 \right)Δf, \frac{NΔf}{2}, \left(-\frac{N}{2} -1 \right)Δf, …, -2Δf,
-Δf \\}.
$$

Note there are exactly $N$ values of $X_m$ and $F_m$. Also, it can be shown that

(9)

$$
\hat{X}^\ast \left(-f_m \right) = \hat{X} \left(f_m \right),
$$

(10)

$$
\hat{X} \left(-f_m \right) = \hat{X}_{N-m}.
$$

## B. Welch Periodogram

The Welch periodogram (Welch, 1967) divides a data record in to equal length segments, which have a tapered window
applied to them (Figure 1). Typically each window is half-overlapping (this preserves the original statistical power of
the data series, as will be discussed below). The DFTs for each of these windows are computed, with the periodogram
being given by the mean-square value of the individual DFTs. This process reduces the noise associated with an
individual DFT, at the price of a reduction in frequency resolution.

It can be advantageous to use a higher overlap factor, such as when a noisy data rejection criterion is being used (
Talmadge, 1993).

The overlap factor is set using the nOverlap variable in DFFT. Note that this factor refers to the fraction of a window
to step between consecutive windows. The variable “nover” can be used for a more fine-grained control of the amount of
overlap.
