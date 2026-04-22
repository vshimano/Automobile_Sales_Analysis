# U.S. Automobile Sales Analysis - Part 2: Advanced Modeling

**Part 2 of the [Automobile Sales Analysis](../README.md) Series**

## Overview

Part 1 established that U.S. automobile sales are strongly correlated with unemployment rate (r = -0.689) and consumer sentiment (r = +0.598), and that multiple linear regression explains approximately 75% of sales variance. However, time series cross-validation revealed a critical limitation - mean R² of just 0.039 across historical periods, with catastrophic failure at structural breaks like the 2008 financial crisis and COVID-19 pandemic.

Part 2 addresses this by applying four additional modeling approaches that account for time series structure and non-linear relationships, then comparing all five models head-to-head.

## Models Implemented

| Model | Approach | Key Strength |
|-------|----------|-------------|
| Linear Regression *(Part 1 baseline)* | Economic indicators only | Interpretable economic relationships |
| **ARIMA(0,1,2)** | Classical time series | Captures temporal autocorrelation |
| **Ridge Regression** | Regularized linear model | Clean coefficient estimates under multicollinearity |
| **Random Forest** | Ensemble (bagging) | Combines temporal + economic features |
| **XGBoost** | Ensemble (boosting) | Non-linear relationships, sequential error correction |


## Results Summary

All models evaluated using **time series cross-validation (5 folds)** - the only honest evaluation method for temporal data.

| Model | Mean CV R² | Mean RMSE | R² Std Dev |
|-------|-----------|-----------|------------|
| Linear Regression | 0.039 | 1.445M | 0.360 |
| Ridge Regression | -0.358 | 1.602M | 1.288 |
| ARIMA(0,1,2) | -1.264 | 2.305M | 1.606 |
| XGBoost | 0.215 | 1.306M | 0.656 |
| **Random Forest** | **0.436** | **1.159M** | **0.361** |

**Random Forest is the best overall model** - highest R², lowest RMSE, and tied for best consistency.


## Key Findings

**1. Temporal features dominate short-range prediction**
Both Random Forest and XGBoost assign ~95% of predictive importance to lagged sales values and rolling averages. `sales_lag_1` alone accounts for 62% of Random Forest's predictive power.

**2. Economic indicators explain *why*, not *what next***
Linear and Ridge regression explain the long-run economic story but cannot predict well across time periods. Knowing where the economy is matters for structural understanding; knowing where sales have been matters for month-to-month forecasting.

**3. ARIMA is unsuitable for long-range forecasting**
As a univariate model, ARIMA has no access to economic conditions and reverts toward the long-term mean when forecasting 8+ years ahead. Mean CV R² of -1.264, making it the weakest model in this evaluation framework.

**4. Ridge regression's value is interpretive, not predictive**
Despite worse cross-validation performance than standard linear regression, Ridge provides the most reliable coefficient estimates by separating multicollinear predictors. The recession coefficient shrinks from -0.823 to -0.245 once unemployment overlap is removed - a more accurate economic insight.

**5. Data volume matters for tree-based models**
Using the full 576-row dataset (1977–2024) vs. a 413-row subset improved Random Forest mean R² from -0.844 to +0.436 - a difference of 1.28 R² points.

## Feature Importance

| Feature | Random Forest | XGBoost |
|---------|--------------|---------|
| `sales_lag_1` | 62.0% | 33.5% |
| `sales_rolling_3` | 27.8% | 44.7% |
| `sales_rolling_6` | 2.4% | 4.1% |
| All economic indicators combined | ~4.3% | ~5.5% |

Both tree-based models agree: **recent sales momentum is far more predictive than current economic conditions** for short-range month-to-month forecasting.

## Interactive Dashboard

The notebook includes a **Dash dashboard** (`app.run(debug=True, port=8051)`) with three tabs:

- **Tab 1 - Model Performance**: Mean CV R², RMSE, and R² consistency comparison across all five models; R² by fold line chart
- **Tab 2 - Feature Importance**: Individual and side-by-side importance charts for Random Forest and XGBoost
- **Tab 3 - Predictions**: Interactive actual vs. predicted chart with year range slider and model selector; residuals comparison

## Requirements

```
pandas
numpy
matplotlib
seaborn
scikit-learn
statsmodels
pmdarima
xgboost
dash
plotly
sqlite3 (standard library)
scipy
```

Install missing libraries with `pip install <library_name>`.


## How to Run

> **Note:** This notebook loads data directly from `auto_sales.sqlite` - the database created in Part 1. The file is not included in the repository. To run this notebook, either run Part 1 first to generate the database, or copy `auto_sales.sqlite` from your Part 1 local folder into this folder before proceeding.

1. Ensure `auto_sales.sqlite` is present in the same directory as this notebook
2. Install all required libraries (see above)
3. Run all cells in order
4. The Dash dashboard launches at `http://127.0.0.1:8051` - open in your browser


## Notebook Structure

| Section | Content |
|---------|---------|
| 1 | Introduction & business questions |
| 2 | Library imports, data loading, feature engineering |
| 3 | ARIMA - stationarity testing, ACF/PACF, auto model selection, diagnostics, cross-validation |
| 4 | Random Forest - setup, training, cross-validation, feature importance |
| 5 | Ridge Regression - coefficient analysis, cross-validation |
| 6 | XGBoost - training, cross-validation, feature importance comparison |
| 7 | Model comparison, overall conclusions, limitations & future work |
| 8 | Interactive Dash dashboard |


## Known Limitations

- All models struggle with **structural breaks** - the 2008 financial crisis and COVID-19 pandemic remain difficult to predict regardless of model sophistication
- XGBoost hyperparameters were not exhaustively tuned - a thorough grid search could potentially close the gap with Random Forest
- ARIMA was evaluated in a long-range forecasting framework that does not reflect its intended use case; short-range (1–3 month) evaluation would show much better performance
- No ensemble model was tested - combining Random Forest and XGBoost predictions could potentially outperform either individually


## Future Work (Part 3)

Part 3 will apply the best performing model (Random Forest) to **regional U.S. data**, testing whether the momentum-driven prediction pattern holds across different geographic markets or whether economic indicators play a larger role in specific regions.


## Series Navigation

- [Part 1 - EDA & Linear Regression](../Part1_US_Analysis/README.md)
- **Part 2 - Advanced Modeling** *(this notebook)*
- Part 3 - Regional Analysis *(coming soon)*


*Data source: Federal Reserve Economic Data (FRED). Analysis covers U.S. automobile sales 1976–2024.*
