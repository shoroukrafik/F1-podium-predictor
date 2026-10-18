# Analytical Report: Formula 1 Podium Prediction

## 1. Objective
The task is to predict whether a driver finishes on the podium in a given race using only information available after qualifying and before the race starts. This means the modeling table must be built without post-race leakage and the evaluation must respect the temporal nature of the sport.

## 2. Data Engineering Questions

### Q1. Which circuits convert a front-row start into a podium most often, and does that ranking change after the 2014 hybrid era?
The notebook shows that front-row conversion varies by circuit and is not uniform across eras. The ranking changes after 2014, which is consistent with the sport’s evolution in car performance, strategy, and track characteristics. The analysis distinguishes qualifying position from grid position, because penalties can move a driver away from their qualifying result.

Suggested figure: `figures/q1_front_row_conversion.png`

### Q2. Do drivers podium more often at home after controlling for starting position?
The notebook indicates that the raw home-race effect is mostly explained by starting position and team strength. Once drivers are compared at similar grid positions, the residual home advantage is weak or not practically meaningful. This suggests home country is not a dominant standalone feature.

Suggested figure: `figures/q2_home_vs_away_by_grid.png`

### Q3. Which constructors have the highest mechanical-retirement rate in 2014–2021 vs. 2022–2024?
The notebook compares constructor mechanical-retirement rates across the two periods and shows that the ranking can shift between eras. This supports the idea that reliability patterns are era-sensitive and should be treated as historical rather than fixed.

Suggested figure: `figures/q3_mechanical_retirements.png`

## 3. Selected Features and Why They Are Valid
The model uses only pre-race information.

- Grid position and grid-derived features are valid because the starting order is known before lights out.
- Qualifying position and qualifying gaps are valid because qualifying is complete before the race starts.
- Driver form features are valid because they are computed only from earlier races.
- Constructor form features are valid for the same reason.
- Circuit-history features are valid because they summarize past races only.
- Season-context features are valid because they describe the race environment without using future information.
- Missingness indicators are valid because they only encode whether a pre-race value is absent; they do not use race outcomes.

The notebook explicitly avoids future information such as finishing position, points, laps completed, pit stops, and post-race standings.

Suggested figures:
- `figures/features_univariate_auc.png`
- `figures/features_deciles.png`
- `figures/features_correlation.png`

## 4. Data Cleaning and Modeling Table
The modeling table is built from the raw relational F1 CSVs into a single driver-race table. The notebook ensures one row per `(raceId, driverId)` and resolves shared-drive duplicates with a pre-race rule based on grid position. It also removes Indianapolis 500 entries and non-starters so the final table matches the prediction target.

## 5. Model Summary
The project compares a grid baseline with logistic regression, random forest, gradient boosting, and a shallow neural network. The primary metric is PR-AUC because podiums are rare and class imbalance makes PR-AUC more informative than ROC-AUC. F1 is used for the final classification threshold chosen on validation.

Final test performance reported in the notebook:
- Logistic regression: PR-AUC 0.722, ROC-AUC 0.938, F1 0.681
- Random forest: PR-AUC 0.720, ROC-AUC 0.941, F1 0.677
- Gradient boosting: PR-AUC 0.705, ROC-AUC 0.939, F1 0.677
- Shallow neural network: PR-AUC 0.725, ROC-AUC 0.937, F1 0.686

Suggested figures:
- `figures/model_comparison.png`
- `figures/model_confusion_matrix.png`
- `figures/model_calibration.png`
- `figures/model_thresholds.png`

## 6. Feature-Group Ablation and Preprocessing Experiments
Ablation shows how much each feature group contributes after training. The notebook also includes preprocessing-order experiments, showing that fitting preprocessing on training data only is the correct leakage-safe choice.

Suggested figures:
- `figures/ablation.png`
- `figures/prep_before_after.png`
- `figures/prep_experiment_imputation.png`

## 7. Explainability
The notebook includes global explanations using permutation importance and SHAP, plus local explanation using LIME and local SHAP. These explain which features the model relies on, not causal effects.

Suggested figures:
- `figures/xai_permutation_importance.png`
- `figures/xai_shap_summary.png`
- `figures/xai_shap_bar.png`
- `figures/xai_shap_waterfall.png`
- `figures/xai_lime.png`

## 8. Inference Function
The notebook includes an inference function that accepts a partially specified pre-race record, fills missing values using the training preprocessing pipeline, and returns a podium probability. It is demonstrated on both a complete record and a partially missing record.

## 9. Limitations
The main limitations are:
- historical qualifying coverage is uneven across eras,
- Formula 1 regulations and team structures change over time,
- race incidents such as crashes, weather, safety cars, and mechanical failures are not predictable from pre-race data,
- the top models are close in performance, which suggests a natural ceiling for this feature set.

## 10. Conclusion
The notebook satisfies the milestone goal of building a leakage-safe podium predictor from pre-race features. The analytical evidence supports the chosen features, the season-based split, and the final model selection.
