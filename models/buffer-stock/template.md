---
title: Buffer stock saving
subtitle: Baseline template, version 0.1 (draft)
authors:
  - name: Akshay Shanker
date: "2026-09-13"
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

Saving protects consumption against uncertain labour income, which can fall to zero, but it reduces consumption today. We study how this trade-off determines the consumer's consumption and saving.

We use the infinite-horizon model in Carroll and Shanker, *Theoretical Foundations of Buffer Stock Saving*, version of 5 December 2025 ([source paper](https://econ-ark.github.io/BufferStockTheory/), [code and source files](https://github.com/econ-ark/BufferStockTheory), DOI 10.5281/zenodo.4088918). The model has one asset, a fixed interest rate, and permanent and transitory income shocks. We express resources and consumption relative to permanent income, so the numerical problem has one state variable.

```{note}
**Completing the template.** Copy the notebook into your toolkit's directory and create a `results/` directory beside it. Replace each commented code cell with your own calculation. Keep the model and calibration fixed, and add short explanations of your method and results as you work. The completed notebook, three result tables, and a metadata file form a submission; the model README gives the file list and a metadata example. The comparison cell is supplied in Python and may be translated into your notebook's language.
```

Load the packages used to solve the model and record their installation instructions and versions so another reader can run the notebook from the first cell.

```{code-cell} python
# Load the toolkit and other packages used in this notebook.
```

## 1. Income and market resources

### Income risk

Labour income in period $t$ is $\mathbf{y}_t = \mathbf{p}_t\theta_t$, where $\mathbf{p}_t$ is permanent income and $\theta_t$ is a transitory shock. Permanent income evolves according to

$$
\mathbf{p}_{t+1} = \mathcal{G}\,\psi_{t+1}\,\mathbf{p}_t,
$$

where $\mathcal{G}$ is the growth factor and $\psi_{t+1}$ is a permanent shock with mean one. A change in $\psi_{t+1}$ affects income in every subsequent period, whereas a change in $\theta_t$ affects only current income.

The transitory shock is zero with probability $0<\wp<1$. Conditional on positive income, it equals a positive shock $\Theta$ divided by $1-\wp$:

$$
\theta =
\begin{cases}
0 & \text{with probability } \wp, \\
\Theta/(1-\wp) & \text{with probability } 1-\wp .
\end{cases}
$$

The shock $\Theta$ has mean one, so $\mathbb{E}[\theta]=1$. The permanent and transitory shocks are independent of each other and over time; their distributions are specified with the calibration.

### Saving and normalisation

Market resources are the funds available for consumption and saving after current income is received. With bold letters denoting levels, consumption $\mathbf{c}_t$ leaves end-of-period assets $\mathbf{a}_t$, which earn the gross interest factor $\mathsf{R}$:

$$
\mathbf{a}_t = \mathbf{m}_t - \mathbf{c}_t, \qquad
\mathbf{m}_{t+1} = \mathsf{R}\,\mathbf{a}_t + \mathbf{y}_{t+1}.
$$

We divide resources, consumption, and assets by permanent income. Thus $m_t=\mathbf{m}_t/\mathbf{p}_t$, $c_t=\mathbf{c}_t/\mathbf{p}_t$, and $a_t=\mathbf{a}_t/\mathbf{p}_t$; for example, $m=2$ means resources equal to twice permanent income. Primes on variables denote the following period, so the normalised budget equations are

$$
a = m - c, \qquad
b' = \mathcal{R}'\,a, \qquad
m' = b' + \theta', \qquad
\mathcal{R}' = \frac{\mathsf{R}}{\mathcal{G}\,\psi'} ,
$$

where $b'$ is next period's bank balance relative to next period's permanent income. The return $\mathcal{R}'$ includes both interest on assets and the change in the income used to normalise them.

1. The consumer observes market resources $m$, after receiving current income.
2. The consumer chooses consumption $c$ and leaves assets $a=m-c$.
3. Assets earn interest, and next period's shocks $\psi'$ and $\theta'$ determine resources $m'$ before the next consumption decision.

## 2. Calibration and income shocks

We use the paper's annual calibration. The values remain the same across toolkit submissions.

| Parameter | Symbol | Value |
| --- | --- | --- |
| Permanent income growth factor | $\mathcal{G}$ | 1.03 |
| Gross interest factor | $\mathsf{R}$ | 1.04 |
| Discount factor | $\beta$ | 0.96 |
| Relative risk aversion | $\rho$ | 2 |
| Probability of zero income | $\wp$ | 0.005 |
| Standard deviation of $\log\psi$ | $\sigma_\psi$ | 0.1 |
| Standard deviation of $\log\Theta$ | $\sigma_\theta$ | 0.1 |

The paper bases income growth and income risk on the Panel Study of Income Dynamics (Carroll, 1992) and uses conventional values for preferences and the interest rate. We enter these seven parameters in the notation used by our toolkit.

```{code-cell} python
# Set the seven parameters in the calibration table.
```

The positive shocks are log-normal:

$$
\log\psi \sim N(-\sigma_\psi^2/2,\,\sigma_\psi^2), \qquad
\log\Theta \sim N(-\sigma_\theta^2/2,\,\sigma_\theta^2).
$$

Each toolkit chooses its own approximation to these distributions. The approximation must retain $\mathbb{E}[\psi]=\mathbb{E}[\Theta]=\mathbb{E}[\theta]=1$ and $\Pr(\theta=0)=\wp$ exactly.

The paper's existence results require the positive shocks to be bounded above and away from zero. Finite approximations with strictly positive values of $\psi$ and $\Theta$ meet that requirement; we apply the paper's existence results to these approximations.

We also compute $\mathbb{E}[\psi^{-1}]$ and $\mathbb{E}[\psi^{1-\rho}]$ using the same approximation as the solver. These moments enter the target and the condition checks. At $\rho=2$ they coincide; their value under the continuous distribution is $e^{\sigma_\psi^2}\simeq1.01005$, which provides a comparison for the numerical approximation.

```{code-cell} python
# Construct the income shocks and compute their means, the zero-income
# probability, E_psi_inv, and E_psi_1mrho under your approximation.
```

## 3. The consumption decision

### Preferences and the borrowing constraint

Starting from positive resources and permanent income, the consumer chooses consumption after observing current resources to maximise

$$
\mathbb{E}_0\!\left[\sum_{t=0}^{\infty}\beta^t\,
\mathrm{u}(\mathbf{c}_t)\right],
\qquad \mathrm{u}(c)=\frac{c^{1-\rho}}{1-\rho},
\qquad \rho>1,\quad\beta>0,
$$

subject to the budget equations in section 1 and $0<\mathbf{c}_t\leq\mathbf{m}_t$ after every history of income shocks. There is no mortality or bequest motive. We obtain the infinite-horizon solution as the limit of finite-horizon problems with terminal consumption equal to resources, $\mathrm{c}_T(m)=m$. Roman letters denote functions: $\mathrm{c}(m)$ is the consumption function and $c$ is a consumption amount.

No artificial borrowing constraint is imposed. If the consumer saved nothing, a zero-income realisation next period would leave no resources for consumption. Since utility tends to minus infinity as consumption approaches zero, optimal consumption is positive and leaves positive assets: $0<\mathrm{c}(m)<m$ for $m>0$.

### The Bellman equation

The stationary normalised value function solves the Bellman equation, for $m>0$,

$$
\mathrm{v}(m)=\max_{0<c<m}\left\{
\frac{c^{1-\rho}}{1-\rho}
+\beta\,\mathbb{E}\!\left[
(\mathcal{G}\psi')^{1-\rho}\,
\mathrm{v}\!\left(\frac{\mathsf{R}(m-c)}{\mathcal{G}\psi'}+\theta'\right)
\right]\right\}.
$$

This is the stationary form of the paper's normalised problem in section 2.1.1, equation (5). The first term rewards consumption today; the second values resources after interest and next period's income shocks. The factor $(\mathcal{G}\psi')^{1-\rho}$ adjusts future value for income growth. It follows from CRRA homogeneity: value in levels is $\mathbf{v}(\mathbf{m},\mathbf{p})=\mathbf{p}^{1-\rho}\mathrm{v}(\mathbf{m}/\mathbf{p})$, and optimal consumption in levels is $\mathbf{p}\,\mathrm{c}(\mathbf{m}/\mathbf{p})$.

Write the discounted expectation as $\bar{\mathrm{v}}(a)$, the end-of-period value of assets $a=m-c$. The same problem is then $\mathrm{v}(m)=\max_{0<c<m}\{\mathrm{u}(c)+\bar{\mathrm{v}}(m-c)\}$. The existence conditions in section 4 ensure that the optimum is attained in the interior for the finite shock approximation.

### The Euler equation

At the interior optimum, the first-order condition $\mathrm{u}'(c)=\bar{\mathrm{v}}'(m-c)$ equates the marginal value of consuming and saving. Differentiating the continuation value by the chain rule and applying the envelope condition $\mathrm{v}'(m)=\mathrm{u}'(\mathrm{c}(m))$ gives

$$
c^{-\rho} = \beta\,\mathsf{R}\; \mathbb{E}\bigl[\, (\mathcal{G}\psi')^{-\rho}\, \mathrm{c}(m')^{-\rho} \,\bigr],
\qquad m' = \mathcal{R}'\,(m - c) + \theta' .
$$

### Solving by the endogenous grid method

[Carroll's endogenous grid method (EGM)](https://www.econ2.jhu.edu/people/ccarroll/EndogenousGridpoints.pdf) uses the Euler equation to compute consumption at chosen asset values, then adds consumption to assets to obtain the resource grid. This avoids a separate maximisation at each resource value. A toolkit may use another method to solve the same Bellman equation.

1. Choose positive asset values $a_1<\cdots<a_N$, with more points near zero, and joint shocks $(\psi_j,\theta_j)$ with probabilities $\pi_j$ summing to one. Retain the zero-income event. Start from $\mathrm{c}^{(0)}(m)=m$, the terminal-period policy; $k$ counts iterations towards the infinite-horizon solution.

2. For every asset and shock pair, compute future resources $m'_{ij}=\mathsf{R}a_i/(\mathcal{G}\psi_j)+\theta_j$ and evaluate next period's consumption, $\mathrm{c}^{(k)}(m'_{ij})$.

3. Compute the marginal value of saving by averaging future marginal utility over the shocks:

   $$
   q_i=\beta\mathsf{R}\sum_j\pi_j(\mathcal{G}\psi_j)^{-\rho}
   \bigl[\mathrm{c}^{(k)}(m'_{ij})\bigr]^{-\rho}.
   $$

4. Recover consumption by inverting marginal utility, then add assets to obtain current resources:

   $$
   c_i=q_i^{-1/\rho},\qquad m_i=a_i+c_i.
   $$

5. Construct $\mathrm{c}^{(k+1)}$ by linearly interpolating the pairs $(m_i,c_i)$ and the limiting point $(0,0)$. Above the final point, one choice is to extend the last segment with its slope. Record the upper-grid rule; never evaluate marginal utility at zero.

6. Compare successive consumption functions at the same fixed resource points. Repeat steps 2–5 until their largest absolute difference is below a recorded tolerance. Then check the Euler equation and repeat with finer asset and shock grids and a larger asset upper bound; small iteration changes alone do not establish numerical accuracy.

Record the method, grids, interpolation, stopping tolerance, and accuracy checks in the notebook.

```{code-cell} python
# Solve the infinite-horizon problem and obtain the consumption function c(m).
```

## 4. Limiting marginal propensities to consume

The marginal propensity to consume is $\kappa(m)=\mathrm{c}'(m)$ where the derivative exists. The model gives closed-form limits for this slope, which we use to check the numerical consumption function.

Define the absolute patience factor $\text{Þ}=(\mathsf{R}\beta)^{1/\rho}$. The limiting slopes as resources tend to infinity and to zero are

$$
\underline{\kappa} = \max\Bigl\{0,\; 1 - \frac{\text{Þ}}{\mathsf{R}}\Bigr\}, \qquad
\overline{\kappa} = 1 - \wp^{1/\rho}\,\frac{\text{Þ}}{\mathsf{R}} .
$$

At the baseline, $\underline{\kappa}\simeq0.0392$ and $\overline{\kappa}\simeq0.9321$. A consumer with few resources consumes most of an additional unit of resources. At high resources, the marginal propensity to consume approaches that of the perfect-foresight consumer.

The paper states its impatience and finiteness conditions as factors below one. The final column gives the names used in the result file.

| Condition | Factor, required to be below one | Continuous-law value | Result name |
| --- | --- | --- | --- |
| Finite value of autarky | $\beta\,\mathcal{G}^{1-\rho}\,\mathbb{E}[\psi^{1-\rho}]$ | 0.941 | `FVAC` |
| Absolute impatience | $\text{Þ}$ | 0.999 | `AIC` |
| Return impatience | $\text{Þ}/\mathsf{R}$ | 0.961 | `RIC` |
| Weak return impatience | $\wp^{1/\rho}\,\text{Þ}/\mathsf{R}$ | 0.068 | `WRIC` |
| Finite human wealth | $\mathcal{G}/\mathsf{R}$ | 0.990 | `FHWC` |
| Growth impatience | $\text{Þ}/\mathcal{G}$ | 0.970 | `GIC` |
| Strong growth impatience | $(\text{Þ}/\mathcal{G})\,\mathbb{E}[\psi^{-1}]$ | 0.980 | `GICMod` |

All seven inequalities hold for the continuous-law values in the table and for the reference approximation. Under the paper's bounded-shock assumptions, weak return impatience and finite value of autarky give a limiting solution; adding strong growth impatience gives a unique target. Compute the two factors involving expectations under your own shock approximation, so they may differ slightly from the continuous-law values in the table.

Display the two limiting slopes and the seven condition factors. Save the condition names and whether each factor is below one in `results/conditions.csv`, with columns `name,holds` and values `true` or `false`; retain the slopes for the scalar table in section 6.

```{code-cell} python
# Compute and display the limiting MPCs and the seven condition factors;
# write each condition's name and true/false result to results/conditions.csv.
```

## 5. The consumption function

We evaluate $\mathrm{c}(m)$ at the following 16 resource values. The second column contains the reference calculation for comparison; all quantities are relative to permanent income.

| Market resources $m$ | Reference consumption $\mathrm{c}_{\mathrm{ref}}(m)$ |
| --- | --- |
| 0.25 | 0.2324 |
| 0.5 | 0.4607 |
| 0.75 | 0.6759 |
| 1 | 0.8528 |
| 1.25 | 0.9647 |
| 1.5 | 1.0346 |
| 2 | 1.1274 |
| 2.5 | 1.1940 |
| 3 | 1.2485 |
| 4 | 1.3401 |
| 5 | 1.4195 |
| 6 | 1.4919 |
| 8 | 1.6241 |
| 10 | 1.7461 |
| 15 | 2.0262 |
| 20 | 2.2867 |

Save the resource and consumption values in `results/cfunc.csv`, with columns `m,c` and one row per resource value. Then plot consumption against market resources over $m\in[0,20]$, together with the reference points and the lines $\underline{\kappa}m$ and $\overline{\kappa}m$; use the limiting value $\mathrm{c}(0)=0$ at the origin. Label the horizontal axis “Market resources / permanent income” and the vertical axis “Consumption / permanent income”, and identify the curves and reference points in the legend.

```{code-cell} python
# Evaluate c(m) on the 16-point grid, write results/cfunc.csv,
# and plot consumption, the reference points, and the two bounding lines.
```

The paper's bounds require $\underline{\kappa}m\leq\mathrm{c}(m)\leq\overline{\kappa}m$ for $m>0$. Check that the computed function stays within these bounds and is increasing and concave. In the completed notebook, describe how its slope changes with resources and where its values differ from the reference points.

## 6. Target resources and the marginal propensity to consume

Income risk encourages saving, while impatience limits how much wealth the consumer wants to accumulate. At the baseline, these incentives imply a target $\hat{m}$: expected resources rise when resources are below $\hat{m}$ and fall when they are above it. The target is a statement about conditional expected resources, not a level that every simulated consumer reaches or the mean of the stationary distribution.

Using the budget equation and the independence of the shocks, expected next-period resources are

$$
\mathbb{E}[m' \mid m] = \bigl(m - \mathrm{c}(m)\bigr)\,\frac{\mathsf{R}}{\mathcal{G}}\,\mathbb{E}[\psi^{-1}] + 1 .
$$

We find $\hat{m}$ by solving $\mathbb{E}[m'\mid\hat{m}]-\hat{m}=0$, using the moment $\mathbb{E}[\psi^{-1}]$ computed from our shock approximation. For comparison across toolkits, the reported marginal propensity to consume at the target uses the same central difference:

$$
\kappa(\hat{m}) \approx
\frac{\mathrm{c}(\hat{m}+h)-\mathrm{c}(\hat{m}-h)}{2h},
\qquad h=0.01.
$$

The reference target is $1.3910$ and its reported marginal propensity to consume is $0.2696$. The fixed difference step makes the reported slope less sensitive to the location of interpolation points than a much smaller step.

```{code-cell} python
# Find m_target, compute mpc_at_target using h = 0.01,
# and check expected resource growth on either side of the target.
```

As a check on the target, evaluate $\mathbb{E}[m'\mid m]-m$ at $m=\hat{m}-0.5$ and $m=\hat{m}+0.5$. The first value should be positive and the second negative at the baseline.

Save the scalar results in `results/scalars.csv`, with columns `name,value`. Write each of the following six rows once, using the quantities computed in the notebook.

| Row name | Quantity |
| --- | --- |
| `m_target` | Target resources $\hat{m}$ |
| `mpc_at_target` | Central difference of consumption at $\hat{m}$, with $h=0.01$ |
| `kappa_min` | Limiting marginal propensity to consume $\underline{\kappa}$ |
| `kappa_max` | Limiting marginal propensity to consume $\overline{\kappa}$ |
| `E_psi_inv` | $\mathbb{E}[\psi^{-1}]$ under the solver's shock approximation |
| `E_psi_1mrho` | $\mathbb{E}[\psi^{1-\rho}]$ under the solver's shock approximation |

```{code-cell} python
# Write all six rows to results/scalars.csv.
```

## 7. Comparison with the reference

The reference uses seven equiprobable values for each positive shock and 960 asset grid points. The `reference/` directory records the calculation, the full-precision values, and a sensitivity study; a submission does not need to reproduce those supporting files.

The current draft uses the following tolerances. They remain provisional while the library reviews the template.

| Quantity | Allowed absolute difference from the reference |
| --- | --- |
| Consumption at each of the 16 resource values | 0.02 |
| Target resources | 0.02 |
| Marginal propensity to consume at the target | 0.01 |
| Each limiting marginal propensity to consume | $10^{-6}$ |
| Each of the seven condition results | Must agree exactly |

The shock approximation accounts for more variation in the reference calculations than the asset grid. Increasing the number of positive shock values from seven to twenty-one changes consumption by up to $0.013$ and the target by $0.008$, while halving or doubling the asset grid changes consumption by less than $0.0001$. The draft tolerances allow for these differences because toolkit authors choose their own shock approximations. The two reported shock moments help explain differences and have no separate tolerance.

The notebook includes a comparison cell that prints absolute differences and their tolerances. Run it from the directory containing `tutorial.ipynb` and `results/`, or change `results_dir` to that folder. Discuss differences exceeding a tolerance in the author's notes.

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


## 8. Author's notes

Explain the numerical choices that affect the results, including the shock approximation, asset grid, and convergence criterion. If the toolkit cannot represent part of the stated model, describe the change and its expected effect on the reported quantities. For differences beyond a tolerance, report the quantity and size of the difference, and distinguish an explanation you have checked from one that remains to be tested.

Complete `metadata.yml` using the example in the model README, and retain the notebook's computed tables and figures when submitting. The library records differences between methods without ranking toolkits or comparing run times.
