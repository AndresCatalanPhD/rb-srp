# Routing and basing maritime patrol aircraft for fisheries surveillance around marine protected areas

Code and results for the article

> Catalán, A. H., Canut-de-Bon, D. A., Piñeiro, M. J., and González, E. *Routing and basing maritime patrol aircraft for fisheries surveillance around marine protected areas: an out-of-sample evaluation with vessel-tracking data.* Submitted to *Naval Research Logistics*.

The repository reproduces the whole study from public data: the gridding of 22 Chilean marine protected sites and their surrounding rings, the risk model of apparent fishing effort, the monthly routing of a heterogeneous multi-base fleet of patrol aircraft (the Risk-Based Surveillance Routing Problem), the linear-programming upper bound and the exact benchmarks, the out-of-sample backtest over 48 test months, the enumeration of the 336 deployments of the fleet, the validation with daily vessel-level data and satellite radar detections, and every table and figure of the article.

## Contents

```
notebooks/
  01_fetch_panel_gfw_wdpa.ipynb    WDPA polygons -> cell grid; monthly apparent fishing effort 2019-2025 (GFW)
  02_fetch_daily_sar.ipynb         daily effort by vessel 2021-2025 and SAR detections 2019-2025 (GFW), for validation
  03_model_routing_basing.ipynb    risk model, routing, bounds, backtest, basing, robustness; all tables and figures
results/
  results.json, SUMMARY.txt        every number reported in the article
  tables/                          tables of the article (CSV and LaTeX) and monthly backtest outputs
  figures/                         figures of the article (PDF and PNG)
  validation/                      summary checks of the daily download against the monthly panel
requirements.txt                   packages, for a run outside Colab
CITATION.cff, .zenodo.json         citation and archive metadata
```

The notebooks are written for Google Colab; their comments are in Spanish.

## Data

All inputs are public, but they are **not redistributed here**: the notebooks download them.

| Source | What | Version used in the article |
|---|---|---|
| Global Fishing Watch, 4Wings API | Apparent fishing effort at 0.1°, monthly and daily, by gear and vessel | `public-global-fishing-effort:v4.0`; monthly panel retrieved on 7 September 2026, daily data in November 2026 |
| Global Fishing Watch, 4Wings API | Sentinel-1 SAR vessel detections, matched and unmatched to AIS | `public-global-sar-presence:latest`, retrieved in November 2026 |
| World Database on Protected Areas (Protected Planet) | Protected-area polygons | September 2026 release |
| Natural Earth | Coastlines and land polygons, 1:10m | current |

**Important: dataset version.** On 21 October 2026 Global Fishing Watch released version 5 of the effort product, with a new classifier that records materially fewer fishing hours, and `latest` now points to it. The article uses version 4, which the notebooks request explicitly (`public-global-fishing-effort:v4.0`). Requesting `latest` gives different numbers.

## How to reproduce

Requirements: a Google account with Google Drive, a free Global Fishing Watch API token (https://globalfishingwatch.org/our-apis/), and, optionally, a Gurobi license.

1. **Download the WDPA.** Download the September 2026 release of the WDPA in shapefile format from https://www.protectedplanet.net and put the three zip files in `MyDrive/wdpa/`.
2. **Notebook 01** (`01_fetch_panel_gfw_wdpa.ipynb`). Open it in Colab (CPU runtime), choose *Run all*, authorize Drive and paste the GFW token when asked. It writes `cells.csv` and `monthly_sparse.parquet` to `MyDrive/acanav_paper1/`.
3. **Notebook 02** (`02_fetch_daily_sar.ipynb`). *Run all*. It downloads the daily vessel-level effort and the SAR detections to `MyDrive/acanav_paper1/addon/`. It makes several thousand small requests and takes several hours; it can be resumed by running it again.
4. **Notebook 03** (`03_model_routing_basing.ipynb`). *Run all* on a CPU runtime. The full run takes four to five hours. Every stage caches its results in Drive, so an interrupted session resumes where it stopped. At the end it writes `results_v3/` and downloads `results_v3.zip`, whose contents are those of `results/` in this repository.

`FAST_MODE = True` in the configuration cell of notebook 03 runs a reduced version in about an hour, for testing.

**Gurobi (optional).** Only stage L2 (exact solution of single sorties by branch and cut) uses Gurobi. Without a license, `gurobipy` solves candidate lists of 40 and 60 blocks; with a full license it also solves the full list of 300. The notebook reads a WLS license from Colab Secrets (`WLSACCESSID`, `WLSSECRET`, `LICENSEID`) or a `gurobi.lic` file in Drive. Never commit a license file.

## Where each result is

| Article | File in `results/` |
|---|---|
| Study area and effort maps | `figures/F2_map` |
| Effort by distance to the boundary | `figures/F3_boundary_profile`, `tables/boundary_by_region.csv`, `tables/boundary_by_site.csv` |
| Table of MPAs; effort by sector | `tables/T1_mpas.tex`, `tables/sectors.csv` |
| Risk model performance; feature attributions | `tables/T2_risk_metrics.tex`, `tables/risk_metrics_by_year.csv`, `figures/F4b_shap` |
| Single-sortie benchmark | `tables/T8_sortie_benchmark.csv`, `tables/sortie_benchmark.csv` |
| Exact benchmarks | `tables/T15_exact.csv`, `tables/exact_instances.csv`, `tables/exact_sortie_candidates.csv` |
| Linear-programming bound | `tables/T7_lp_bound.csv`, `tables/T14_lp_extended.csv`, `tables/lp_bound_monthly.csv` |
| Planning methods | `tables/T3_methods.csv`, `tables/backtest_monthly.csv`, `figures/F5_detection_effort` |
| Alternative predictors | `tables/T13_predictors.csv`, `tables/T13a_predictors_cell.csv`, `tables/T13b_predictors_block.csv` |
| Basing scenarios | `tables/T4_scenarios.csv` |
| Enumeration of deployments | `tables/basing_enumeration.csv`, `tables/T9_basing_top10.csv`, `figures/F6_basing` |
| Example month | `figures/F7_example_month` |
| Sensitivity analysis | `tables/T6_sensitivity.csv`, `tables/sensitivity_monthly.csv` |
| Jurisdiction and narrower rings | `tables/T12_ring_jurisdiction.csv` |
| Daily validation | `tables/T16_dated.csv`, `tables/T16_dated_comparisons.csv`, `tables/dated_backtest_monthly.csv` |
| Randomized plans | `tables/T17_randomization.csv` |
| Reachability ceiling | `tables/backtest_by_year.csv` |

## Licenses

- Code (`notebooks/`): MIT License, see `LICENSE`.
- Results (`results/`): CC BY-NC 4.0, because they derive from Global Fishing Watch data; see `results/LICENSE-results.md`. Powered by Global Fishing Watch.
- Users of the notebooks must comply with the terms of Global Fishing Watch (non-commercial use, CC BY-NC 4.0) and of the WDPA.

## Note on operational use

Aircraft parameters (speeds, endurance, operating radii, sweep widths) and base capacities are planning assumptions of the authors drawn from open sources; they are not operational data of the Chilean Navy. The routes produced by the notebooks are sequences of patrol blocks for research purposes and are not flight plans.

## Citation

Please cite the article and the archived version of this repository (see `CITATION.cff`).
