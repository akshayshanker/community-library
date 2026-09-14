# Buffer stock saving

Read the [model description](template.md) or its [three-page PDF](template.pdf)
for the theory, calibration and numerical considerations. Implementations live
under `projects/<toolkit>/BST/`. The [HARK notebook](../../projects/HARK/BST/tutorial.ipynb)
is a runnable example with brief explanations of its code.

The Markdown is the shared model description. Each project maintains its own
implementation notebook; notebooks are not generated from the model description.

## What to submit

Add your implementation under `projects/<toolkit>/BST/`:

```text
projects/<toolkit>/BST/
    tutorial.ipynb
    metadata.yml
    results/
        cfunc.csv
        scalars.csv
        conditions.csv
```

| File | What to produce |
| --- | --- |
| `tutorial.ipynb` | Runnable implementation with short explanations, figures and computed results. Link to the shared model description. |
| `metadata.yml` | Toolkit and software versions, implementation authors, model version and numerical settings. |
| `results/cfunc.csv` | Consumption at the common resource values below. |
| `results/scalars.csv` | Target resources, MPCs and the two permanent-shock moments below. |
| `results/conditions.csv` | Whether each of the seven model conditions holds. |

Include an environment file and any code the notebook imports. The HARK
example has a `solve.py` helper module and optional numerical tests; other
implementations need only the files required to run their own code.

## Prepare the implementation

1. Read the shared model and calibration. Use the HARK notebook as an example of the calculations and outputs, and implement them in your toolkit.
2. Keep explanations close to the code and link to the model description rather than repeating it. Record numerical choices and explain any departures from the stated model.
3. Write the result tables from the computed solution and record the settings in `metadata.yml`.
4. Restart the kernel, run the notebook from beginning to end and save its outputs. Open a pull request adding `projects/<toolkit>/BST/`.

## Result tables

Resources and consumption are normalised by permanent income. Compute expectations
using the same shock approximation as the solver.

### Consumption

Write `results/cfunc.csv` with columns `m,c`, one row for each value

$$
m\in\{0.25,0.5,0.75,1,1.25,1.5,2,2.5,3,4,5,6,8,10,15,20\}.
$$

Plot consumption over $m\in[0,20]$ and check that it is increasing and
concave. The limiting MPCs below give the bounds
$\underline\kappa m\leq\mathrm{c}(m)\leq\overline\kappa m$.
Use the limiting value $\mathrm{c}(0)=0$ at the origin.

### Target, MPCs and shock moments

Write `results/scalars.csv` with columns `name,value`. Define the absolute
patience factor $\text{Þ}=(\mathsf{R}\beta)^{1/\gamma}$, where $\gamma$ is
relative risk aversion.

| Row name | Quantity |
| --- | --- |
| `m_target` | Target $\hat m$ solving the model's conditional expected resource-growth equation. |
| `mpc_at_target` | $[\mathrm{c}(\hat m+h)-\mathrm{c}(\hat m-h)]/(2h)$ with $h=0.01$. |
| `kappa_min` | $\underline\kappa=\max\{0,1-\text{Þ}/\mathsf{R}\}$. |
| `kappa_max` | $\overline\kappa=1-\wp^{1/\gamma}\text{Þ}/\mathsf{R}$. |
| `E_psi_inv` | $\mathbb{E}[\psi^{-1}]$ under the solver's shock approximation. |
| `E_psi_1mrho` | $\mathbb{E}[\psi^{1-\gamma}]$ under the solver's shock approximation. The existing CSV name uses `rho` for $\gamma$. |

Check that expected resource growth is positive at $\hat m-0.5$ and negative
at $\hat m+0.5$. The target differs from a balanced-growth pseudo-target and
from the mean of the stationary distribution.

### Conditions

Write `results/conditions.csv` with columns `name,holds`, using `true` or
`false`. Display the corresponding factors in the notebook.

| Row name | Condition | Factor, required to be below one |
| --- | --- | --- |
| `FVAC` | Finite value of autarky | $\beta\mathcal{G}^{1-\gamma}\mathbb{E}[\psi^{1-\gamma}]$ |
| `AIC` | Absolute impatience | $\text{Þ}$ |
| `RIC` | Return impatience | $\text{Þ}/\mathsf{R}$ |
| `WRIC` | Weak return impatience | $\wp^{1/\gamma}\text{Þ}/\mathsf{R}$ |
| `FHWC` | Finite human wealth | $\mathcal{G}/\mathsf{R}$ |
| `GIC` | Growth impatience | $\text{Þ}/\mathcal{G}$ |
| `GICMod` | Strong growth impatience | $(\text{Þ}/\mathcal{G})\mathbb{E}[\psi^{-1}]$ |

## Record authors and numerical settings

Use this example for `metadata.yml`, replacing the descriptions in angle
brackets. List the people who wrote the implementation in `authors`.

```yaml
toolkit: "<toolkit name>"
toolkit_version: "<version>"
authors:
  - name: "<implementation author>"
model: "BST"
date: "<YYYY-MM-DD>"
template_version: "0.1"
template_source: "https://github.com/QuantEcon/community-library/tree/main/models/buffer-stock"
software: "<language version and package versions>"
method: "<solution method and convergence criterion>"
discretisation: "<shock approximation, number of points, and zero-income treatment>"
grid: "<grid variables, bounds, number of points, and spacing>"
```

Describe settings in words if your method has no grid or does not discretise
the shocks. Retain the source-paper citations and CC-BY attribution, and
state the licence for your code.

## Comparing implementations

The [HARK results](../../projects/HARK/BST/results/) provide one comparison.
Its [numerical checks](../../projects/HARK/BST/checks/sensitivity.md) show
how grids, shock approximations and interpolation affect the results.
There is no separate reference implementation.

Version 0.1 uses the following provisional absolute differences to identify
results that need explanation when comparing implementations:

| Quantity | Difference requiring explanation |
| --- | --- |
| Consumption at any common resource value | Greater than 0.02 |
| Target resources | Greater than 0.02 |
| MPC at the target | Greater than 0.01 |
| Either limiting MPC | Greater than $10^{-6}$ |
| Any condition result | Disagreement |

The two shock moments help explain differences and have no separate
tolerance. Use each implementation's own shock approximation for the target.
These comparison conventions remain provisional. The library does not rank
toolkits or compare run times.

## Maintaining the model description

Edit `template.md` and build its PDF from `models/buffer-stock/`:

```sh
myst build --pdf
```

The build requires LaTeX and uses `../../templates/plain_latex_wide/`.
Edit implementation notebooks directly in their project directories. The
library website is maintained separately in `docs/`.
