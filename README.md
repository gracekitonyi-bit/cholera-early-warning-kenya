# Cholera Early Warning in Kenya

This repository contains the reproducible analysis for the MSc thesis:

**Explainable Machine Learning Models for Early Warning of Cholera Outbreaks in Kenya Using Climate and Epidemiological Indicators**

The study develops and evaluates a county-level early-warning framework for predicting cholera outbreaks in Kenya two to four weeks in advance using epidemiological, climatic, environmental, demographic and WASH-related indicators.

## Study Overview

The analysis covers all 47 counties in Kenya using weekly observations from 2015 to 2025.

The main objectives were to:

- describe temporal patterns in cholera cases, deaths, incidence and outbreak occurrence;
- compare ARIMA, Random Forest and XGBoost models for three-week-ahead outbreak prediction;
- evaluate outbreak detection and false-alert burden;
- identify important predictors using SHAP;
- assess robustness across two-, three- and four-week prediction horizons.

The primary analytical outbreak definition was a county-week with at least five reported cholera cases.

## Data Structure

The complete surveillance panel contained:

- 47 counties;
- 574 weekly observations per county;
- 26,978 county-week observations;
- 23 predictors used in the machine-learning analysis.

After removing observations with structurally unavailable lagged predictors or future outcomes, the primary three-week modelling sample contained 26,508 county-weeks.

The data included information from the following domains:

- epidemiological surveillance;
- temporal indicators;
- rainfall and temperature;
- environmental indicators;
- population;
- WASH vulnerability.

## Modelling Framework

The study compared three modelling approaches:

### ARIMA
County-specific ARIMA models were used as the statistical time-series baseline.

### Random Forest
Random Forest models were evaluated using both unweighted and class-balanced configurations.

### XGBoost
XGBoost was selected as the main machine-learning early-warning model.

The final XGBoost specification used:

- 300 estimators;
- maximum depth = 2;
- learning rate = 0.03;
- subsample = 0.80;
- column sample = 0.80;
- positive-class weight = 6;
- classification threshold = 0.50;
- random seed = 42.

## Temporal Validation

The analysis preserved chronological order:

- **Training:** 2015–2022
- **Validation:** 2023–2024
- **Independent test:** 2025

This design was used to reduce the risk of information leakage from future observations.

## Main Results

The independent 2025 test set contained 2,397 county-weeks:

- 705 outbreak observations;
- 1,692 non-outbreak observations.

The final three-week XGBoost model produced:

- True positives: 413
- False negatives: 292
- True negatives: 1,441
- False positives: 251

Performance metrics were:

- Accuracy: 0.7735
- Precision: 0.6220
- Sensitivity: 0.5858
- Specificity: 0.8517
- F1-score: 0.6034
- Balanced accuracy: 0.7187
- ROC AUC: 0.7601
- PR AUC: 0.5933
- Brier score: 0.1741

## ARIMA versus XGBoost

On the same 2025 test observations:

- ARIMA detected 363 outbreak county-weeks;
- XGBoost detected 413 outbreak county-weeks.

XGBoost therefore detected 50 additional outbreaks but generated 99 additional false alerts.

The two approaches were interpreted as having different operational error profiles rather than one model being uniformly superior.

## Model Interpretation

SHAP was used to interpret the final XGBoost model.

Important predictive contributors included:

- calendar year;
- WASH vulnerability;
- current cholera cases;
- population.

SHAP values were interpreted as contributions to the fitted prediction model and not as causal effects.

## Prediction Horizon

Performance at two and three weeks was very similar, while performance declined more clearly at four weeks.

The three-week horizon was retained because it provided one additional week of warning compared with the two-week horizon with negligible loss in balanced accuracy.

## Repository Structure

```text
cholera-early-warning-kenya/
│
├── README.md
├── requirements.txt
├── .gitignore
│
├── notebooks/
│   └── cholera_early_warning_analysis.ipynb
│
├── thesis/
│   └── Grace_Nzambali_Kitonyi_MSc_Thesis_2026.pdf
│
└── figures/
    └── final analysis figures
