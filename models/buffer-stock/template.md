---
title: Buffer stock saving
subtitle: Baseline template, version 0.1 (draft)
date: '2026-09-14'
license: CC-BY-4.0
exports:
- format: pdf
  template: ../../templates/plain_latex_wide
  output: template.pdf
---

## 1. Model theory

We consider a consumer who saves in one risk-free asset and receives labour income with permanent and transitory shocks, following Carroll's buffer-stock models (1992, 1997).[^carroll] We use the model and notation of Carroll and Shanker, [*Theoretical Foundations of Buffer Stock Saving*](https://econ-ark.github.io/BufferStockTheory/BufferStockTheory.pdf), version of 5 December 2025. The stationary solution is the limit of finite-horizon problems, with no mortality or bequest motive.

[^carroll]: Carroll, C. D. (1992), [“The Buffer-Stock Theory of Saving: Some Macroeconomic Evidence”](https://www.brookings.edu/articles/the-buffer-stock-theory-of-saving-some-macroeconomic-evidence/), *Brookings Papers on Economic Activity*, 1992(2), 61–156; Carroll, C. D. (1997), [“Buffer-Stock Saving and the Life Cycle/Permanent Income Hypothesis”](https://academic.oup.com/qje/article-abstract/112/1/1/1870884), *Quarterly Journal of Economics*, 112(1), 1–55.

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

## 3. Numerical and methodological considerations

Value function iteration is one way to solve the model. The paper's [HARK notebook](https://github.com/econ-ark/BufferStockTheory/blob/master/Code/Python/BufferStockTheory-Problems-and-Solutions-Source.ipynb) uses the endogenous grid method, linear interpolation and twenty times the default number of asset grid points, iterating from the terminal consumption rule to convergence. Retain the calibration above; some notebook figures alter parameters.

Use finite shock approximations that preserve the means and zero-income probability specified above. The paper's existence results require positive shocks bounded above and away from zero. Finite approximations with strictly positive $\psi$ and $\theta$ satisfy this assumption, but log-normal laws do not. Weak return impatience and finite value of autarky then give a finite value function and a positive limiting consumption function:

$$
\wp^{1/\gamma}\frac{\text{\textbf{Þ}}}{\mathsf{R}}<1,\qquad
\beta\mathcal{G}^{1-\gamma}\mathbb{E}[\psi^{1-\gamma}]<1,
\qquad \text{\textbf{Þ}}=(\mathsf{R}\beta)^{1/\gamma}.
$$

Adding strong growth impatience, $(\text{\textbf{Þ}}/\mathcal{G})\mathbb{E}[\psi^{-1}]<1$, gives a unique individual target. These inequalities hold for the continuous laws and the HARK approximation. Evaluate them, and the target equation, using the solver's shock approximation.

The notebook checks convergence under greater permanent-income risk by increasing the asset range and grid density and tightening the tolerance. Also refine the shock approximation and check extrapolation, Euler residuals, monotonicity, concavity and consumption bounds; the stopping tolerance alone does not measure accuracy. Linear interpolation creates kinks, so compare marginal propensities to consume at the target using $[\mathrm{c}(\hat m+h)-\mathrm{c}(\hat m-h)]/(2h)$ with $h=0.01$.
