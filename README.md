# Credit Scoring Model — Logistic Regression (SAS)

Predicting the probability that a customer will default on their loan payment, using demographic and credit bureau data.

## Overview

Banks need to identify customers who are likely to become non-performing assets (NPAs) so they can act before losses grow. This project builds a **logistic regression credit scoring model** on the Kaggle "Give Me Some Credit" dataset to flag customers at high risk of default.

- **Dataset:** ~150,000 observations, 13 variables (Kaggle — "Give Me Some Credit")
- **Target variable:** `NPA_Status` (`Serious Dlqin2yrs`) — 1 = defaulted, 0 = did not default
- **Class balance:** ~93% non-defaulters, ~7% defaulters
- **Tool:** SAS

## Repository Contents

| File | Description |
|---|---|
| `Project_writeup.md` | Full write-up of the approach: data exploration, cleaning, imputation logic, dummy variable creation, and model-building steps |
| `SAS_code.md` | Complete annotated SAS code — data import, cleaning, outlier treatment, imputation, dummy variables, model fitting, ROC/lift charts, validation |
| `Credit_Scoring_model_-_Results_&_Conclusion.pdf` | Presentation of data exploration findings, model output, and final conclusions |
| `README.md` | This file |

## Methodology

1. **Data Exploration** — checked variable types, missing values, outliers, and duplicate fields; identified the target variable.
2. **Data Preparation**
   - Converted `MonthlyIncome` and `NumberOfDependents` from character to numeric.
   - Deleted records with missing target variable (cannot be imputed).
   - Treated outliers/erroneous values in:
     - `RevolvingUtilizationOfUnsecuredLines` (impute values >100% with the mean of valid observations)
     - `NumberOfTime30-59/60-89/90+ DaysPastDue` (impute implausible values like 96/98 using event-rate matching from cross-tabs)
     - `Age` (single record with age = 0 imputed to average age of 52)
     - `DebtRatio` (values > 2 imputed with mean of 0.30)
     - `MonthlyIncome` (~19% missing, imputed via age-group × occupation cross-tab means)
     - `NumberOfDependents` (~2.3% missing, imputed via age-group × income-group medians)
   - Created dummy variables for gender, occupation, region, house-ownership status, and education.
3. **Model Building** — Split data into training (~70%) and validation (~30%) sets; fit a logistic regression on `NPA_Status` using `PROC LOGISTIC`, iterating to select the model with the lowest AIC.
4. **Model Evaluation**
   - Model fit statistics (AIC, SC, -2 Log L)
   - Global null hypothesis tests (Likelihood Ratio, Score, Wald)
   - Concordance / Somer's D / Gamma
   - Hosmer–Lemeshow goodness-of-fit test
   - ROC curve and gain (lift) chart
   - Validation-set scoring and misclassification rate

## Key Results

- **Concordance:** 89.2% of predicted probability pairs correctly ranked.
- **Validation accuracy:** ~94% of events correctly classified (0.71% false positives, 4.69% false negatives).
- **Significant drivers of default (odds ratios), in order of impact:**
  - Credit utilization: +1 unit → ~424% higher odds of default
  - Education level "Matric": ~307% higher odds vs. reference group
  - Default 90+ days past due: ~90% higher odds
  - Debt-to-income ratio: +1 unit → ~70% higher odds
  - Moving up one income group: ~6% lower odds
- **Regional patterns:** Default rates were highest in the West and North regions; the Centre region showed the lowest default rates.
- **Age patterns:** ~75% of defaulters fall in the 30–60 age range; defaults among customers 60+ are a concern given reduced post-retirement repayment ability.
- **Income patterns:** ~93% of defaulters have income below 10,000; the 2,500–5,000 income band alone accounts for ~34% of defaulters.

## Limitations

- The Hosmer–Lemeshow test p-value (< 0.0001) suggests the model's calibration is not a strong fit despite good discrimination (ROC/concordance), so probability estimates should be interpreted with caution.

## Source

Original project repository: [Credit-Scoring-Model-SAS-](https://github.com/Vicky-Crasto/Credit-Scoring-Model-SAS-)
