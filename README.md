# Formula 1 Podium Prediction Companion — Milestone 1

Predict whether a driver finishes on the podium (`positionOrder <= 3`) using only information available
**after qualifying and before the race starts**, from the Ergast / Kaggle Formula 1 World Championship dataset (1950–2024, 14 tables).

## Contents

| File | What it is |
|---|---|
| `F1_Podium_Prediction_Milestone1.ipynb` | The single run-all notebook: audit & cleaning, data-engineering questions, features, models, experiments, XAI, inference function |
| `F1_Milestone1_Report.pdf` | Analytical report (answers to the three questions with figures, features and their pre-race validity, results, limitations) |
| `requirements.txt` | Python packages (all pre-installed on Kaggle) |

## How to run

**On Kaggle (recommended):** create a notebook, attach the dataset
[`rohanrao/formula-1-world-championship-1950-2020`](https://www.kaggle.com/datasets/rohanrao/formula-1-world-championship-1950-2020),
upload the notebook and choose **Run All** (≈ 3–5 minutes on CPU). Outputs are written to `/kaggle/working`.

**Locally:**
```bash
pip install -r requirements.txt
export F1_DATA_DIR=/path/to/the/14/csv/files   # or put the CSVs in ./data
jupyter nbconvert --to notebook --execute F1_Podium_Prediction_Milestone1.ipynb
```
Outputs go to `./outputs`.

## Notebook structure

| Part | Sections | Content |
|---|---|---|
| 1 Data cleaning & auditing | §1–8 | Raw EDA, `\N` → NaN, semantic typing, duplicate (raceId, driverId) keep-rule, key checks, EDA after cleaning, 2003–2024 base table, raw-file hash proof |
| 2 Data-engineering questions | §9–12 | Q1 front-row conversion by circuit (pre-hybrid vs hybrid, grid vs qualifying); Q2 home advantage controlling for grid; Q3 mechanical retirements by constructor |
| 3 Feature selection & engineering | §13–16 | Leakage-safe form / standings / qualifying features with leakage tests, time-based split, feature evidence, preprocessing-order experiments |
| 4 Predictive modeling | §17–19 | Rule baseline, logistic regression (baseline), random forest, gradient boosting, shallow neural network; all metrics on train / validation / test |
| 5 Experiments | §20–21 | Feature-group ablation; preprocessing experiments with the selected model |
| 6 Explainability | §22–25 | Permutation importance (feature + group), SHAP global and dependence, SHAP waterfall + LIME for single drivers |
| 7 Inference | §26–27 | `predict_podium(record)` on a complete and on partially-missing records; requirement checklist |

## Key results (test seasons 2022–2024)

| Model | PR-AUC | ROC-AUC | F1 |
|---|---|---|---|
| Rule: grid ≤ 3 | 0.647 | 0.914 | 0.649 |
| Logistic regression (baseline) | 0.702 | 0.936 | 0.680 |
| **Random forest (selected on validation)** | **0.688** | **0.938** | **0.673** |
| Gradient boosting | 0.676 | 0.932 | 0.674 |
| Shallow neural network | 0.677 | 0.926 | 0.653 |

Random PR-AUC ≈ 0.15. The selected model and the baseline are statistically indistinguishable on test; starting position
(grid + qualifying) dominates every importance measure. Explanations are associations, not causal effects.
