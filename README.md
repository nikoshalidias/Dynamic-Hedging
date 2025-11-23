# Dynamic Trading and Path Dependent Options – Numerical Experiments

This repository contains the Python / Colab code used for the numerical
experiments in the paper

> **N. Halidias** – *Dynamic Trading and Path Dependent Options*.

The code implements the discrete–time, sigmoidal rule–based dynamic
hedging framework from the paper and runs Monte Carlo experiments for a
path–dependent payoff (arithmetic Asian call).  
It compares several families of sigmoidal functions (tanh, logistic,
normal CDF / \(N(\cdot)\)) under a mean–CVaR performance criterion.

---

## 1. What this code does

The main components are:

- **Underlying model**  
  Simulation of a geometric Brownian motion (GBM) on a discrete grid  
  \(0 = t_0 < t_1 < \dots < t_N = T\).

- **Path dependent payoff**  
  Arithmetic Asian call
  \[
    A_N = \frac{1}{N}\sum_{k=1}^{N} S_{t_k}, \qquad
    f(S_{t_1},\dots,S_{t_N}) = (A_N - K)^+.
  \]

- **Dynamic hedging strategy**
  - Portfolio with **underlying + bank account** (no additional options in the
    numerical part).
  - Self–financing wealth updates as in the paper.
  - Stock position \(a_k\) updated by sigmoidal rules of the form
    \[
      a_{k+1} = \max\{-L,\ \min(\tilde a_{k+1}, A^{k+1})\},
    \]
    where \(\tilde a_{k+1}\) depends on “move” and “level” signals built from
    sigmoidal functions.

- **Sigmoidal families under test**
  - Hyperbolic tangent: \(\tanh(\cdot)\).
  - Logistic function: \(\sigma(x) = (1 + e^{-x})^{-1}\).
  - Normal CDF: \(N(x)\) (standard Gaussian CDF, as in Black–Scholes).

- **Objective and risk measures**
  - Terminal hedging error
    \[
      \Pi_\theta(S_T) = a_{N-1}^\theta S_T + b_{N-1}^\theta - f(\text{path}),
    \]
    with loss \(L_\theta = -\Pi_\theta(S_T)\).
  - Performance functional
    \[
      J(\theta)
      = \mathbb{E}[\Pi_\theta(S_T)]
        - \lambda\,\mathrm{CVaR}_\alpha(L_\theta),
    \]
    typically with \(\alpha = 0.99\) and \(\lambda = 1\).
  - Monte Carlo estimates of:
    - initial price estimate \(Y\),
    - mean hedging error \(\mathbb{E}[\Pi]\),
    - standard deviation \(\mathrm{Std}(\Pi)\),
    - \(\mathrm{VaR}_{0.99}(L)\),
    - \(\mathrm{CVaR}_{0.99}(L)\),
    - mean–CVaR objective \(J\).

- **Parameter search over sigmoids**
  - For each family, we sample random triples
    \[
      \theta = (V,\ k_{\text{move}},\ k_{\text{level}})
    \]
    from a given box and keep the one with the best (largest) \(J(\theta)\).
  - This reproduces the comparison table in the “Numerical experiments”
    section of the paper.

---

## 2. Repository structure

A typical layout is:

- `README.md` – this file.
- `requirements.txt` – Python dependencies (if provided).
- `experiments_sigmoidal_hedging.ipynb` – main Colab / Jupyter notebook
  with the experiments.
- `sigmoidal_hedging.py` – (optional) Python module with reusable functions:
  GBM simulation, payoff computation, hedging updates, risk metrics, etc.

If your filenames differ, just adjust the instructions below accordingly.

---

## 3. Getting started

### 3.1. Requirements

The code only uses standard scientific Python packages:

- `numpy`
- `scipy`
- `pandas` (optional, for tabular summaries)
- `matplotlib`
- `tqdm` (optional, for progress bars)

You can install them via:

```bash
pip install numpy scipy pandas matplotlib tqdm
