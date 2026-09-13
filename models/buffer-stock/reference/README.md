# Reference values for the buffer stock template

Read the [recorded values](results/reference_values.json) or the
[consumption table](results/reference_cfunc.csv) to compare a result with the
buffer stock reference. To reproduce both files, run these commands from
`models/buffer-stock/reference/`:

```sh
python -m pip install -r requirements.txt
python solve.py
```

The solver prints the results and writes the two files in `results/`. The
three files at the top level are this guide, `solve.py` and `requirements.txt`;
the other material is grouped by purpose:

| Directory | What it contains |
| --- | --- |
| `results/` | The saved reference values with their numerical settings and software versions, and the consumption table. |
| `checks/` | Tests, an independent solver, and the sensitivity study supporting the reference values. |

The library maintains these files. Toolkit authors can prepare their own
tutorial using the [submission guide](../README.md#what-to-submit).

## What was computed, and with what

The reference solves the infinite-horizon problem of the template at the
baseline calibration with HARK (the `econ-ark` package), using the class
`IndShockConsumerType`, and follows the settings of the paper's own notebook
(`Code/Python/BufferStockTheory-Problems-and-Solutions-Source.ipynb`, cell 39
of the BufferStockTheory repository): twenty times HARK's default number of
asset grid points, linear interpolation, and an infinite horizon.

| Setting | Value |
| --- | --- |
| HARK version | 0.17.1 (Python 3.12.7, numpy 2.3.5, scipy 1.17.1) |
| Shock discretisation | 7 equally probable positive values for each of ψ and Θ; θ = 0 with probability ℘, otherwise Θ/(1 − ℘) |
| Asset grid | 960 points from 0.001 to 20, nested exponential spacing (nesting factor 3), plus the natural borrowing constraint at zero |
| Interpolation of c | linear |
| Horizon | infinite; backward iteration until the policy moves by less than 1e-6 |
| Borrowing constraint | natural only (no artificial constraint) |
| Mortality | none |

The following table maps the template's symbols to HARK's parameter names.

| Symbol | HARK name | Value |
| --- | --- | --- |
| 𝒢 | `PermGroFac` | 1.03 |
| 𝖱 | `Rfree` | 1.04 |
| β | `DiscFac` | 0.96 |
| ρ | `CRRA` | 2 |
| ℘ | `UnempPrb` (with `IncUnemp = 0`) | 0.005 |
| σ_ψ | `PermShkStd` | 0.1 |
| σ_θ | `TranShkStd` | 0.1 |

Two facts about HARK's output matter for reading the cross-checks in
[recorded values](results/reference_values.json). HARK's `mNrmTrg` is the template's target m̂, the
root of E[m′ | m] = m; its `mNrmStE` is the paper's pseudo-target, the root of
the balanced-growth condition, which the template does not report. The
`MPCmin` attribute of HARK's solution object is the slope after finitely
many backward steps (0.0404 at the baseline), not the limiting value; the
limiting MPCs printed in the template are the paper's closed forms, which
HARK also evaluates in its condition checks (`bilt["MPCmin"]`).

## Optional checks

To check the reference computation or repeat the sensitivity study, run these
commands from the same `reference/` directory:

```sh
python -m pytest -q checks
python -m checks.sensitivity
```

The tests in `checks/test_reference.py` compare the model with the paper's
formulas and the independent endogenous grid method in
`checks/independent_egm.py`. The sensitivity calculation writes
`checks/sensitivity.md` and `checks/sensitivity.json`. Read the
[saved study](checks/sensitivity.md) for the detailed comparisons.

## What the sensitivity study shows

The consumption function from HARK and from the independent solver, both
with the same 56 joint shock outcomes (seven permanent values and eight
transitory values, including zero income), agree to 6e-6 at every grid point up to
m = 15 and to 1.3e-4 at m = 20, the top of HARK's asset grid, where HARK's
extrapolation uses the finite-iteration slope in `MPCmin`; raising the grid top
to 40 reduces the gap at m = 20 to 1.4e-5. Halving or doubling the number
of asset points moves c(m) by at most 2.5e-5 and the target by 2e-5.

The shock discretisation dominates every other setting. Relative to 7
positive values per shock, 5 values move c(m) by up to 9.4e-3 and the target by 6.0e-3;
11 values by 7.4e-3 and 4.9e-3; 21 values by 1.3e-2 and 8.4e-3; 3 values by
3.5e-2 and 2.1e-2. The independent solver reproduces each of these
differences to four significant figures, so they are properties of the
equally probable discretisation, not of either implementation.

The marginal propensity to consume at the target computed as a central
difference with step 1e-2 is 0.2696 on every grid tried and in the
independent solver; with step 1e-4 it varies between 0.2692 and 0.2695
because the step then resolves the kinks of the piecewise-linear
interpolant. The template therefore defines the reported MPC by the step
1e-2 difference.
