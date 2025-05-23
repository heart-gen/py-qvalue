# py-qvalue

A Python package for estimating q-values and controlling the False Discovery
Rate (FDR), based on the functionalities of the renowned R package `qvalue`.

## Overview

`py-qvalue` brings key methods from the R `qvalue` package to the Python
ecosystem. It is designed for researchers and analysts who perform multiple
hypothesis testing and need robust ways to estimate the proportion of true null
hypotheses ($\pi_0$), calculate q-values, and estimate local false discovery
rates (lfdr).

This package currently provides Python implementations of the core `qvalue` and
`pi0est` functions, along with the necessary `lfdr` calculation, allowing for
straightforward FDR control within your Python workflows.

## Installation

You can install `py-qvalue` using pip:

```bash
pip install py-qvalue
```

## Quick Start

A basic example demonstrating how to use `py-qvalue` to calculate
q-values from a set of p-values:

```python
import numpy as np
from py_qvalue.core import qvalue

# Parameters
n_tests = 1000
effect_size = 0.3
null_proportion = 0.9

# Generate null (uniform) and signal (skewed low) components
n_null = int(n_tests * null_proportion)
n_signal = n_tests - n_null

null_p = np.random.uniform(0, 1, n_null)
signal_p = np.random.beta(1, 15, n_signal)  # Stronger signal = more left skew

# Combine and shuffle
p_values = np.concatenate([null_p, signal_p])
np.random.shuffle(p_values)

# Calculate q-values
qvalue_results = qvalue(p_values)

# Access the q-values
q_values = qvalue_results['qvalues']
print("P-values:", p_values[0:10]) # Just the first ten
print("Q-values:", q_values[0:10])

# Access the estimated proportion of true null hypotheses (pi0)
pi0_estimate = qvalue_results['pi0']
print("Estimated pi0:", pi0_estimate)

# Get significant results at a specific FDR level (e.g., 10%)
results_at_fdr = qvalue(p_values, fdr_level=0.10)
significant_mask = results_at_fdr['significant']
print("Significant at 10% FDR:", significant_mask)
```

## Implemented Functions

As a port of `qvalue`, we use the same core functions includes:

* `qvalue(p, fdr_level=None, pfdr=False, lfdr_out=True, pi0=None, **kwargs)`:
  Estimates q-values and related FDR quantities from a set of p-values.
* `pi0est(p, lambda_=None, pi0_method="smoother", smooth_df=3, smooth_log_pi0=False, **kwargs)`:
  Estimates the proportion of true null hypotheses ($\pi_0$).
* `lfdr(p, pi0, trunc=True, monotone=True, transf="probit", adj=1.5, eps=1e-8, **kwargs)`:
  Estimates local false discovery rates (lfdr).

Users can import `qvalue` and `pi0est` from this package.

``` python
from qvalue import pi0est, qvalue
```

## Relationship to the R `qvalue` Package

This package is a Python port inspired by and aiming to replicate the
functionality of the original `qvalue` package for R, developed by John D.
Storey and colleagues. The R `qvalue` package is a widely cited and respected
tool for false discovery rate control in multiple hypothesis testing.

For more information on the statistical methods and the original R
implementation, please refer to:

* The original R `qvalue` [package documentation and resources](https://bioconductor.org/packages/release/bioc/html/qvalue.html).
* Relevant publications by John D. Storey and Robert Tibshirani on false discovery rates and q-values.
  - [Statistical significance for genomewide studies](https://www.pnas.org/doi/abs/10.1073/pnas.1530509100)
  - [A direct approach to false discovery rates](https://academic.oup.com/jrsssb/article-abstract/64/3/479/7098513)
  - [The positive false discovery rate: a Bayesian interpretation and the q-value](https://projecteuclid.org/journals/annals-of-statistics/volume-31/issue-6/The-positive-false-discovery-rate--a-Bayesian-interpretation-and/10.1214/aos/1074290335.full)

## Contributing

We welcome contributions to `py-qvalue`! If you find a bug, have a feature
request, or want to contribute code, please feel free to open an issue or submit
a pull request on the GitHub repository.

## License

This project is licensed under the GPL v3 License. See the `LICENSE` file for
details.
