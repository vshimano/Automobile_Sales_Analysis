# Part 4: Global Automobile Sales Analysis

Part 4 of the [Automobile Sales Analysis](../README.md) series. This analysis examines new passenger car registrations across 14 countries grouped by vehicle ownership culture and transit availability, asking whether the relationship between economic conditions and auto sales differs systematically across market types.

---

## Overview

Parts 1 and 2 established that U.S. automobile sales are dominated by economic momentum - lagged sales values account for approximately 95% of Random Forest predictive power, with economic indicators playing a secondary role in month-to-month forecasting. Part 3 found that transit tier is a structural determinant of per-capita registrations at the regional U.S. level, explaining 20% of additional variance beyond economic conditions alone.

Part 4 takes the analysis international, asking whether transit access and ownership culture produce similar structural effects across countries - and whether economic conditions matter more in markets where car purchase is a discretionary choice rather than a necessity.

> **Data note:** The OECD TOCAPA indicator covers new passenger car registrations only. Trucks, SUVs, and vans represent big portion of new vehicle sales especially in Group 1 (USA, Canada, Australia) but much smaller share in Group 2 and Group 3 markets. This creates systematic undercount Group 1 market activity that affects all registration-level comparisons across groups. Used vehicle purchases are also excluded for all countries which also creates systemic undercount for Group 3 since new registrations capture only the wealthier segment of the market. Group 2 is  represented by the data the best; passenger cars dominate new sales and new car market is accessible to a broader population share. 

---

## Country Groups

Countries are grouped by vehicle ownership culture and transit availability rather than geography, directly extending the transit tier framework from Part 3 to an international context.

| Group | Label | Countries |
|-------|-------|-----------|
| 1 | High Ownership / Limited Transit | United States, Canada, Australia |
| 2 | High Ownership / Strong Transit | Germany, France, United Kingdom, Italy, Japan, South Korea, Spain, Poland |
| 3 | Rising Ownership / Unequal Access | Brazil, Turkey, Chile |

**Excluded markets:** Lower-income country groups were excluded due to absence of reliable machine-readable time series data. OICA direct downloads are no longer publicly accessible. World Bank vehicle ownership indicators were removed in 2015 due to a licensing dispute. Colombia and India were excluded because the OECD reports their data as an index rather than absolute vehicle counts.

---

## Business Questions

1. Do country groups differ significantly in per-capita new car registration rates?
2. Are some groups more sensitive to economic recessions than others?
3. Do GDP per capita and urbanization explain registration differences, or does group membership have an independent effect after economic controls?
4. Does the momentum-dominated Random Forest pattern from Parts 2 and 3 hold internationally, or do economic indicators gain predictive power in specific market types?

---

## Key Findings

### Hypothesis Testing

| Hypothesis | Result | Detail |
|-----------|--------|--------|
| Groups differ in registration rates | Reject H0 | Kruskal-Wallis H=82.19, p<0.001, eta-squared=0.39 (large effect) |
| All group pairs significantly different | All significant | All three pairs pass Bonferroni-corrected Mann-Whitney U (p<0.0167) |
| 2008-2009 recession impact differs by group | Fail to reject H0 | p=0.26 - severely underpowered with n=3 per group |
| COVID 2020 impact differs by group | Fail to reject H0 | p=0.24 - all groups declined uniformly (-31%, -24%, -29%) |

### OLS Regression

Adding group membership to an economic-only model improves $R^2$ by 0.273 (from 0.471 to 0.744). Unlike Part 3, both GDP per capita and group membership remain independently significant after controlling for each other - economic conditions and group structure each explain distinct portions of registration variance in the international context.

### Random Forest by Group

| Group | $R^2$ (test) | RMSE | Top Feature | GDP Importance |
|-------|-------------|------|-------------|---------------|
| 1 - Limited Transit | -2.680 | 0.005 | reg_pc_lag1 | 4.4% |
| 2 - Strong Transit | 0.937 | 0.002 | reg_pc_lag1 | 14.1% |
| 3 - Unequal Access | 0.538 | 0.004 | reg_pc_lag1 | 8.1% |

Momentum dominates all groups. Group 2 shows the highest GDP importance (14.1%) in the series - consistent with the hypothesis that economic conditions matter more in markets where car purchase is discretionary. Group 1 fails due to insufficient sample size (3 countries).

### The Counterintuitive Finding

Group 2 (Strong Transit) has higher median new car registrations per 1,000 people than Group 1 (Limited Transit). This directly reflects the TOCAPA data limitation - passenger cars only, excluding the trucks and SUVs that represent the majority of sales in the USA, Canada, and Australia.

---

## Data Sources

| Source | Data | Coverage | Access |
|--------|------|----------|--------|
| OECD SDMX REST API (DF_INDSERV) | New passenger car registrations, monthly | 2005-2023, 14 countries | Free, no key |
| World Bank API (wbgapi) | GDP per capita PPP, population, urbanization | 2005-2023, all countries | Free, no key |
| WHO GHO API | Road traffic death rate per 100,000 | 2021 snapshot | Free, no key |

**OECD note:** Monthly data is aggregated to annual by summing 12 monthly observations. Only non-seasonally adjusted (`ADJUSTMENT=N`) and absolute count (`UNIT_MEASURE=VEH`) series are used.

---

## Requirements

```
pandas
numpy
matplotlib
seaborn
scikit-learn
scipy
statsmodels
requests
wbgapi
folium
dash
plotly
sqlite3 (standard library)
warnings (standard library)
```

Install missing libraries with `pip install <library_name>`. No API keys are required - all data sources are publicly accessible.

---

## How to Run

> **Note:** This notebook uses a separate database `auto_sales_global.sqlite` that is generated automatically when you run Section 2. It is not included in the repository.

1. Install all required libraries (see above)
2. Run all cells in order - Section 2 will call the OECD and World Bank APIs and may take a minute
3. The Dash dashboard launches at `http://127.0.0.1:8050`

---

## Notebook Structure

| Section | Content |
|---------|---------|
| 1 | Introduction, group definitions, business questions |
| 2 | Data collection - World Bank API, OECD SDMX API, WHO GHO API, SQLite storage |
| 3 | Group classification and validation - 2019 pre-COVID baseline |
| 4 | EDA - registration trends, box plots, recession sensitivity, GDP scatter |
| 5 | Hypothesis testing - Kruskal-Wallis, Mann-Whitney U, Bonferroni correction |
| 6 | Economic indicator analysis - correlations by group, OLS regression |
| 7 | Random Forest by group and by country - feature importance comparison |
| 8 | Conclusions - summary table, key findings, limitations, future work |
| 9 | Interactive Dash dashboard |

---

## Dashboard

An interactive dashboard built with Plotly and Dash covers six tabs:

- **Tab 1 - World Map**: Plotly choropleth with countries colored by group
- **Tab 2 - Registrations**: Per-capita registration trends with group and country filters, recession shading
- **Tab 3 - Economic Indicators**: Toggle between GDP per capita and urbanization rate by country and group
- **Tab 4 - GDP vs Registrations**: Scatter plot of GDP per capita versus registration rate, 2005-2023
- **Tab 5 - Recession Sensitivity**: Per-country registration change during 2008-2009 and 2020 recessions
- **Tab 6 - Random Forest**: Feature importance by group (grouped bar chart)

---

## Known Limitations

- OECD TOCAPA covers passenger cars only - trucks, SUVs, and vans excluded. This systematically undercounts Group 1 markets and makes cross-group registration level comparison unreliable
- Group 1 contains only 3 countries - too few for reliable pooled modeling or recession sensitivity testing
- Annual data frequency limits modeling sophistication; monthly MSA-level data would enable ARIMA and more reliable per-country Random Forest
- Lower-income country groups excluded due to data unavailability - the research question cannot be addressed for those markets with free public data
- Per-country Random Forest yields negative $R^2$ for all 14 countries - 17 annual observations is insufficient for tree-based modeling

---

## Series Navigation

- [Part 1 - EDA & Linear Regression](../README_Part1.md)
- [Part 2 - Advanced Modeling](../README_Part2.md)
- [Part 3 - Regional Analysis](../README_Part3.md)
- **Part 4 - Global Comparison** *(this notebook)*
- Part 5 - EV Transition *(coming soon)*

---

*Data sources: OECD Short-Term Economic Indicators, World Bank Development Indicators, WHO Global Health Observatory. Analysis covers 14 countries across three market groups, 2005-2023.*
