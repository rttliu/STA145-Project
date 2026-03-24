# Bayesian Inference for Midge Wing-Length Data

This project studies Bayesian inference for the mean and variance of midge wing-length data under a small-sample normal model. Using a dataset of nine wing-length measurements, the analysis compares two Bayesian specifications: a baseline model with a fixed variance-prior scale and a hierarchical extension in which the variance-prior scale is itself treated as random. The main goal is not only to estimate the population mean and variance, but also to examine how hierarchical prior design affects posterior inference, posterior dependence, predictive fit, and MCMC behavior. 

## Overview

In very small samples, inference for variance parameters is often much more sensitive to prior assumptions than inference for location parameters. This project uses a simple biological dataset to study that issue in a controlled setting. The report compares:

* **Case 1:** a conjugate Bayesian model with a fixed variance-prior scale
* **Case 2:** a hierarchical Bayesian model that places an additional Gamma prior layer on the variance-prior scale 

The central question is how much changes when uncertainty about the variance-prior scale is explicitly modeled rather than fixed in advance. 

## Data

The data consist of nine midge wing-length measurements (in millimeters):

`1.64, 1.70, 1.72, 1.74, 1.82, 1.82, 1.82, 1.90, 2.08`

with sample mean `1.804`. Because the sample size is only `n = 9`, the problem is a natural test case for prior sensitivity, especially for variance inference. 

## Model Setup

The sampling model is

$$
Y_i \mid \theta, \sigma^2 \overset{iid}{\sim} N(\theta, \sigma^2), \quad i=1,\dots,n.
$$

For computational convenience, the analysis works with the precision parameter ( \tilde{\sigma}^2 = 1/\sigma^2 ), which keeps the conditional updates conjugate and allows Gibbs sampling without a Metropolis–Hastings step. 

### Case 1: Fixed variance-prior scale

The baseline model uses:

* ( \theta \sim N(\mu_0, \tau_0^2) )
* ( \tilde{\sigma}^2 \sim \text{Gamma}(\nu_0/2, (\nu_0/2)\sigma_0^2) )

with the variance-prior scale ( \sigma_0^2 ) treated as fixed. This gives a clean conjugate benchmark. 

### Case 2: Hierarchical variance-prior scale

The hierarchical model replaces the fixed prior scale with an additional layer:

* ( \theta \sim N(\mu_0, \tau_0^2) )
* ( \tilde{\sigma}^2 \mid \sigma_0^2 \sim \text{Gamma}(\nu_0/2, (\nu_0/2)\sigma_0^2) )
* ( \sigma_0^2 \mid \beta \sim \text{Gamma}(a, \beta) )
* ( \beta \sim \text{Gamma}(b_1, b_2) )

This allows the data to update beliefs about the variance-prior scale instead of assuming that scale is known in advance. 

## Hyperprior Sensitivity Design

To study prior sensitivity within the hierarchical model, the project uses a (3 \times 3) factorial design:

* ( a \in {0.1, 2, 10} )
* ( (b_1, b_2) \in {(0.1,10), (2,200), (10,1000)} )

These three ( \beta )-prior settings have the same prior mean but different concentrations, corresponding to weak, medium, and strong regularization. This yields nine Case 2 configurations in total. 

## Computation

Posterior inference is obtained with **Gibbs sampling**.

* For **Case 1**, the sampler alternates between updates for ( \theta ) and ( \tilde{\sigma}^2 )
* For **Case 2**, the sampler cycles through ( \theta \rightarrow \tilde{\sigma}^2 \rightarrow \sigma_0^2 \rightarrow \beta )

For each prior setting, the report runs the Gibbs sampler for **10,000 iterations**, discards the first **2,000** as burn-in, and uses the remaining **8,000** draws for posterior inference. 

## What This Project Examines

The comparison goes beyond posterior means. The report studies:

* posterior summaries for ( \theta ) and ( \sigma^2 )
* marginal posteriors for the hierarchical parameters ( \sigma_0^2 ) and ( \beta )
* posterior dependence and joint posterior geometry
* predictive fit using criteria such as WAIC and lppd
* MCMC diagnostics such as ESS, MCSE, and lag-1 autocorrelation

## Main Findings

### 1. Inference for the mean is relatively robust

Across the nine hierarchical prior settings, posterior inference for ( \theta ) changes very little in location. The posterior mean stays in a narrow range, while prior choice mainly affects interval width rather than the posterior center. 

### 2. Inference for the variance is much more sensitive

In contrast, posterior summaries for ( \sigma^2 ) vary substantially across hierarchical prior choices. Weak regularization and more diffuse variance hierarchy settings can produce broader and more right-skewed posterior distributions for variance. 

### 3. The sensitivity is driven by the variance hierarchy itself

The hierarchical parameters ( \sigma_0^2 ) and ( \beta ) are not passive additions. Their posterior movement explains much of the variation seen in the posterior of ( \sigma^2 ), especially under weakly regularized settings. 

### 4. Hierarchical modeling improves flexibility but can hurt mixing

The hierarchical model can improve predictive fit and modestly stabilize inference for ( \theta ), but it also creates stronger posterior dependence among variance-related parameters. As a result, ( \sigma^2 ) often mixes more slowly than ( \theta ).

### 5. Best-performing hierarchical setting

Among the nine Case 2 specifications, the report identifies the best overall configuration as:

$$
a = 0.1, \quad \beta \sim \Gamma(10,1000)
$$

This setting balances flexibility in the prior shape for the variance scale with strong regularization on the scale-controlling hyperparameter. 

## Conclusion

The main conclusion of this project is that **hierarchical variance priors matter much more for variance inference than for mean inference** in this small-sample normal problem. The hierarchical model offers a more realistic treatment of uncertainty in the variance-prior scale, but that gain comes with stronger posterior coupling and weaker MCMC efficiency for variance-related parameters. In this setting, the best results come not from an unrestricted hierarchy, but from a balance between flexibility and regularization.



## Reproducibility

To reproduce the analysis:

1. Prepare the wing-length dataset
2. Run the Gibbs sampler for Case 1 and all nine Case 2 hyperprior settings
3. Compute posterior summaries, predictive-fit metrics, and MCMC diagnostics
4. Regenerate the figures and report

## Author Notes

This repository was developed as part of a Bayesian statistics course project on prior sensitivity and hierarchical modeling in small-sample inference. The emphasis is on both **inferential interpretation** and **computational behavior**, rather than point estimation alone. 

