# STAT 248 Final Project — NBA schedule load & team performance

**Author:** Chenfei Peng  

Professional basketball schedules pack **back-to-backs** and **short-turnaround** nights into dense road trips—conditions that resemble travel-fatigue experiments in sports science. This project asks whether, at the **team-game level**, unusually tight rest predicts worse performance **after controlling for opponent schedule, venue, momentum (lagged outcomes), and season**, and whether those associations show up comparably for **margins**, **effective field-goal shooting**, **turnovers**, and in **predictive** out-of-sample tests.

Using three regular seasons (2022–23–2024–25) of NBA team box scores merged into a chronologically ordered panel, the analysis walks through **(1)** cluster-robust lagged regressions, **(2)** compact ARIMAX-style models fit **within each team–season** to respect the time-series structure, and **(3)** forward-expanding seasonal cross-validation that trains only on past league years before scoring the next. All code, the frozen input CSV, tables, and figures live in this repo so the entire pipeline can be rerun from the notebook.

## Repository layout

| Folder | Contents |
|--------|----------|
| `notebooks/` | **`stat248_final_report.ipynb`** — run this top-to-bottom (narrative + code + diagnostics + figures). |
| `scripts/` | Python utilities: data build, lagged regression, ARIMAX, rolling CV, shared plotting, CLI runners. |
| `data/` | `nba_team_game_panel_stat248.csv` — team-game panel (2022–23 through 2024–25 regular season) used in the report. |
| `results/` | Exported CSVs and PNGs produced by the analysis (also regenerated when you run the notebook). |

## Environment (reproducibility)

- Python **3.10+** recommended.
- Install dependencies:

```bash
pip install -r requirements.txt
```

- **Rebuild from NBA.com (optional):** the panel was built with the `nba-api` package. Install with `pip install nba-api` (PyPI distribution of [swar/nba_api](https://github.com/swar/nba_api)). Then in the notebook set `REBUILD_PANEL = True`, or run:

```bash
export PYTHONPATH="/path/to/empty/or/nba-api/src:$PYTHONPATH"   # only if developing from source
python scripts/build_nba_team_game_dataset.py \
  --seasons 2022-23 2023-24 2024-25 --rolling-window 5 \
  -o data/nba_team_game_panel_stat248.csv
```

(If `nba-api` is installed normally, omit `PYTHONPATH`; the notebook sets it when rebuilding from repo-style layouts.)

- **Notebook kernel:** open the repo root (`248-final-project/`) in Jupyter / VS Code / Cursor so `Path.cwd()` resolves correctly in the first cell.

## Quick checks (optional CLIs)

```bash
python scripts/run_stat248_method2_arimax.py --maxiter 80
python scripts/run_stat248_method3_cv.py
```

## Methodologies

- **Method 1:** clustered OLS with lagged outcome; residuals + Breusch–Pagan / Durbin–Watson in the notebook.  
- **Method 2:** `SARIMAX(1,0,0)` with exogenous regressors **per team–season stratum** + BIC vs home-only baseline; pooled diagnostics figure for the best-BIC streak.  
- **Method 3:** forward-expanding **season** holdouts; RMSE/MAE for models with vs without fatigue covariates.  
- **`results/`** mirrors slide-ready exports; rerunning the report overwrites consistent filenames.
