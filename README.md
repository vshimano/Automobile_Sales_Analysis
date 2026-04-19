# Automobile Sales Analysis: A Multi-Scale Economic Study

A multi-part data analytics project examining automobile sales dynamics across different scales — from national economic conditions to regional variation and international comparison. Each part builds on the findings of the previous one, with the scope and direction of subsequent parts informed by what each analysis reveals.

---

## Project Series

| Part | Title | Status | Key Methods |
|------|-------|--------|-------------|
| [Part 1](./Part1_US_Analysis/) | U.S. National Trends (1976–2024) | ✅ Complete | EDA, Linear & Multiple Regression |
| Part 2 | Advanced Modeling — U.S. National | 🔲 Planned | ARIMA, Random Forest |
| Part 3 | Regional & International Variation | 🔲 Planned | EDA, Regional Comparison |
| Part 4 | Geopolitical Shocks & EV Transition | 🔲 Planned | TBD |

---

## Key Findings — Part 1

- **Recessions reduce auto sales by ~20%** on average — a 3 million vehicle/year drop representing ~$100 billion in lost industry revenue
- **Unemployment is the dominant predictor** of auto sales (Pearson r = -0.689), more so than interest rates or gas prices
- **Gas prices show almost no correlation** with sales volume (r = -0.057) — consumers shift vehicle preferences rather than postponing purchases
- **Linear regression explains 75% of variance** but fails at structural breaks (2008 crisis, COVID) — motivating advanced modeling in Part 2
- **Long-term trend is gently positive** — rising from ~13.5M to ~16.5M annualized sales over 48 years

---

## Data Sources

All data obtained from **FRED (Federal Reserve Economic Data)** — Federal Reserve Bank of St. Louis:

| Series | Description |
|--------|-------------|
| `TOTALSA` | Total Vehicle Sales (SAAR, millions/year) — BEA |
| `UNRATE` | Unemployment Rate — BLS |
| `FEDFUNDS` | Federal Funds Rate — Federal Reserve |
| `GASREGCOVW` | Regular Gas Price — EIA |
| `UMCSENT` | Consumer Sentiment Index — U. of Michigan |
| `USREC` | Recession Indicator — NBER |
| `CPIAUCSL` | Consumer Price Index — BLS |

---

## Tools & Technologies

| Category | Tools |
|----------|-------|
| Data retrieval | Python, `fredapi` |
| Data manipulation | Pandas, NumPy |
| Database | SQLite, `sqlite3` |
| Visualization | Matplotlib, Seaborn, Plotly |
| Dashboard | Dash |
| Modeling | Scikit-learn |
| Environment | Jupyter Notebook |

---

## Repository Structure

```
Automobile_Sales_Analysis/
├── Part1_US_Analysis/
│   ├── autoSalesAnalysis.ipynb   # Main analysis notebook
│   └── README.md                 # Part 1 documentation
├── .gitignore
└── README.md                     # This file
```



## Setup & Installation

```bash
# Clone the repository
git clone https://github.com/vshimano/Automobile_Sales_Analysis.git
cd Automobile_Sales_Analysis

# Install required libraries
pip install pandas numpy matplotlib seaborn plotly dash scikit-learn fredapi

# API Keys
# A free FRED API key is required — register at (https://fred.stlouisfed.org)
# Store your key in a local config.py file (see Part 1 README for details)
```

---

## Author

**Victoria Shimanovich**
PhD in Applied Mathematics | Data Analytics  
[LinkedIn](https://www.linkedin.com/in/victoria-shimanovich-a7309764/) | [GitHub](https://github.com/vshimano)
