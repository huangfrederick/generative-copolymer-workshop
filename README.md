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

---

## For Contributors

Everything below this line is for instructors and developers working on the notebook content.

### Repository Structure

Notebooks are authored as plain Python scripts in [jupytext](https://jupytext.readthedocs.io/) percent format under `sources/`. Paired `.ipynb` files live in the top-level `day*/` directories for Colab compatibility. Always edit the `.py` files — diffs on `.ipynb` are collapsed on GitHub via `.gitattributes`.

```
day1/                              # Student-facing notebooks (.ipynb)
day2/
day3/
sources/                           # Notebook sources (.py, percent format)
├── day1/
├── day2/
└── day3/
```

### Jupytext Workflow

Regenerate a single notebook after editing its `.py` source:

```bash
python build_notebooks.py sources/day1/01_md_simulation.py
```

Rebuild all notebooks:

```bash
python build_notebooks.py
```

`build_notebooks.py` wraps `jupytext --to ipynb` and additionally resolves any
`# CDSE-INCLUDE: <file>` marker cells (used by Day 1 to splice in
`day1/cdse_lab.py` and the shared install cell) before conversion, then strips
the delivered notebook's jupytext pairing metadata.

> **`day{1,2,3}/*.ipynb` are build outputs.** Never hand-edit them or run
> `jupytext --sync` on them — edit the paired `.py` file under `sources/` and
> rerun `build_notebooks.py`. A notebook with `CDSE-INCLUDE` markers no longer
> matches its source line-for-line, so a sync would overwrite the spliced
> content (or the marker) with the wrong side.

### Local Development

```bash
pip install -r requirements.txt
pip install jupytext
```

### Verifying on Colab

Local execution is necessary but **not sufficient**. Two earlier test harnesses both passed
while the notebooks were broken for every student, because each one silently provided
something the Colab badge does not:

- `tools/execute_notebooks.py` replaces install cells with `pass` (see `INSTALL_MARKERS`) and
  runs papermill with `cwd` inside the repo. It therefore cannot see a broken install cell, and
  never exercises a missing `../data`.
- An earlier round of Colab testing used genuinely fresh CPU VMs, but each run began by
  extracting a `repo.tar.gz` into `/content/repo` and `chdir`-ing into `dayN` — handing the
  notebook both the repo and the working directory.

A real test reproduces the **badge entry conditions**: a bare VM containing only the `.ipynb`.

```bash
colab new -s check
colab upload -s check day1/01_md_simulation.ipynb /content/01.ipynb
# run cells through the live kernel from /content, never chdir-ing into a checkout
```

Three things to know:

- **`colab exec` defaults to a 30-second output timeout.** Long MD runs and the ~76 MB Zenodo
  fetch are silent for far longer, so pass `--timeout` explicitly or the run dies mid-notebook
  and looks like a notebook bug.
- **`condacolab.install()` restarts the kernel.** Run that cell on its own, then resume.
- **Check figures, not just exceptions.** A broken `<img>` fails silently — it does not raise.

Bugs this catches that nothing else does: a `%%` cell magic below line 1 (now also caught at
build time by `check_cell_magics`), a relative `../data` read before the bootstrap cell, a
`campaign/` import, an unflushed GSD writer read back in a later cell, and an `import` in the
same cell as the `conda install` that created the package.

Prose style is checked separately with `tools/style_check.py`, calibrated against the original
MATSE 219 lectures.

## License

This work is licensed under a [Creative Commons Attribution 4.0 International License](LICENSE).

Figures reproduced from published articles are listed in
[`sources/figures/papers/NOTICE.md`](sources/figures/papers/NOTICE.md); most are
excluded from the CC BY 4.0 grant above, except where that notice states
otherwise (e.g. figures the notice marks as CC BY 4.0 or reused under their own
open-access terms).
