# Telco Customer Churn: Profiling, Cleaning & Business Insights

An end-to-end exploratory analysis of the public [Telco Customer Churn](https://www.kaggle.com/datasets/blastchar/telco-customer-churn) dataset (IBM sample data, 7,043 customers, 21 columns). The notebook profiles data quality, documents every cleaning decision with its rationale, and turns the results into five actionable business insights, with a plain-language summary for non-technical readers.

> The dataset is a public sample dataset, not real company data. Findings describe this dataset only.

## Headline findings

Overall churn is **26.5%**, and churned customers account for about **30%** of monthly revenue.

| # | Insight | Evidence |
|---|---------|----------|
| 1 | Contract type is the strongest predictor | Month-to-month 43% churn vs 3% for two-year contracts |
| 2 | The first 6 months are the danger zone | 53% churn in months 0-6; 55% of all churners leave within a year |
| 3 | Payment method matters | Electronic check 45% vs 15-17% for automatic payments |
| 4 | Fiber customers pay more and leave more | Fiber 42% (55% on month-to-month) vs DSL 19% |
| 5 | Protection and support add-ons retain customers | 0 add-ons: 57% churn; all 4 add-ons: 5% |

All five differences are statistically significant (chi-square). They are **associations, not proven causes**; see the caveats section in the notebook.

## Data quality findings

- **Hidden missing values:** `TotalCharges` is stored as a string, and 11 rows are blank. `isna()` reports zero missing, which is misleading. All 11 are customers with `tenure = 0` (not billed yet), so they are set to 0 rather than imputed with the median.
- **Duplicates:** none (no repeated rows or IDs). Rows identical apart from the ID are different customers and are kept.
- **Outliers:** none by IQR or z-score. 13 rows where `TotalCharges` deviates more than 30% from `tenure x MonthlyCharges` are flagged, not removed.
- **Encoding:** `SeniorCitizen` (0/1) harmonised to Yes/No; `No internet service` / `No phone service` kept as their own categories.

Every decision (what, why, alternatives rejected) is in the cleaning table in section 3 of the notebook.

## How the notebook guards its own claims

- Post-cleaning assertions check row count, ID uniqueness, no remaining nulls, and an unchanged churn target.
- A final guard cell re-computes every number quoted in the executive summary and fails if the data changes and the text goes stale.

## Run it

```bash
pip install -r requirements.txt
# Download the CSV from Kaggle and place it next to the notebook (or in ./data)
jupyter notebook telco_churn_analysis.ipynb
```

The notebook searches the working directory, `./data`, and your Downloads/Desktop/Documents folders for `*Telco-Customer-Churn*.csv`. You can also set `DATA_PATH` in the first code cell.

The notebook is committed with outputs, so it renders fully on GitHub without running.

## Limitations and next steps

- Snapshot data: no churn dates or price history, so no survival analysis.
- Several drivers overlap (month-to-month, electronic check, Fiber, short tenure). Next step: a multivariate model (logistic regression or gradient boosting with SHAP) to isolate each factor's effect.
- Any retention offer should be A/B tested before rollout.
