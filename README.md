# Bayesian Inference in Hierarchical Modelling

Code and data for applying Bayesian hierarchical models to
household business profit from seven randomised trials of expanded microcredit
access.

---

## What this repository contains

| File | Description |
|---|---|
| `BHIM_Microcredit_Main.ipynb` | Main analysis. Fits five models, imports the other two, and runs all model criticism and results. |
| `Second_Notebook.ipynb` | Fits the two most expensive models and exports them for the main notebook. |
| `microcredit_profit.csv` | The dataset: 35,303 households across seven study sites. |

---

## The data

One row per household, with four columns.

| Column | Meaning |
|---|---|
| `site` | Study site: Mexico, Mongolia, Bosnia, India, Morocco, Philippines, Ethiopia |
| `site_idx` | Integer index of the site, 0 to 6 |
| `treat` | 1 if assigned to the treatment arm, 0 if control |
| `y` | Household business profit, USD PPP per fortnight |

No observations are removed and no values are transformed. A majority of
households record a profit of exactly zero, and roughly a tenth record a negative
profit, so the outcome is neither strictly positive nor continuous.

The file is a processed extract of the household data from the seven published
trials, as used in Meager (2019). Section 4.4 of the dissertation verifies it
against the published within-site residual standard deviations; the largest
deviation across the seven sites is 0.34 per cent.

---
