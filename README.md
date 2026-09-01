# Bayesian Inference in Hierarchical Modelling

Code and data for an MSc dissertation applying Bayesian hierarchical models to
household business profit from seven randomised trials of expanded microcredit
access.

MSc Data Science and Analytics, School of Mathematical Sciences,
University College Cork.

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

## The models

Seven specifications, each differing from a neighbour in one respect so that the
contribution of each modelling choice can be isolated.

| Label | Model | Question it answers |
|---|---|---|
| M0 | Complete pooling | Do the treatment effects differ across sites at all? |
| M1 | No pooling | Does linking the sites help? |
| M2 | Hierarchical on the effects only | Do the effects need pooling? |
| M3 | Hierarchical on baselines and effects | Do the baselines need it too? |
| M4 | Hierarchical, correlated | Does a site's baseline predict its effect? |
| M5 | M3 with pooled residual scales | Do the residual scales need pooling? |
| M7 | M3 with uniform priors | Do the conclusions depend on the prior? |

M0 and M1 are the two limiting cases of the hierarchical model, with the
between-site standard deviation forced to zero and to infinity respectively.
M3 is the specification reported as the main result.

The labelling skips M6, a Student t outcome model. It is not part of the set
reported here, because every other model in the comparison has a normal outcome.

---

## How to run

The analysis is split across two notebooks because the models were fitted on two
separate hosted notebook sessions running at the same time. Run them in this
order.

**Step 1 — `Second_Notebook.ipynb`**

Run every cell. Upload `microcredit_profit.csv` when prompted. It fits M4 and M7
and downloads `fits_worker.zip`. Keep that file.

**Step 2 — `BHIM_Microcredit_Main.ipynb`**

Run every cell. Upload `microcredit_profit.csv` when prompted, and upload
`fits_worker.zip` at Part 6b. It fits the remaining five models, imports the two
from step 1, and produces all the diagnostics, figures and tables.

Both notebooks are written for Google Colab. All required packages are installed
by the first cell. Fitting takes several hours in total; each model is written to
disk as it completes, so an interrupted session can be resumed by re-running the
fitting cell rather than starting again.

---

## Software

| Package | Role |
|---|---|
| PyMC | Model specification and sampling |
| ArviZ | Posterior summaries, diagnostics, model comparison |
| NumPy, pandas | Data handling and numerical work |
| Matplotlib | Figures |

**Sampler.** All models are fitted with the No-U-Turn Sampler. It is not
implemented in this repository: it is the sampler PyMC selects by default for
models whose parameters are all continuous, which is the case for every model
here, and no alternative step method is specified.

**Settings.** Two chains of 3,000 iterations per model, the first 2,000 of each
discarded as warm-up. The target acceptance rate is 0.99 for the hierarchical
models and 0.90 for M0 and M1. Section 4.9 of the dissertation records how these
were arrived at.

**Diagnostics implemented directly.** The split potential scale reduction factor
and the effective sample size are written from the formulas in Gelman et al.
(2013), Sections 11.4 and 11.5, rather than taken from a library. Both are
checked against the ArviZ implementations in Part 7 of the main notebook and both
sets of figures are reported.

---

## Output

The main notebook writes:

- `results.json` — every number quoted in the dissertation
- Nine CSV tables — data summary, verification, convergence, predictive checks,
  model comparison, estimates, leave-one-site-out, shrinkage, WAIC
- Seven figures in `figures/`

---

## Headline results

Under M3, the main specification:

| Quantity | Estimate | 95% interval |
|---|---|---|
| Average treatment effect | 6.74 | −4.62 to 23.72 |
| Between-site standard deviation | 11.61 | 1.53 to 34.93 |
| Predicted effect in a new country | 6.52 | −22.34 to 43.49 |

Meager (2019) reports an average effect of 6.81 with a 95 per cent interval from
−3.03 to 24.49.

Every model fails five of six posterior predictive checks. The binding failure is
the proportion of households recording exactly zero profit: no continuous density
can place probability mass on a single point. This is a property of the outcome
rather than of any one specification, and Chapter 6 of the dissertation discusses
the class of model that would address it.

---

## References

Gelman, A., Carlin, J. B., Stern, H. S., Dunson, D. B., Vehtari, A. and Rubin,
D. B. (2013). *Bayesian Data Analysis*, third edition. Chapman and Hall/CRC.

Meager, R. (2019). Understanding the average impact of microcredit expansions: a
Bayesian hierarchical analysis of seven randomized experiments. *American
Economic Journal: Applied Economics*, 11(1), pages 57-91.
