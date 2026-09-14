# Buffer stock saving with HARK

Open [tutorial.ipynb](tutorial.ipynb) for the runnable implementation.
The theory and calibration are in the [shared model description](../../../models/buffer-stock/template.md).
The notebook follows the numerical approach of the
[paper's HARK notebook](https://github.com/econ-ark/BufferStockTheory/blob/master/Code/Python/BufferStockTheory-Problems-and-Solutions-Source.ipynb).

From this directory, install the packages and open the notebook in Jupyter
or your editor, using the Python environment where they were installed:

```sh
python -m pip install -r requirements.txt
```

Restart the kernel and run all cells. The notebook solves the model, plots
consumption and expected resource growth, checks the numerical solution,
and writes `metadata.yml` and the three CSV files in `results/`.

| File or directory | Purpose |
| --- | --- |
| `tutorial.ipynb` | Implementation, plots and computed results. |
| `solve.py` | HARK parameters and functions used by the notebook to calculate and save results. |
| `requirements.txt` | Packages needed to run the notebook and tests. |
| `metadata.yml` | Software versions and numerical settings from the notebook run. |
| `results/` | Consumption, scalar and condition tables in the shared format. |
| `checks/` | Optional numerical tests, an independent EGM check and a sensitivity study. |

`python solve.py` also produces the three result tables, without the
notebook's plots or metadata. Both entry points use the same HARK solver.

The calculation uses HARK 0.17.1, seven positive values per income shock
plus the zero-income event, and 960 asset grid points between 0.001 and 20.
Consumption is linearly interpolated and iteration uses tolerance 1e-6.
The notebook computes shock moments from the discretised distribution and
uses a central difference with step 0.01 for the MPC at the target.

HARK's `mNrmTrg` is the individual target requested here; `mNrmStE` is the
balanced-growth pseudo-target. The solution object's `MPCmin` is a
finite-iteration slope, so the reported limiting MPCs use the paper's
closed forms. In the helper code, `rho` denotes the paper's risk aversion
parameter gamma, and `theta` denotes its full transitory shock xi.

To run the numerical tests or repeat the sensitivity study:

```sh
python -m pytest -q checks
python -m checks.sensitivity
```

The [saved sensitivity study](checks/sensitivity.md) compares asset grids,
shock approximations and MPC difference steps. `checks/expected.json`
preserves an earlier verified HARK calculation for regression tests; it is
not a second implementation or a required submission file.

See the [submission guide](../../../models/buffer-stock/README.md) for the
common result columns and how to add another toolkit.
