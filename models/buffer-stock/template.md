---
title: Buffer stock saving
subtitle: Baseline template, version 0.1 (draft)
authors:
  - name: Akshay Shanker
date: "2026-09-14"
license: CC-BY-4.0
exports:
  - format: pdf
    template: ../../templates/plain_latex_wide
    output: template.pdf
kernelspec:
  name: python3
  display_name: Python 3
  language: python
jupytext:
  formats: md:myst,ipynb
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
---

+++ {"tags": ["no-tex"]}

```{note}
**Completing the notebook.** Copy this notebook into your toolkit's directory and create a `results/` directory beside it. Replace each commented code cell with your own calculation, keeping the model and calibration fixed. The model README lists the five submission files. The PDF contains the model specification; this notebook adds the exercises and reporting instructions. The comparison cell is supplied in Python and may be translated into your notebook's language.
```

**Set up the notebook.** Load the packages used to solve the model and record their installation instructions and versions so another reader can run it from the first cell.

```{code-cell} python
:tags: [no-tex]

# Load the toolkit and other packages used in this notebook.
```

+++

## 1. Model theory

We consider a consumer who saves in one risk-free asset and receives labour income with permanent and transitory shocks. Following Carroll and Shanker, *Theoretical Foundations of Buffer Stock Saving*, version of 5 December 2025 ([paper and source](https://github.com/econ-ark/BufferStockTheory)), we obtain the stationary solution as the limit of finite-horizon problems. There is no mortality or bequest motive.

The consumer has CRRA utility $\mathrm{u}(c)=c^{1-\gamma}/(1-\gamma)$, with relative risk aversion $\gamma>1$ and discount factor $\beta>0$. Bold Latin letters denote levels. Permanent income $\boldsymbol{p}_t$ grows by $\tilde{\mathcal{G}}_{t+1}=\mathcal{G}\psi_{t+1}$, where $\mathcal{G}>0$ is deterministic growth and $\psi$ is a permanent shock. Labour income is $\boldsymbol{y}_t=\boldsymbol{p}_t\pmb{\xi}_t$, with transitory shock

$$
\pmb{\xi} =
\begin{cases}
0 & \text{with probability } \wp,\\
\theta/(1-\wp) & \text{with probability } 1-\wp,
\end{cases}
\qquad 0<\wp<1.
$$

The positive shocks $\psi$ and $\theta$ have mean one and are independent of each other and over time, as is the zero-income event. Thus $\mathbb{E}[\pmb{\xi}]=1$.

After receiving income, the consumer observes market resources and chooses consumption. Divide resources, consumption and end-of-period assets by permanent income: $m_t=\boldsymbol{m}_t/\boldsymbol{p}_t$, $c_t=\boldsymbol{c}_t/\boldsymbol{p}_t$ and $a_t=\boldsymbol{a}_t/\boldsymbol{p}_t$. With gross interest factor $\mathsf{R}>0$, the normalised budget equations are

$$
a_t=m_t-c_t,\qquad
b_{t+1}=\tilde{\mathscr{R}}_{t+1}a_t,\qquad
m_{t+1}=b_{t+1}+\pmb{\xi}_{t+1},\qquad
\tilde{\mathscr{R}}_{t+1}=\frac{\mathsf{R}}{\tilde{\mathcal{G}}_{t+1}}.
$$

Here $b_{t+1}$ is the bank balance relative to next period's permanent income. Zero-income risk makes zero assets the natural debt limit: admissible plans satisfy $0<\boldsymbol{c}_t\leq\boldsymbol{m}_t$ after every shock history. At the terminal date $T$, $\mathrm{c}_T(m)=m$ and $\mathrm{v}_T(m)=\mathrm{u}(m)$. Before $T$, utility tending to minus infinity at zero consumption makes saving nothing suboptimal, so $0<\mathrm{c}_t(m)<m$.

Let $\mathrm{v}$ and $\mathrm{c}$ denote the stationary normalised value and consumption functions. Value in levels is $\boldsymbol{\mathrm{v}}(\boldsymbol{m},\boldsymbol{p})=\boldsymbol{p}^{1-\gamma}\mathrm{v}(\boldsymbol{m}/\boldsymbol{p})$. The normalised Bellman equation is

$$
\mathrm{v}(m)=\max_{0<c<m}\left\{
\mathrm{u}(c)+\beta\,\mathbb{E}\!\left[
(\tilde{\mathcal{G}}')^{1-\gamma}
\mathrm{v}\!\left(\tilde{\mathscr{R}}'(m-c)+\pmb{\xi}'\right)
\right]\right\},\qquad m>0.
$$

Primes denote next-period quantities; expectations integrate over both income shocks. The growth factor adjusts continuation value for the change in permanent income. The first-order and envelope conditions give the normalised Euler equation

$$
\mathrm{c}(m)^{-\gamma}
=\beta\mathsf{R}\,\mathbb{E}\!\left[
(\tilde{\mathcal{G}}')^{-\gamma}\mathrm{c}(m')^{-\gamma}
\right],\qquad
m'=\tilde{\mathscr{R}}'\bigl(m-\mathrm{c}(m)\bigr)+\pmb{\xi}'.
$$

Precautionary saving becomes more attractive as resources fall, while impatience encourages consumers to spend down high resources. At the baseline these incentives produce a target $\hat m$, below which expected resources rise and above which they fall. The target satisfies

$$
\bigl(\hat m-\mathrm{c}(\hat m)\bigr)
\frac{\mathsf{R}}{\mathcal{G}}\,\mathbb{E}[\psi^{-1}]+1=\hat m.
$$

It is a point of zero conditional expected resource growth, not the mean of the stationary distribution.

## 2. Calibration

We use the paper's annual calibration. Income growth and risk draw on the Panel Study of Income Dynamics (Carroll, 1992); preferences and the interest rate take conventional values.

| Parameter | Symbol | Value |
| --- | --- | --- |
| Income growth factor | $\mathcal{G}$ | 1.03 |
| Gross interest factor | $\mathsf{R}$ | 1.04 |
| Discount factor | $\beta$ | 0.96 |
| Relative risk aversion | $\gamma$ | 2 |
| Probability of zero income | $\wp$ | 0.005 |
| Standard deviation of $\log\psi$ | $\sigma_\psi$ | 0.1 |
| Standard deviation of $\log\theta$ | $\sigma_\theta$ | 0.1 |

The positive shocks are log-normal with means normalised to one:

$$
\log\psi\sim N(-\sigma_\psi^2/2,\sigma_\psi^2),\qquad
\log\theta\sim N(-\sigma_\theta^2/2,\sigma_\theta^2).
$$

All submissions retain this calibration but may approximate the shock distributions differently.

+++ {"tags": ["no-tex"]}

**Enter the calibration.** Set the seven parameters above using your toolkit's notation. The paper uses $\gamma$ for risk aversion; some toolkits and the reference code call it `rho` or `CRRA`.

```{code-cell} python
:tags: [no-tex]

# Set the seven parameters in the calibration table.
```

+++ {"tags": ["no-tex"]}

**Approximate income risk.** Construct the joint shocks used by the solver. Check their means and the probability of zero income. Compute the inverse moments needed below using these same shocks. At $\gamma=2$ they coincide; their continuous-law value is $e^{\sigma_\psi^2}$. The CSV name `E_psi_1mrho` is retained for compatibility and denotes $\mathbb{E}[\psi^{1-\gamma}]$.

```{code-cell} python
:tags: [no-tex]

# Construct the income shocks and compute their means, the zero-income
# probability, E_psi_inv, and E_psi_1mrho under your approximation.
```

+++

## 3. Numerical and methodological considerations

Value function iteration is one way to solve the model. The paper's [HARK notebook](https://github.com/econ-ark/BufferStockTheory/blob/master/Code/Python/BufferStockTheory-Problems-and-Solutions-Source.ipynb) uses the endogenous grid method, linear interpolation and twenty times the default number of asset grid points, iterating from the terminal consumption rule to convergence. Retain the calibration above; some notebook figures alter parameters.

Use finite shock approximations that preserve the means and zero-income probability specified above. The paper's existence results require positive shocks bounded above and away from zero. Finite approximations with strictly positive $\psi$ and $\theta$ satisfy this assumption, but log-normal laws do not. Weak return impatience and finite value of autarky then give a finite value function and a positive limiting consumption function:

$$
\wp^{1/\gamma}\frac{\text{\textbf{Þ}}}{\mathsf{R}}<1,\qquad
\beta\mathcal{G}^{1-\gamma}\mathbb{E}[\psi^{1-\gamma}]<1,
\qquad \text{\textbf{Þ}}=(\mathsf{R}\beta)^{1/\gamma}.
$$

Adding strong growth impatience, $(\text{\textbf{Þ}}/\mathcal{G})\mathbb{E}[\psi^{-1}]<1$, gives a unique individual target. These inequalities hold for the continuous laws and the reference approximation. Evaluate them, and the target equation, using the solver's shock approximation.

The notebook checks convergence under greater permanent-income risk by increasing the asset range and grid density and tightening the tolerance. Also refine the shock approximation and check extrapolation, Euler residuals, monotonicity, concavity and consumption bounds; the stopping tolerance alone does not measure accuracy. Linear interpolation creates kinks, so compare marginal propensities to consume at the target using $[\mathrm{c}(\hat m+h)-\mathrm{c}(\hat m-h)]/(2h)$ with $h=0.01$.

+++ {"tags": ["no-tex"]}

**Solve the model.** Choose a numerical method and explain how it solves the stated problem. Record the grids, interpolation, stopping tolerance and accuracy checks.

```{code-cell} python
:tags: [no-tex]

# Solve the infinite-horizon problem and obtain the consumption function c(m).
```

+++ {"tags": ["no-tex"]}

**Check the theoretical conditions.** The consumption function is increasing and concave. Its marginal propensity to consume (MPC) tends to $\underline\kappa$ as resources tend to infinity and to $\overline\kappa$ as resources tend to zero:

$$
\underline\kappa=\max\!\left\{0,1-\frac{\text{\textbf{Þ}}}{\mathsf{R}}\right\},\qquad
\overline\kappa=1-\wp^{1/\gamma}\frac{\text{\textbf{Þ}}}{\mathsf{R}},\qquad
\underline\kappa m\leq\mathrm{c}(m)\leq\overline\kappa m.
$$

Display the two limiting MPCs and the following seven factors. Save the condition names and whether each factor is below one in `results/conditions.csv`, with columns `name,holds` and values `true` or `false`. All seven inequalities hold for the baseline reference approximation.

| Condition | Factor, required to be below one | Result name |
| --- | --- | --- |
| Finite value of autarky | $\beta\mathcal{G}^{1-\gamma}\mathbb{E}[\psi^{1-\gamma}]$ | `FVAC` |
| Absolute impatience | $\text{\textbf{Þ}}$ | `AIC` |
| Return impatience | $\text{\textbf{Þ}}/\mathsf{R}$ | `RIC` |
| Weak return impatience | $\wp^{1/\gamma}\text{\textbf{Þ}}/\mathsf{R}$ | `WRIC` |
| Finite human wealth | $\mathcal{G}/\mathsf{R}$ | `FHWC` |
| Growth impatience | $\text{\textbf{Þ}}/\mathcal{G}$ | `GIC` |
| Strong growth impatience | $(\text{\textbf{Þ}}/\mathcal{G})\mathbb{E}[\psi^{-1}]$ | `GICMod` |

```{code-cell} python
:tags: [no-tex]

# Compute and display the limiting MPCs and the seven condition factors;
# write each condition's name and true/false result to results/conditions.csv.
```

+++ {"tags": ["no-tex"]}

**Evaluate and plot consumption.** Evaluate $\mathrm{c}(m)$ at

$$
m\in\{0.25,0.5,0.75,1,1.25,1.5,2,2.5,3,4,5,6,8,10,15,20\}.
$$

Save these values in `results/cfunc.csv`, with columns `m,c` and one row per resource value. Plot consumption over $m\in[0,20]$ with the reference points and bounding lines $\underline\kappa m$ and $\overline\kappa m$, using $\mathrm{c}(0)=0$ at the origin. Label the axes “Market resources / permanent income” and “Consumption / permanent income”, and identify the curves and reference points in the legend. The comparison cell below contains the reference values; `reference/results/` stores them at full precision.

```{code-cell} python
:tags: [no-tex]

# Evaluate c(m) on the 16-point grid, write results/cfunc.csv,
# and plot consumption, the reference points, and the two bounding lines.
```

+++ {"tags": ["no-tex"]}

Check that consumption is increasing, concave and within its theoretical bounds. Describe how its slope changes with resources and where its values differ from the reference.

**Find the target and its MPC.** Solve the target equation in section 1 using your shock approximation and compute the central difference with $h=0.01$. Check that expected resource growth is positive at $\hat m-0.5$ and negative at $\hat m+0.5$.

```{code-cell} python
:tags: [no-tex]

# Find m_target, compute mpc_at_target using h = 0.01,
# and check expected resource growth on either side of the target.
```

+++ {"tags": ["no-tex"]}

Save the following six rows in `results/scalars.csv`, with columns `name,value`.

| Row name | Quantity |
| --- | --- |
| `m_target` | Target resources $\hat m$ |
| `mpc_at_target` | Central difference of consumption at $\hat m$, with $h=0.01$ |
| `kappa_min` | Limiting MPC $\underline\kappa$ |
| `kappa_max` | Limiting MPC $\overline\kappa$ |
| `E_psi_inv` | $\mathbb{E}[\psi^{-1}]$ under the solver's shock approximation |
| `E_psi_1mrho` | $\mathbb{E}[\psi^{1-\gamma}]$ under the solver's shock approximation |

```{code-cell} python
:tags: [no-tex]

# Write all six rows to results/scalars.csv.
```

+++ {"tags": ["no-tex"]}

**Compare the results.** The reference uses seven equiprobable values for each positive shock and 960 asset grid points. Its source, full-precision results and sensitivity study are in `reference/`; submissions do not need to reproduce those supporting files. The draft tolerances remain provisional while the library reviews the template.

| Quantity | Allowed absolute difference from the reference |
| --- | --- |
| Consumption at each of the 16 resource values | 0.02 |
| Target resources | 0.02 |
| MPC at the target | 0.01 |
| Each limiting MPC | $10^{-6}$ |
| Each of the seven condition results | Must agree exactly |

Increasing the number of positive shock values from seven to twenty-one changes reference consumption by up to $0.013$ and the target by $0.008$, while halving or doubling the asset grid changes consumption by less than $0.0001$. The draft tolerances allow for these differences. The two shock moments help explain differences and have no separate tolerance.

Run the comparison cell from the directory containing `tutorial.ipynb` and `results/`, or change `results_dir` to that folder.

```{code-cell} python
:tags: [no-tex]

import csv
import pathlib

results_dir = pathlib.Path("results")

reference_c = {
    0.25: 0.2324218, 0.5: 0.4606692, 0.75: 0.6758563, 1.0: 0.8527854,
    1.25: 0.9647240, 1.5: 1.0346454, 2.0: 1.1273869, 2.5: 1.1939523,
    3.0: 1.2484674, 4.0: 1.3401163, 5.0: 1.4194961, 6.0: 1.4918864,
    8.0: 1.6241387, 10.0: 1.7460689, 15.0: 2.0262320, 20.0: 2.2867095,
}
reference_scalars = {
    "m_target": 1.3910311, "mpc_at_target": 0.2695590,
    "kappa_min": 0.0392311, "kappa_max": 0.9320634,
}
tolerance = {"c": 0.02, "m_target": 0.02, "mpc_at_target": 0.01,
             "kappa_min": 0.000001, "kappa_max": 0.000001}
reference_conditions = {"FVAC": True, "AIC": True, "RIC": True, "WRIC": True,
                        "FHWC": True, "GIC": True, "GICMod": True}

with open(results_dir / "cfunc.csv") as f:
    c = {float(row["m"]): float(row["c"]) for row in csv.DictReader(f)}
with open(results_dir / "scalars.csv") as f:
    scalars = {row["name"]: float(row["value"]) for row in csv.DictReader(f)}
with open(results_dir / "conditions.csv") as f:
    conditions = {row["name"]: row["holds"].strip().lower() in ("true", "yes", "1")
                  for row in csv.DictReader(f)}

gap_c = max(abs(c[m] - c_ref) for m, c_ref in reference_c.items())
print(f"consumption function: largest deviation {gap_c:.2e} "
      f"(tolerance {tolerance['c']})")
for name, value in reference_scalars.items():
    gap = abs(scalars[name] - value)
    print(f"{name}: {scalars[name]:.6f}, deviation {gap:.2e} "
          f"(tolerance {tolerance[name]})")
disagreements = [k for k, v in reference_conditions.items()
                 if conditions.get(k) is not v]
print("conditions:", "all agree" if not disagreements
      else f"disagree on {disagreements}")
```

+++ {"tags": ["no-tex"]}

**Finish the submission.** Explain any model changes and differences beyond a tolerance, distinguishing explanations you have checked from those still to be tested. Complete `metadata.yml` using the example in the model README, and retain the notebook's computed tables and figures when submitting. The library records differences between methods without ranking toolkits or comparing run times.
