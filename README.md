# Gravitational-Wave Signal Analysis — GW190521

**MSc Astrophysics — Cardiff University, 2024**  
Module: Module: Gravitational Wave Astrophysics (PXT903)  
Signal processing and parameter estimation of a real LIGO gravitational-wave event.

---

## Overview

This project performs end-to-end analysis of GW190521, one of the most unusual gravitational-wave events ever detected — a short-duration (~0.1s) signal from the merger of two intermediate-mass black holes with a combined mass of ~142 M☉, producing the first confirmed intermediate-mass black hole (IMBH). The analysis covers signal conditioning, noise modelling, waveform matching, SNR computation, and Bayesian parameter estimation.

---

## GW190521 — Event Background

- **Detected:** 21 May 2019 by LIGO-Livingston, LIGO-Hanford, and Virgo
- **Primary mass:** ~85 M☉, **Secondary mass:** ~66 M☉
- **Remnant:** ~142 M☉ intermediate-mass black hole (IMBH)
- **Distance:** ~5.3 Gpc (redshift z ≈ 0.82)
- **Network SNR:** 14.7, **FAR:** 1 in 4900 years
- **Signal frequency range:** 30–80 Hz
- Published results: [Abbott et al. (2020), PRL 125, 101102](https://doi.org/10.1103/PhysRevLett.125.101102)

---

## Analysis Pipeline

**1. Data Acquisition and Conditioning**
- Downloaded 1024s of strain data from LIGO Open Science Center (GWOSC) for all three detectors (L1, H1, V1)
- Resampled to 2048 Hz (Nyquist frequency appropriate for 30–80 Hz signal band)
- Computed Q-transform to visually identify the signal location in time-frequency space
- Computed Power Spectral Density (PSD) using Welch's method (FFT length 4s, Tukey window, 50% overlap)
- Whitened data to flatten the noise floor across frequencies
- Bandpassed (30–120 Hz) to isolate the signal band and remove irrelevant noise

**2. Signal Modelling**
- **CBC model:** Generated inspiral-merger-ringdown waveform using PyCBC's `SEOBNRv4_opt` approximant with parameters from the published paper (m₁=154 M☉, m₂=120 M☉ redshifted, distance=5300 Mpc, inclination=0.3 rad)
- **Burst (phenomenological) model:** Generated a Gaussian pulse using `scipy.signal.gausspulse` (fc=55 Hz, bw=0.3) as a model-agnostic waveform template
- Performed by-eye overlay of both models against conditioned data to estimate initial parameters

**3. Overlap and Matched Filtering**
- Computed cross-correlation between CBC and burst models using `scipy.signal.correlate`
- Projected waveforms onto each detector using antenna pattern functions (`fp`, `fc`) accounting for right ascension, declination, polarisation, and time delays between detectors

**4. Parameter Estimation**
- Defined log-likelihood in the frequency domain using the noise-weighted inner product
- Defined log-prior with physical bounds on frequency, bandwidth, amplitude, and sky position
- Combined into log-posterior and minimised using `scipy.optimize.minimize` (Powell method) to find best-fit parameters
- Signal subtraction and Q-transform comparison used to verify the fit

---

## Results

Parameters recovered from the burst model fit:

| Parameter | This analysis | Published (Abbott et al.) |
|-----------|--------------|--------------------------|
| Primary mass (redshifted) | 156.8 M☉ | 154 M☉ |
| Secondary mass (redshifted) | 99.0 M☉ | 120 M☉ |
| Distance | 4981 Mpc | ~5300 Mpc |
| Inclination | 0.55 rad | ~0.5 rad |
| SNR (L1) | 26 | 14.7 (network) |
| SNR (H1) | 24 | — |
| SNR (V1) | 4 | — |

Results are broadly consistent with published values. Discrepancies in mass and SNR are expected given the simplified burst model and limited data duration used — the published analysis used multiple waveform approximants (including `NRSur7dq4`) and full Bayesian inference with MCMC sampling.

---

## Tools and Software

| Tool | Purpose |
|------|---------|
| Python | Analysis and visualisation |
| GWpy | Data download, whitening, Q-transform, PSD |
| PyCBC | Waveform generation, matched filtering, detector projection |
| SciPy | Gaussian pulse model, cross-correlation, optimisation |
| NumPy / Matplotlib | Numerical computation and plotting |
| GWOSC | Open gravitational-wave data access |

---

## Data Access

All data used in this project is publicly available via GWOSC:

- [GW190521 event page](https://gwosc.org/eventapi/html/GWTC-2/GW190521/v2/)
- [GWOSC open data](https://gwosc.org/)

---

## Key References

- Abbott et al. (2020) — GW190521 detection paper, *Physical Review Letters* 125, 101102
- PyCBC software: Nitz et al. (2024)
- GWpy: Macleod et al.

---

## Development Notes

This analysis was completed as part of the Gravitational Wave Astrophysics module (PXT903) at Cardiff University. The Jupyter notebook structure and several code sections were provided by the module lecturer as a framework; the analysis, parameter estimation, signal interpretation, and written commentary were completed independently.

Most packages used (`GWpy`, `PyCBC`) are open-source software released by the LIGO Scientific Collaboration.
