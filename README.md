# MAT-Sum: Semantic Abstraction for Privacy-Aware Synthetic Mobility Generation

Code, intermediate data and result files for the paper
*Semantic Abstraction for Privacy-Aware Synthetic Mobility Generation* (ACM TIST, under review)
and its Supplementary Material.

Every table and figure of the paper and of the supplement is produced by one of the scripts listed below.
Result files are included, so tables and figures can be checked without re-running the experiments.

## Data

| Dataset | Source | In this repository |
|---|---|---|
| Paris-OSM GPS points | Zenodo record 15658129 | processed files in `data/paris/` (the raw points file is not included, download it to rebuild from scratch) |
| GeoLife (Beijing) | Microsoft Research GeoLife 1.3 | processed files in `data/geolife/` |
| OpenStreetMap extracts | Geofabrik (Ile-de-France, Beijing) | Beijing extract in `data/geolife/`; the Ile-de-France `.pbf` is not included |

The OSM label taxonomy used by all scripts is in `data/labeled_OSM_category.csv` and `data/pois_categories_OSM.txt`.

## Setup

```bash
python3.12 -m venv ~/matsum-venv
~/matsum-venv/bin/pip install -r requirements.txt
export PYTHONHASHSEED=0          # fixes set ordering, needed for exact reproduction
export MATSUM_OUT=$PWD/data/paris   # data directory read and written by the scripts
export MATSUM_FIG=$PWD/figures      # figures
```

Other variables, used only by some scripts:

* `MATSUM_INPUT`: path of the raw Paris points file (`matsum_prepare.py`).
* `MATSUM_PBF`: path of the OSM `.pbf` extract (`matsum_prepare.py`, map preparation only).
* `GEO_OUT`: GeoLife data directory (`data/geolife`), needed by `geolife_e1.py` and other GeoLife scripts.
* `MATSUM_DS`: dataset label for `val_extra_metrics.py` (`Paris` or `GeoLife`).

Keep the virtual environment outside any cloud-synced folder.
Scripts that run a neural generator (LSTM) need `torch`. They run on CPU.
The largest runs (`val_rq3_extended.py`, `paris_release_size.py`) take hours.

## Core modules

| File | Role |
|---|---|
| `matsum_prepare.py` | builds tiles, OSM-enriched tiles and trajectories from the raw points |
| `matsum_summarize.py` | semantic locations: top-m aspects, k-support, summarization rate |
| `matsum_synth.py` | order-1 Markov generator, semantic vocabulary, sampling of visits |
| `matsum_muitas.py` | MUITAS similarity used for near-copy detection |

## Paper element → script → output

| Paper element | Script | Main output |
|---|---|---|
| Table 2 (dataset statistics) | `geolife_e0_semantic.py`, `matsum_prepare.py`, `matsum_summarize.py` | `data/geolife/geolife_E0.csv`, `results/paris/summarization_rate.csv` |
| Fig. 2, Table S2 (m, k sweep) | `val_paris_eval.py` | `results/paris/val_sweep_mk.csv`, `figures/fig_mk_sweep.png` |
| Table 3, Fig. 3, Table S3 | `val_abstraction_ci.py` | `results/paris/val_abstraction_ci.csv`, `figures/fig_abstraction_ci.png` |
| Table S9 | `val_abstraction_diagnostics.py` | `data/paris/val_abstraction_diagnostics.csv` |
| Fig. S10 | `val_abstraction_budget.py`, `replot_abstraction_budget.py` | `data/paris/val_abstraction_budget.csv`, `figures/fig_abstraction_budget_semantic.png` |
| Table S4 | `val_abstraction_smoothing.py` | `data/paris/val_abstraction_smoothing*.csv` |
| Table S10, Tables S11, S12 | `val_abstraction_ngrams.py`, `make_supp_tex.py` | `data/paris/val_abstraction_ngrams*.csv`, `paper_tables/` |
| Mixed-use analysis | `val_mixeduse_downstream.py` | `results/paris/val_mixeduse_downstream.csv` |
| Table 4, Fig. 4 | `geolife_e1.py` | `figures/fig_geolife_e1.png` |
| Fig. 5, Tables S5, S6 (RQ2) | `val_rq2.py` | `results/paris/val_rq2_*.csv`, `figures/fig_rq2.png` |
| Table 5, Fig. S2 | `geolife_e2.py` | `figures/fig_geolife_e2.png` |
| Table 6, Fig. 6, Table S7 (RQ3) | `val_rq3.py` | `results/paris/val_rq3_*.csv`, `figures/fig_rq3.png` |
| Table 7, Tables S13, S14 (order-2 Markov) | `val_rq3_order2.py` | `data/paris/val_rq3_order2_*.csv` |
| Table S15, Fig. S11 (Pareto) | `val_rq3_pareto.py` | `data/paris/val_rq3_pareto.csv` |
| Table S16 (hypervolume) | `val_rq3_hypervolume.py` | `data/paris/val_rq3_hypervolume_*.csv` |
| Table 8, Tables S18 to S20 (n-grams up to order 5, LSTM capacity scale) | `val_rq3_extended.py`, `val_rq3_extended_summary.py`, `make_rq3_ext_tex.py` | `data/paris/val_rq3_ext_*.csv`, `paper_tables/rq3_extended.tex` |
| Table 9, Fig. S3 | `geolife_e3.py` | `figures/geolife_e3.png` |
| Table 10, Table S17 (query workload utility) | `paris_workload_utility.py` | `data/paris/paris_workload_utility_*.csv` |
| Table 11, Fig. 7 (TSTR) | `paris_tstr.py` | `results/paris/paris_tstr_*.csv`, `figures/paris_tstr.png` |
| Fig. 8, Fig. S4 | `paris_geoviz.py` | `figures/fig_paris_geoviz*.png` |
| Table 12, Fig. 9 (time of day) | `paris_time_of_day_ci.py` | `data/paris/paris_time_of_day_ci_*.csv`, `figures/fig_time_of_day_demand_ci.png` |
| Fig. 10 (Paris disclosure) | `paris_disclosure.py`, `replot_disclosure.py` | `results/paris/paris_disclosure.csv`, `figures/Paris_disclosure.png` |
| Fig. 11 (GeoLife disclosure) | `geolife_disclosure.py` | `figures/GeoLife_disclosure.png` |
| Table 13, Fig. S6 (membership inference) | `paris_mia.py`, `geolife_mia.py`, `paris_shadow_mia.py` | `results/paris/paris_mia_*.csv`, `data/paris/paris_shadow_mia_*.csv` |
| Table 14, Fig. S7 (unicity) | `unicity.py` | `results/paris/unicity_paris.csv`, `figures/unicity_*.png` |
| Section S9.3 (edit-based disclosure) | `paris_edit_disclosure.py` | `data/paris/paris_edit_disclosure_*.csv` |
| Table 15, Figs. S8, S9 (extra metrics) | `val_extra_metrics.py` | figures `fig_extra_metrics_*.png` (result CSV is regenerated by the script) |
| Fig. S5 (release size, Paris) | `paris_release_size.py` | `data/paris/paris_release_size_*.csv`, `figures/fig_release_size.png` |
| Table S8 (release size, GeoLife) | `geolife_release_size.py` | `data/geolife/geolife_release_size*.csv` |
| Table S1, Fig. S1 (GeoLife audit) | `geolife_retention.py`, `geolife_audit.py`, `geolife_points.py`, `geolife_build_cache.py` | `figures/fig_geolife_diag.png` |

Table numbers follow the submitted manuscript and supplement.
`paris_time_of_day.py` reproduces the first version of the time-of-day analysis; the paper uses `paris_time_of_day_ci.py`.
`make_supp_tex.py` and `make_rq3_ext_tex.py` write the LaTeX tables in `paper_tables/` from the result CSVs.

## Reproducibility notes

* Replicates use fixed seeds. Run with `PYTHONHASHSEED=0`.
* The order-1 Markov generator in `val_rq3_extended.py` orders its states by sorting, so its runs are exactly repeatable.
  The original order-1 numbers in `val_rq3.py` depend on Python set order and match up to this ordering.
* `val_rq3_extended.py` accepts `--models`, `--tag`, `--reps`, `--ns`, `--resume`, `--start`, `--end` to split a long run.
  The three LSTM capacity runs are stored as `val_rq3_ext_{h128,h256,h256b,l2}_raw.csv`; the summary script removes duplicate replicates.
* The shadow-model membership inference and the release-size runs are stochastic and long. Their stored outputs are the reported ones.

## Data release policy

The per-tile visitation-frequency tables of the real data are not released. Only the semantic state sequences and aggregate results are.

## License

To be added by the authors.
