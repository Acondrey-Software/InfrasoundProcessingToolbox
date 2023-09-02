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

### Discrete Fourier Transform

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
    E(a, a^{*}) = \sum_{n=0}^{N-1} W_n(ae^{-2πift_n} + a^{*}e^{2πift_n}-x_n)^2
$$