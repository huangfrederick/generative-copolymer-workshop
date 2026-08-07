# Soft Matter Informatics & Inverse Design Workshop

**Duration:** 3 Days  
**Format:** Hybrid Synchronous/Asynchronous  
**Compute Platform:** Google Colab  
**Prerequisites:** Basic Python proficiency, general understanding of polymer physics

## Overview

This workshop bridges molecular dynamics simulation, machine learning, and inverse design for soft matter systems. Over three days, participants will:

1. **Generate** morphologies using coarse-grained MD (Kremer-Grest model via HOOMD-blue)
2. **Embed** high-dimensional structural features into interpretable latent spaces (PCA, UMAP)
3. **Design** novel copolymer sequences targeting specific morphologies using evolutionary optimization

## Getting Started

Each notebook is designed to run in Google Colab — click the "Open in Colab" badge at the top of any notebook to launch it, and the Python packages install in-notebook (no local *software* setup needed). Every notebook is standalone: none of them depend on having run an earlier one in the same session.

**Data note:** every data and model artifact a notebook needs is published in a **data bundle on Zenodo** ([DOI 10.5281/zenodo.21382942](https://doi.org/10.5281/zenodo.21382942), see `data/master64/README.md`) — the derived tables, `pca.joblib`, the KDE renders, the GSD trajectories, the exhaustive-library features, and the trained GRU surrogate. No manual download and **no repository checkout** is needed: a `colab_bootstrap` cell near the top of each notebook creates the `../data` / `../models` workspace, and notebooks 02–09 then call `ensure_bundle(...)` (defined in each day's `data_bootstrap.py`) to fetch, sha256-verify, and extract exactly the archives they need, on first use. The bundle manifest is embedded in `data_bootstrap.py`, so verification works with nothing on disk.

> **Why this matters:** the Colab badge opens *only* the `.ipynb` into a bare VM — no repo comes with it. Notebooks must therefore obtain their own data and helper code. Anything that assumes a checkout (a relative `../data` read before the bootstrap, or a `campaign/` import) will pass local testing and fail for every student. `build_notebooks.py` enforces the cell-magic half of this; see "Verifying on Colab" below for the rest.

**First time?** Start with `day1/01_md_simulation.ipynb`.

## Schedule

| Day | Focus | Morning Sync | Async Compute | Afternoon Sync |
|-----|-------|-------------|---------------|----------------|
| 1 | **Representation** | MD Basics & Kremer-Grest | Running "The Virtual Lab" | CNNs & Feature Extraction |
| 2 | **Embedding** | PCA/UMAP & Latent Space | Mapping to Master Dataset | RNNs & Forward Prediction |
| 3 | **Inverse Design** | HTS vs. Evolutionary Opt. | The Design Challenge | ExAI & Capstone Review |

## Session Protocol

| Session | Time (ET) | Duration | Description |
|---------|-----------|----------|-------------|
| Morning Sync | 10:00–11:30 | 90 min | Conceptual lectures and live demos |
| Async Compute | 11:30–14:00 | 120 min | Independent lab work and simulation |
| Afternoon Sync | 14:00–15:30 | 90 min | Advanced topics and integration |
| Office Hours | 15:30–16:30 | 60 min | Open Q&A and troubleshooting |

## Notebooks

| # | Notebook | Session |
|---|----------|---------|
| 1 | `01_md_simulation` | Day 1 Morning: HOOMD-blue & Kremer-Grest |
| 2 | `02_virtual_lab` | Day 1 Async: Run your own simulation |
| 3 | `03_feature_extraction` | Day 1 Afternoon: CNN feature extraction |
| 4 | `04_embedding` | Day 2 Morning: PCA & UMAP |
| 5 | `05_mapping` | Day 2 Async: Map to master dataset |
| 6 | `06_forward_prediction` | Day 2 Afternoon: RNN sequence→structure |
| 7 | `07_inverse_design` | Day 3 Morning: Evolutionary optimization |
| 8 | `08_design_challenge` | Day 3 Async: The Design Challenge |
| 9 | `09_explainability` | Day 3 Afternoon: ExAI & capstone |
