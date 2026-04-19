# Part 1: U.S. Automobile Sales & Economic Conditions (1976–2024)

Part 1 of the [Automobile Sales Analysis](../README.md) series. This analysis examines the relationship between U.S. automobile sales and key economic indicators over nearly five decades, spanning six distinct recession periods.

---

## Overview

Automobile sales are one of the most economically sensitive consumer categories — a major financial commitment that buyers postpone when jobs are scarce and confidence is low. This analysis quantifies exactly how sensitive, and which economic conditions matter most.

> **Important:** All auto sales figures are reported as **SAAR — Seasonally Adjusted Annual Rate**, expressed in millions of vehicles per year. A value of 15.0 means sales were occurring at a pace that would produce 15 million vehicle sales if sustained for a full year. Differences between periods, while appearing small in absolute numbers, represent millions of vehicles and hundreds of billions of dollars in industry revenue.

---

## Business Questions

1. **Recession Impact** — How significantly do recessions affect auto sales, and does the severity vary across recession periods?
2. **Economic Correlations** — Which indicators correlate most strongly with auto sales?
3. **Long-term Trends** — How have sales trended over five decades?
4. **Predictive Power** — Can economic indicators reliably predict auto sales?

---

## Key Findings

### Recession Impact
- Normal period average: **15.471 million vehicles/year**
- Recession period average: **12.434 million vehicles/year**
- Difference: **3.037 million vehicles/year — a 20% decline**
- At average transaction prices, this represents ~**$100 billion in lost annual industry revenue**

| Recession | Duration | Avg Sales | Peak Unemployment | Avg Interest Rate |
|-----------|----------|-----------|-------------------|-------------------|
| 1980 | 6 months | 11.242M | 7.8% | 13.07% |
| 1981-82 | 16 months | 10.441M | 10.8% | 13.29% |
| 1990-91 | 8 months | 13.136M | 6.8% | 7.35% |
| 2001 | 8 months | 17.559M* | 5.5% | 3.51% |
| 2008-09 | 18 months | 12.243M | 9.5% | 1.35% |
| 2020 | 2 months | 10.372M | 14.8% | 0.35% |

*2001 exceeded normal average due to post-9/11 stimulus and 0% financing campaigns

### Economic Correlations

| Indicator | Correlation | Strength |
|-----------|-------------|----------|
| Unemployment Rate | -0.689 | Strong negative |
| Consumer Sentiment | +0.598 | Strong positive |
| CPI | +0.357 | Moderate positive |
| Interest Rate | -0.363 | Moderate negative |
| Gas Price (1990-2024) | -0.057 | Weak negative |

**Most counterintuitive finding:** Gas prices show almost no correlation with sales volume — consumers shift vehicle preferences (SUV → compact) rather than postponing purchases entirely.

### Predictive Modeling

| Model | Predictors | R² | RMSE |
|-------|------------|-----|------|
| Simple regression | Unemployment only | 0.550 | 1.566M |
| Model A | All 5 indicators | 0.755 | 1.156M |
| **Model B (selected)** | **Without CPI** | **0.754** | **1.157M** |
| Model C | Unemployment + Sentiment | 0.674 | 1.332M |

Model B selected as final model — equivalent performance to Model A with one fewer variable (CPI is redundant due to multicollinearity with interest rate).

**Time series cross-validation reveals important limitations:**
- Mean R² across folds: 0.039 (vs 0.754 on test set)
- Performance varies dramatically by era — the model fails at structural breaks (2008 crisis, COVID-19)
- This motivates Part 2: advanced modeling with ARIMA and Random Forest

---

## Data Sources

All data retrieved via **FRED API** (Federal Reserve Bank of St. Louis):

| FRED Series | Description | Frequency |
|-------------|-------------|-----------|
| `TOTALSA` | Total Vehicle Sales (SAAR) | Monthly |
| `UNRATE` | Unemployment Rate | Monthly |
| `FEDFUNDS` | Federal Funds Rate | Monthly |
| `GASREGCOVW` | Regular Gas Price | Weekly → resampled monthly |
| `UMCSENT` | Consumer Sentiment Index | Monthly |
| `USREC` | Recession Indicator (NBER) | Monthly |
| `CPIAUCSL` | Consumer Price Index | Monthly |

**Coverage:** January 1976 – December 2024 (588 monthly observations)
**Gas price note:** Available from January 1990 only — a separate `df_gas` dataset (413 rows) is used for gas price analysis

---

## Project Structure

```
Part1_US_Analysis/
├── autoSalesAnalysis.ipynb    # Main analysis notebook
└── README.md                  # This file
```

**Generated locally (not included in repository):**
- `auto_sales.sqlite` — SQLite database with three tables
- `config.py` — API keys (excluded via .gitignore)

---

## Installation & Setup

```bash
# Install required libraries
pip install pandas numpy matplotlib seaborn plotly dash scikit-learn fredapi wordcloud

# API key setup
# 1. Register for a free FRED API key at https://fred.stlouisfed.org
# 2. Create config.py in the same folder as the notebook:

# config.py
FRED_API_KEY = "your_fred_api_key_here"
BEA_API_KEY = "your_bea_api_key_here"  # obtained but not used directly
```

> **Note:** The SQLite database (`auto_sales.sqlite`) is not included in the repository due to file size. It is generated automatically when you run Section 4 of the notebook.

> **Note:** The `config.py` file is excluded from the repository for security. You must create it locally with your own API key before running the notebook.

---

## Notebook Structure

| Section | Content |
|---------|---------|
| 1 | Introduction, business questions, dataset overview |
| 2 | Data collection via FRED API |
| 3 | Data wrangling — merging, missing values, resampling |
| 4 | Exploratory data analysis — SQL queries, correlations |
| 5 | Data visualization — 6 charts |
| 6 | Predictive modeling — regression, cross-validation |
| 7 | Conclusions, implications, limitations, future work |
| 8 | Interactive Dash dashboard |

---

## Dashboard

An interactive dashboard built with Plotly and Dash covers three areas:

- **Tab 1 — Auto Sales Trends**: Time series with year range slider, recession comparison charts
- **Tab 2 — Economic Indicators**: Selectable indicator time series, correlation heatmap, scatter plots
- **Tab 3 — Modeling Results**: Model comparison, cross-validation results, predicted vs. actual

To run the dashboard, execute the final notebook cell and open `http://127.0.0.1:8050` in your browser.

---

## Limitations

- National aggregate data masks significant regional variation
- No vehicle type breakdown (cars vs trucks vs EVs)
- Linear regression fails at structural breaks — see Part 2 for advanced modeling
- Gas price data unavailable before 1990
- All relationships are correlational, not causal

---

## Next: Part 2

The cross-validation findings in this analysis directly motivate Part 2, which will apply **ARIMA time series models** and **Random Forest** to the same national dataset — testing whether models that account for temporal structure or non-linear relationships can better handle the regime changes that defeat linear regression.

