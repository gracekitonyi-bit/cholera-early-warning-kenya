# Cholera Early Warning in Kenya

This repository contains the reproducible analysis for the MSc thesis:

**Explainable Machine Learning Models for Early Warning of Cholera Outbreaks in Kenya Using Climate and Epidemiological Indicators**

The study develops and evaluates a county-level early-warning framework for predicting cholera outbreaks in Kenya two to four weeks in advance using epidemiological, climatic, environmental, demographic and water, sanitation and hygiene (WASH) indicators.

## Study Overview

The analysis covers all 47 counties in Kenya using weekly observations from 2015 to 2025.

The study aimed to:

- describe temporal patterns in cholera cases, deaths, incidence and outbreak occurrence;
- compare ARIMA, Random Forest and XGBoost for three-week-ahead outbreak prediction;
- evaluate outbreak detection and false-alert burden;
- identify important predictive contributors using SHAP;
- assess robustness across two-, three- and four-week prediction horizons.

The primary analytical outbreak definition was a county-week with at least five reported cholera cases.

A broader sensitivity definition additionally classified county-weeks with incidence of at least one case per 100,000 population as outbreaks.

## Data Structure

The complete surveillance panel contained:

- 47 counties;
- 574 weekly observations per county;
- 26,978 county-week observations;
- 23 predictors used in the machine-learning analysis.

After removing observations with structurally unavailable lagged predictors or future outcomes, the primary three-week modelling sample contained:

**26,508 county-weeks**

The predictor domains included:

- epidemiological surveillance;
- temporal indicators;
- rainfall and temperature;
- lagged climatic variables;
- environmental indicators;
- population;
- WASH vulnerability.

## Prediction Framework

For county \(i\) at epidemiological week \(t\), the study estimated the probability of a future outbreak at prediction horizon \(h\):

\[
P(Y^{(h)}_{i,t}=1 \mid X_{i,t})
\]

where:

- \(X_{i,t}\) represents information available at prediction time;
- \(h \in \{2,3,4\}\) weeks;
- three weeks was the primary prediction horizon.

The analysis focused on prediction rather than causal inference.

## Modelling Approaches

Three modelling strategies were evaluated.

### ARIMA

County-specific ARIMA models were used as the statistical time-series baseline.

Forecasted cholera case counts were converted to outbreak predictions using the same primary outbreak threshold applied to the machine-learning models.

### Random Forest

Random Forest models were evaluated using:

- unweighted classification;
- class-balanced classification.

Random Forest achieved high specificity but relatively low outbreak detection at the fixed classification threshold.

### XGBoost

XGBoost provided the most balanced early-warning profile among the machine-learning approaches.

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

The analysis preserved chronological order to reduce the risk of information leakage from future observations.

The data were divided as follows:

- **Training:** 2015–2022
- **Validation:** 2023–2024
- **Independent test:** 2025

Partition assignment was based on the date of the future prediction target.

## Main Results

The independent 2025 test set contained:

- 2,397 county-week observations;
- 705 outbreak observations;
- 1,692 non-outbreak observations.

The final three-week XGBoost model produced:

- True positives: 413
- False negatives: 292
- True negatives: 1,441
- False positives: 251

### Final XGBoost Performance

| Metric | Value |
|---|---:|
| Accuracy | 0.7735 |
| Precision | 0.6220 |
| Sensitivity | 0.5858 |
| Specificity | 0.8517 |
| F1-score | 0.6034 |
| Balanced accuracy | 0.7187 |
| ROC AUC | 0.7601 |
| PR AUC | 0.5933 |
| Brier score | 0.1741 |

The results indicate moderate predictive ability rather than complete outbreak detection.

The model detected approximately 58.6% of outbreak county-weeks while correctly classifying approximately 85.2% of non-outbreak county-weeks.

## ARIMA versus XGBoost

The final ARIMA and XGBoost models were compared on the same 2,397 observations from the 2025 test period.

### ARIMA

- True positives: 363
- False negatives: 342
- True negatives: 1,540
- False positives: 152

### XGBoost

- True positives: 413
- False negatives: 292
- True negatives: 1,441
- False positives: 251

XGBoost therefore detected:

**50 more outbreak county-weeks**

but generated:

**99 additional false alerts**

ARIMA achieved higher accuracy, precision and specificity, while XGBoost achieved greater sensitivity and slightly higher balanced accuracy.

The two approaches were therefore interpreted as having different operational error profiles rather than one model being uniformly superior.

## Prediction Horizon

The locked XGBoost specification was evaluated at two-, three- and four-week prediction horizons.

| Horizon | Sensitivity | Specificity | Balanced Accuracy | ROC AUC | PR AUC |
|---|---:|---:|---:|---:|---:|
| 2 weeks | 0.5915 | 0.8475 | 0.7195 | 0.7601 | 0.5816 |
| 3 weeks | 0.5858 | 0.8517 | 0.7187 | 0.7601 | 0.5933 |
| 4 weeks | 0.5844 | 0.8251 | 0.7047 | 0.7317 | 0.5700 |

Performance at two and three weeks was very similar.

The three-week horizon was retained because it provided one additional week of warning with negligible loss in balanced accuracy compared with the two-week horizon.

## Model Interpretation

SHapley Additive exPlanations (SHAP) were used to interpret the final XGBoost model.

Important predictive contributors included:

- calendar year;
- WASH vulnerability;
- current cholera cases;
- population.

SHAP values were interpreted as contributions to the fitted prediction model and not as causal effects.

The prominence of calendar year motivated a sensitivity analysis in which year was removed from the model.

## Sensitivity Analyses

The study evaluated several robustness checks.

### Alternative Outbreak Definition

A broader analytical rule increased the number of 2025 outbreak county-weeks from 705 to 730.

Sensitivity remained similar, while specificity and balanced accuracy declined modestly.

### Calendar-Year Sensitivity

Removing calendar year increased sensitivity but also substantially increased false alerts and reduced specificity.

This suggested that the model did not depend on calendar year alone, but temporal transportability should be monitored carefully.

### Random Forest Class Weighting

Balanced class weighting unexpectedly reduced Random Forest sensitivity at the fixed 0.50 threshold.

This was investigated by examining predicted probability distributions among observed outbreak weeks.

### Temporal Stability

Expanding-window evaluation across 2017–2024 showed that predictive performance varied over time.

This supports the need for prospective monitoring, recalibration and periodic model updating.

## Calibration

The final XGBoost model showed imperfect probability calibration.

Key calibration results included:

- mean predicted probability: 0.3989;
- observed outbreak prevalence: 0.2941;
- expected calibration error: 0.1047;
- calibration intercept: -0.5702;
- calibration slope: 1.6499.

The raw model probabilities should therefore not be interpreted as perfectly calibrated estimates of absolute outbreak risk.

## Repository Structure

```text
cholera-early-warning-kenya/
│
├── README.md
├── cholera-early-warning_analysis.ipynb
├── Grace_Nzambali-Kitonyi_MSc_Thesis_2026.pdf
└── figures/
    └── final analysis figures
