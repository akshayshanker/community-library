# Buffer stock saving

A household saves to protect consumption against income risk. We solve the
infinite-horizon model in Carroll and Shanker's *Theoretical Foundations of
Buffer Stock Saving* and compare the consumption function and target level
of resources across toolkits.

The [PDF](template.pdf) gives a concise specification in three sections:
model theory, calibration and numerical concerns. The [notebook](template.ipynb)
adds exercises and result reporting for your solution; both are generated
from [one source](template.md). The current template is **version 0.1, a draft**;
its [provisional choices](#draft-decisions) are recorded below.

## What to submit

Add one directory named after your toolkit under `models/buffer-stock/`:

```text
<toolkit>/
    tutorial.ipynb
    metadata.yml
    results/
        cfunc.csv
        scalars.csv
        conditions.csv
```

| File | What to produce |
| --- | --- |
| `tutorial.ipynb` | Complete the template's code cells, retain the model statement, and explain your method and any differences in the author's notes. Save the figures and comparison output in the notebook. |
| `metadata.yml` | Name the toolkit and authors, record software versions and numerical settings, and identify the template version. An example appears below. |
| `results/cfunc.csv` | Report consumption at the 16 common values of normalised market resources. |
| `results/scalars.csv` | Report the target, the marginal propensity to consume at the target, the two limiting marginal propensities to consume, and two permanent-shock moments. |
| `results/conditions.csv` | Report whether each of the seven conditions in the lecture holds. |

Include supporting code and an environment file if the notebook needs them.
Use your toolkit's usual format, such as `requirements.txt`, `environment.yml`
or `Project.toml`, and state the installation and run commands at the start
of the notebook. A separate tutorial document or a second copy of the shared
reference computation is unnecessary.

## Complete the tutorial

1. Copy `template.ipynb` into your toolkit directory as `tutorial.ipynb`. Use the notebook kernel appropriate to your language and translate the supplied Python cells when needed. List the implementation authors while retaining credit to the template author, Akshay Shanker.
2. Complete the exercises in order using the stated model and calibration. Explain the numerical method, record the shock approximation and grids, and describe any departures from the model in the author's notes.
3. Write the three CSV files from your computed results and complete `metadata.yml`. Run the notebook from its own directory so that `results/` refers to the files alongside it. Use the comparison cell to report differences from the reference and explain differences beyond the stated tolerances.
4. Restart the kernel and run the completed notebook from beginning to end. Save its outputs and open a pull request adding your toolkit directory.

The result tables let readers compare the same economic objects even when
the toolkits use different notation or solution methods. A difference beyond
a tolerance should be reported and explained; it does not automatically
exclude a contribution. The library publishes no timings or toolkit ranking
in this first phase.

## Record authors and numerical settings

Copy the following example into `metadata.yml` and replace the descriptions
in angle brackets. The `authors` entries credit the people who wrote the
implementation; `template_author` credits the author of the shared lecture.
The template version identifies the model and reporting conventions you used.

```yaml
toolkit: "<toolkit name>"
toolkit_version: "<version>"
authors:
  - name: "<implementation author>"
date: "<YYYY-MM-DD>"
template_version: "0.1"
template_author: "Akshay Shanker"
template_source: "https://github.com/QuantEcon/community-library/tree/main/models/buffer-stock"
software: "<language version and package versions>"
method: "<solution method and convergence criterion>"
discretisation: "<shock approximation, number of points, and zero-income treatment>"
grid: "<grid variables, bounds, number of points, and spacing>"
```

Describe settings in words if your method has no grid or does not discretise
the shocks. Retain the source-paper citation and CC-BY attribution in the
notebook, and state the licence for your code.

## Write the result tables

Use the column and row names shown here. Replace every blank entry with a
computed number, or with `true` or `false` in `conditions.csv`. The names are
shared across submissions even when your toolkit uses other names internally.

### Consumption

The columns of `results/cfunc.csv` are `m` and `c`, both normalised by
permanent income. Include one row at each of the common resource values:

```csv
m,c
0.25,
0.5,
0.75,
1,
1.25,
1.5,
2,
2.5,
3,
4,
5,
6,
8,
10,
15,
20,
```

### Target, marginal propensities to consume and shock moments

The columns of `results/scalars.csv` are `name` and `value`:

```csv
name,value
m_target,
mpc_at_target,
kappa_min,
kappa_max,
E_psi_inv,
E_psi_1mrho,
```

The first two rows report the target resources and the marginal propensity
to consume there, measured by the central difference with step 0.01. The
next two rows report the limiting marginal propensities to consume at high
and low resources. The last two rows report the expected inverse permanent
shock and the expected permanent shock raised to one minus relative risk
aversion, under the shock approximation used to solve your model. Their
formulas and reference values appear in the lecture.

### Conditions

The columns of `results/conditions.csv` are `name` and `holds`. Enter `true`
when the named condition holds under your numerical settings and `false`
otherwise; the lecture defines each condition and its factor.

```csv
name,holds
FVAC,
AIC,
RIC,
WRIC,
FHWC,
GIC,
GICMod,
```

Report the computed factors in the notebook. The CSV records the seven
yes/no results, which all equal `true` in the reference computation.

## Draft decisions

Version 0.1 uses the comparison tolerances stated in the lecture. Confirming
those tolerances remains an open decision before the template can be
finalised, because different shock approximations change the reference
comparisons.

The target currently uses each implementation's own approximation to the
permanent-shock distribution. Whether all implementations should instead
use the continuous distribution for the target calculation also remains
open. The current choice gives a reference target of 1.3910; using the
continuous inverse-shock moment gives 1.3920. Use the stated version 0.1
choice when preparing a submission and identify that version in the metadata.

The initial draft was dated 11 September 2026. The current revision changes
the exposition and contribution instructions while retaining the model,
calibration and reported reference values.

## Maintaining the shared template

The lecture source is `template.md`; `template.ipynb` and `template.pdf` are
generated from it. From `models/buffer-stock/`, regenerate
the notebook with:

```sh
jupytext --to ipynb template.md
```

Use the one-way command so notebook generation does not rewrite the lecture
source. Build the PDF and a local HTML version with:

```sh
myst build --pdf
myst build --html
```

The PDF build requires LaTeX and uses the shared page layout in
`../../templates/plain_latex_wide/`. The `myst.yml` file configures the local
lecture build; the library website is maintained separately in `docs/`.

The [reference directory](reference/README.md) contains the computation,
recorded values, tests and sensitivity study. Those files support the shared
comparison values; toolkit authors need only the lecture and the submission
instructions to prepare their contribution.
