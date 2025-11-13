# Nozzle-perf-estimator-demo
> Requires **Python 3.10+**. Pure-Python core (no runtime deps).  
> For tests, see `requirements.txt` (e.g., `pytest`, `pytest-cov`). For plotting, install `matplotlib`.

Minimal estimator of ideal (isentropic) rocket nozzle performance:
- Exit state: **Me, Pe, Te**, exit velocity **Ve**
- Thrust coefficient **CF**, specific impulse **Isp**, thrust per throat area **F/At**
- Ready-to-run scripts: batch cases, Pc×ε sweep, and a simple plot

---

## Repository structure

```text
nozzle-perf-estimator-demo/
├─ src/
│  ├─ __init__.py
│  ├─ thermo.py                # isentropic helpers (area–Mach, pressure/temp ratios)
│  ├─ nozzle.py                # ideal nozzle performance: Me, Pe, Te, Ve, CF, Isp, F/At
│  ├─ run_sweep.py             # Pc × ε sweep → writes nozzle_sweep_results.csv (repo root)
│  ├─ run_cases.py             # batch run for data/sample_cases.csv → *_results.csv (in data/)
│  └─ plot_cf_isp_vs_eps.py    # plot CF & Isp vs ε → figures/cf_isp_vs_eps.png
├─ data/
│  └─ sample_cases.csv         # input cases (Pc_bar, epsilon, Tc_K, Pa_Pa, gamma, Mw_kg_per_mol)
├─ tests/
│  ├─ __init__.py
│  ├─ test_thermo.py           # area–Mach monotonicity; Pe/Pc bounds
│  └─ test_nozzle.py           # plausibility checks; ε-effect on Me and CF
├─ docs/
│  └─ ENGINEERING_NOTE.md      # assumptions, equations, sanity checks, limitations
├─ figures/
│  └─ .gitkeep                 # keep the folder in VCS; plots are generated
├─ CHANGELOG.md
├─ README.md
├─ requirements.txt            # optional: pytest / pytest-cov for tests
├─ pyproject.toml              # code style: black/ruff config
├─ .editorconfig               # editor defaults
├─ .pre-commit-config.yaml     # optional hooks (black/ruff)
└─ .gitignore


Quick start

# (optional) test tooling
pip install -r requirements.txt

# Sanity check: compute one case
python - << 'PY'
from src.nozzle import NozzleInputs, performance
out = performance(NozzleInputs(Pc=60e5, Tc=3500.0, Pa=101325.0, gamma=1.22, Mw=0.022, epsilon=10.0))
print(out)
PY


Usage / Scripts

# 1) Compute results for the sample input cases
python src/run_cases.py
# -> data/sample_cases_results.csv

# 2) Sweep over Pc × ε (writes to repo root)
python src/run_sweep.py
# -> nozzle_sweep_results.csv

# 3) Plot CF and Isp vs ε (requires matplotlib)
python -m pip install matplotlib
python src/plot_cf_isp_vs_eps.py
# -> figures/cf_isp_vs_eps.png


Generated artifacts

data/sample_cases_results.csv — created by src/run_cases.py

nozzle_sweep_results.csv — created by src/run_sweep.py (repo root; ignored by VCS)

figures/cf_isp_vs_eps.png — created by src/plot_cf_isp_vs_eps.py

.gitignore excludes generated files but keeps figures/.gitkeep.


Tests

python -m unittest discover -s tests

Covers:

test_thermo.py — area–Mach monotonicity, Pe/Pc bounds

test_nozzle.py — plausible ranges; ε-effect on Me and CF


Engineering note

See docs/ENGINEERING_NOTE.md for assumptions, equations, sanity checks, and limitations.


Extending

Add an over/under-expansion check (compare Pe to Pa) and a separation warning flag.

Support CEA-based property tables so gamma and molar mass vary with temperature (γ(T), Mw(T)).

Introduce simple loss models / nozzle efficiency factors.

Generate plots of CF and Isp versus ε (area ratio) and Pc (chamber pressure).


Console output examples

run_cases.py

Saved: /path/to/repo/data/sample_cases_results.csv


run_sweep.py

=== Nozzle sweep done ===
Saved: /path/to/repo/nozzle_sweep_results.csv


plot_cf_isp_vs_eps.py

Saved: /path/to/repo/figures/cf_isp_vs_eps.png


One-liner check

NozzleOutputs(Me=2.48, Pe=142000.0, Te=..., Ve=..., CF=..., Isp=..., F_per_At=...)


Dev setup (optional)

This repo uses black and ruff (configured in pyproject.toml) and .editorconfig for editor defaults.

pip install black ruff
# optional:
pip install pre-commit && pre-commit install
black .
ruff check . --fix


## Changelog
See [CHANGELOG.md](./CHANGELOG.md) for release notes.


License

MIT/Apache-2.0
