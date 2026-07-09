# PTSD Treatment Outcome Prediction: Regression Modelling and Decision Curve Analysis

This repository contains the Python code developed for a Master's dissertation investigating PTSD treatment outcomes in veterans. The project combines logistic and linear regression modelling with Decision Curve Analysis (DCA) to evaluate the predictive and clinical utility of study-level clinical trial metadata.

## Project Overview

The analysis uses aggregated study-arm level metadata from the United States Department of Veterans Affairs PTSD Repository (accessed via DataLumos) to address three research questions:

- **RQ1:** Can logistic regression models estimate the probability of PTSD treatment success using study-level predictors?
- **RQ2:** Can linear regression models predict continuous PTSD severity/symptom outcomes?
- **RQ3:** Does Decision Curve Analysis demonstrate that regression-based probability estimates offer clinical decision benefit?

The final analytical dataset comprises **99,491 observations** across **202 predictors**, drawn from multiple linked sources within the repository (study characteristics, sample demographics, interventions, and dichotomous/continuous outcome files).

## Repository Contents

| File | Description |
|---|---|
| `temmy_2 (1).py` | Loads raw repository CSV exports, merges study-level and arm-level sources, engineers features (e.g. `baseline_z`, `treatment_duration`), constructs the binary and continuous outcome variables, applies the veteran-focused filter, and handles missing data. Also trains the logistic regression model (binary treatment success) and the linear regression model (continuous symptom change), runs diagnostic checks (calibration, VIF), and performs Decision Curve Analysis. Exports all summary tables and figures used in the results chapter. |
| `temmy_2 (4).py` | An expanded, annotated version of the same end-to-end pipeline (data preparation through Decision Curve Analysis), retaining additional inline documentation and debugging steps from iterative development in Colab. |

## Data Source

Data are drawn from the **VA PTSD Repository**, hosted on DataLumos:
> DataLumos (2025) *Project view*. Available at: https://www.datalumos.org/datalumos/project/240581/version/V1/view

Raw source files expected (place in a local `data/` folder, not committed to GitHub):
- `Study_Characteristics_20250403.csv`
- `Sample_Characteristics_20250403.csv`
- `Study_Interventions_20250403.csv`
- `PTSD_Dichotomous_Outcomes_Between_Arms_20250403.csv`
- `PTSD_Dichotomous_Outcomes_Within_Arms_20250403.csv`
- `PTSD_Continuous_Outcomes_Between_Arms_20250403.csv`
- `PTSD_Continuous_Outcomes_Within_Arms_20250403.csv`

**These files are not included in this repository.** Access to the repository is subject to DataLumos/VA data use terms; users must obtain their own copy.

## Methodology Summary

- **Unit of analysis:** individual treatment arm within a clinical trial (not individual patients)
- **Outcome variables:**
  - `binary_outcome` — treatment success (≥50% diagnostic remission), used for logistic regression
  - `Percent Achieved Diagnostic Change` / `score_difference_1` — continuous symptom change, used for linear regression
- **Predictors:** clinical/contextual (trauma type, diagnostic measure, clinical setting, military status), intervention (treatment name, delivery method, duration), demographic (veteran/female/male percent), and methodological (study arm identifier)
- **Encoding:** one-hot encoding for categorical/nominal predictors; z-score standardisation for baseline severity
- **Missing data:** median imputation for numeric predictors; high-missingness result-oriented fields removed
- **Class imbalance:** handled via class-weighted logistic regression (not oversampling)
- **Model evaluation:** accuracy, precision, recall, F1, ROC-AUC, confusion matrix, calibration curve/slope/intercept, Brier score (logistic); R², MAE, RMSE, residual and Q-Q diagnostics (linear); Variance Inflation Factor (multicollinearity)
- **Decision Curve Analysis:** net benefit calculated across threshold probabilities (0.01–0.99) and compared against Treat-All and Treat-None default strategies

## Requirements

```
python >= 3.9
pandas
numpy
scikit-learn
statsmodels
matplotlib
seaborn
scipy
openpyxl
```

Install with:

```bash
pip install pandas numpy scikit-learn statsmodels matplotlib seaborn scipy openpyxl
```

## Usage

1. Place the raw repository CSV files in a local `data/` directory.
2. Update the `data_path` variable at the top of the script to point to that directory.
3. Run the script to build the final analytical dataset, train the models, and generate all tables and figures:

   ```bash
   python "temmy_2 (1).py"
   ```

   (or `python "temmy_2 (4).py"` for the annotated version)

   This produces `Final_Veterans_PTSD_Dataset.csv` in the data directory, along with the Excel tables and PNG figures in the working directory.

## Outputs

**Tables (Excel):**
Final dataset overview, descriptive statistics, distribution assessment, outcome distribution, classification metrics, logistic regression coefficients, calibration measures, linear regression performance and coefficients, VIF results, DCA net benefit results.

**Figures (PNG):**
Outcome distribution chart, confusion matrix, ROC curve, calibration curve, residual plot, residual distribution/Q-Q plot, Decision Curve Analysis plot.

## Notes and Limitations

- All modelling is performed on **aggregated study-arm level data**, not individual patient records; findings describe study-level patterns rather than individual-level treatment effects.
- The code was developed iteratively in Google Colab; file paths reference Google Drive by default and will need adjustment for local or other cloud environments.
- No patient-identifiable data are used or stored at any stage.
