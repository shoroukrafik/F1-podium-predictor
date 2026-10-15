# Formula 1 Podium Prediction — Milestone 1

Predict, for every driver in every race, whether they finish on the podium (top 3), using only
information available **after qualifying and before the race starts**.

- **Notebook:** `f1_podium_prediction.ipynb` — audit, cleaning, EDA, data-engineering questions Q1–Q3,
  leakage-safe feature engineering, time-based split, preprocessing experiments, models,
  feature-group ablation, explainability (permutation importance, SHAP, LIME) and an inference function.
- **Kaggle notebook:** _link to be added_
- **Figures:** `figures/` (all plots used in the report)

## Team
_to be added_

## Data
[Formula 1 World Championship (1950–2024)](https://www.kaggle.com/datasets/rohanrao/formula-1-world-championship-1950-2020)
by rohanrao. The CSVs are not stored in this repository. To run locally, download them into a folder
named `data/` (or `dataset f1/`) next to the notebook; on Kaggle, add the dataset as an input.

## Run locally
```bash
python -m venv .venv
.venv/bin/pip install -r requirements.txt
```
Then open the notebook and choose Run All.

## Results (test seasons 2022–2024)
| Model | PR-AUC | ROC-AUC | F1 |
|---|---|---|---|
| Grid rule (top 3) | 0.647 | 0.914 | 0.649 |
| Logistic regression (final) | 0.722 | 0.938 | 0.681 |
| Random forest | 0.720 | 0.941 | 0.677 |
| Gradient boosting | 0.705 | 0.939 | 0.677 |
| Shallow neural network | 0.725 | 0.937 | 0.686 |
