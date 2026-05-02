# U.S. Automobile Sales Analysis - Part 3: Regional Analysis

**Part 3 of the [Automobile Sales Analysis](../README.md) Series**

---

## Overview

Parts 1 and 2 established that U.S. automobile sales are strongly predicted by economic momentum at the national level, with Random Forest achieving mean CV R² of 0.436 using lagged sales as the dominant feature. Both parts treated the U.S. as a single homogeneous market.

Part 3 examines whether this picture holds when the market is disaggregated geographically. The central hypothesis is that transit access is a structural determinant of automobile market behavior — metro areas with robust public transportation should exhibit lower per-capita vehicle registrations, different recession sensitivity, and potentially different economic drivers of car ownership compared to car-dependent markets.

---

## Metro Selection

Nine metropolitan areas across three transit tiers, classified using 2019 FTA National Transit Database (NTD) Unlinked Passenger Trips (UPT) per capita. 2019 is used as the pre-COVID baseline — ridership collapsed 70-80% in 2020-2022 and does not reflect structural transit access.

| Tier | Metro | UPT/Capita (2019) | Profile |
|------|-------|-------------------|---------|
| 1 - Transit-Rich | New York-Newark | 206.9 | Subway-dominant; living without a car is common |
| 1 - Transit-Rich | Chicago | 61.1 | Legacy L train; dense urban core |
| 1 - Transit-Rich | San Francisco-Oakland | 134.4 | BART + Muni; tech-corridor density |
| 2 - Mid-Tier Transit | Cleveland-Elyria | 20.5 | Heavy rail + bus; Rust Belt mid-size |
| 2 - Mid-Tier Transit | Pittsburgh | 32.1 | Bus network; similar Rust Belt profile |
| 2 - Mid-Tier Transit | Milwaukee | 23.2 | Bus-dependent; Midwest mid-size |
| 3 - Car-Dependent | Des Moines | 10.0 | Minimal transit; flat and sprawling |
| 3 - Car-Dependent | Oklahoma City | 3.3 | Among least walkable large U.S. metros |
| 3 - Car-Dependent | Nashville | 6.1 | Rejected transit referendum; car-dependent by design |

**Tier cutoffs:** Tier 1 >= 40 UPT/capita, Tier 2 = 11-39, Tier 3 < 11

---

## Business Questions

1. Do transit-rich metro areas have lower per-capita vehicle registrations?
2. Are car-dependent metro areas more sensitive to recessions?
3. Does the Random Forest momentum pattern from Part 2 hold across all tiers?
4. Does Sun Belt growth outpace Rust Belt regardless of transit tier?
5. Do economic indicators predict registrations better in some tiers than others?

---

## Key Findings

### Hypothesis Testing

| Hypothesis | Result | Detail |
|-----------|--------|--------|
| Transit tier affects registrations | Reject H0 | Kruskal-Wallis H=67.7, p<0.001, eta2=0.41 (large effect) |
| All tier pairs significantly different | Significant | All three pairs pass Bonferroni-corrected Mann-Whitney U (p<0.0167) |
| Recession impact differs by tier | Fail to reject H0 | p=0.957 - test underpowered with n=3 per tier |
| Unemployment differs by tier | Reject H0 | Tier 1: 5.91% vs Tier 3: 4.27% (p<0.001) |

### OLS Regression - The Key Result

Adding transit tier to an economic-indicators-only model improves R² by **0.20** and renders both unemployment and income statistically non-significant. This is consistent with omitted variable bias - unemployment and income appeared to matter in the baseline model only because they were correlated with tier assignment. Transit access is the structural factor.

### Random Forest by Tier

| Tier | R² (test) | RMSE | Top Feature | Lag Importance |
|------|-----------|------|-------------|---------------|
| 1 - Transit-Rich | 0.947 | 0.026 | reg_pc_lag1 | 97% |
| 2 - Mid-Tier Transit | 0.902 | 0.017 | reg_pc_lag2 | 90% |
| 3 - Car-Dependent | 0.837 | 0.056 | reg_pc_lag1 | 97% |

Momentum dominates all three tiers, confirming Part 2's national finding holds regionally. Tier 2 shows unemployment beginning to register (6.7% importance) - the only tier where economic conditions meaningfully enter the model. R² drops progressively from Tier 1 to Tier 3, indicating the model fits transit-rich metros better.

Per-metro Random Forest models yield negative R² across all metros regardless of validation strategy, confirming that 21 annual observations per metro is insufficient for reliable single-metro modeling.

### EDA Summary

| Tier | Reg/Capita (median) | Unemployment (median) | Income (median) |
|------|--------------------|-----------------------|----------------|
| 1 - Transit-Rich | 0.768 | 5.48% | $55,382 |
| 2 - Mid-Tier Transit | 0.886 | 5.47% | $43,818 |
| 3 - Car-Dependent | 0.899 | 4.13% | $42,940 |

Per-capita registrations increase monotonically across tiers as expected. The income difference between Tier 1 and Tiers 2-3 is large ($55k vs $43-44k), driven almost entirely by San Francisco and New York. Within-tier economic conditions do not cleanly separate by tier.

---

## Data Sources

| Source | Data | Coverage | Access |
|--------|------|----------|--------|
| FRED API (BLS LAUS) | MSA unemployment rates | 1990-2024, monthly | fredapi |
| FRED API (BEA via FRED) | MSA per capita personal income | 1990-2023, annual | fredapi |
| FHWA Highway Statistics MV-1 | State motor vehicle registrations | 1998, 2002-2024, annual | scraped |
| Census Bureau via FRED | State population estimates | 1990-2024, annual | fredapi |
| FTA National Transit Database | UPT per capita by urbanized area | 2019 baseline | hardcoded |
| NBER via auto_sales.sqlite | Recession periods | From Part 1 | SQLite |

**Registration data note:** FHWA MV-1 reports state totals only, not MSA or county level. State registrations are used as a directional proxy for metro-level car dependency, normalized by state population. This dilutes the transit signal for states with mixed urban and rural populations (notably Illinois). MV-1 Excel files are available for 1998 and 2002-2024 only - years 1999-2001 are published as HTML tables and excluded.

---

## Interactive Dashboard

The notebook includes a **Dash dashboard** (`app.run(debug=True, port=8050)`) with five tabs:

- **Tab 1 - Metro Map**: Folium map of all 9 metros colored by transit tier, with popup showing key stats per metro
- **Tab 2 - Registrations**: Per-capita registration trends with tier and metro filters, recession shading
- **Tab 3 - Economic Indicators**: Toggle between unemployment and income time series by metro and tier
- **Tab 4 - Random Forest**: Feature importance by tier (grouped bar chart)
- **Tab 5 - Recession Sensitivity**: Per-metro registration change during 2008-2009 and 2020 recessions

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
openpyxl
xlrd
folium
dash
plotly
sqlite3 (standard library)
fredapi
```

Install missing libraries with `pip install <library_name>`.

---

## How to Run

> **Note:** This notebook reads from and writes to `auto_sales.sqlite` - the database created in Part 1. The file is not included in the repository. Run Part 1 first to generate the database, or copy `auto_sales.sqlite` from your Part 1 folder before proceeding.

> **Note:** A FRED API key is required. Register for a free key at https://fred.stlouisfed.org and add it to `config.py` alongside your existing key.

1. Ensure `auto_sales.sqlite` is present in the same directory as this notebook
2. Ensure `config.py` contains a valid `FRED_API_KEY`
3. Install all required libraries (see above)
4. Run all cells in order - Section 2 will scrape FHWA data and may take a few minutes
5. The Dash dashboard launches at `http://127.0.0.1:8050`

---

## Notebook Structure

| Section | Content |
|---------|---------|
| 1 | Introduction, hypothesis, metro selection, business questions |
| 2 | Data collection - FRED unemployment and income, FHWA scrape, population, SQLite storage |
| 3 | Transit tier classification using 2019 NTD data |
| 4 | EDA - registration trends, tier distributions, recession sensitivity, Sun Belt vs Rust Belt, economic indicators |
| 5 | Hypothesis testing - Kruskal-Wallis, Mann-Whitney U, Bonferroni correction |
| 6 | Economic indicator analysis - correlations by tier, OLS regression with and without tier controls |
| 7 | Random Forest by tier and by metro - feature importance comparison, cross-validation |
| 8 | Conclusions - summary table, key findings, limitations, future work |
| 9 | Interactive Dash dashboard |

---

## Known Limitations

- Registration data is state-level, not MSA-level. State totals dilute the transit signal - Illinois includes Chicago's urban core and car-dependent downstate and suburban areas simultaneously
- NY state registration data represents New York-Newark (Tier 1). Cleveland-Elyria (OH) was selected as the Tier 2 Rust Belt representative specifically to avoid state-level collision with New York
- Annual data frequency limits modeling sophistication. Monthly MSA-level registration data would enable ARIMA and more reliable per-metro Random Forest
- The recession sensitivity hypothesis test is underpowered with n=3 metros per tier. Failure to reject H0 does not confirm equivalence across tiers
- Transit tier classification uses 2019 NTD pre-COVID baseline. COVID collapsed ridership 70-80% in 2020-2022, which does not reflect structural transit access
- Repeated measures structure (18+ observations per metro) inflates effective sample size in hypothesis tests. True independent sample size is 9 metros
- County-to-MSA crosswalk using Census CBSA delineation files would enable true MSA-level registration data and resolve the state dilution problem - identified as the primary enhancement for future work

---

## Future Work

- **County-to-MSA crosswalk**: Census CBSA delineation files map every county to its MSA. Aggregating FHWA county-level data to MSA would resolve the state dilution limitation and allow direct MSA-level registration analysis
- **Monthly MSA data**: Would enable ARIMA modeling and reliable per-metro Random Forest
- Part 4: International comparison - USA vs. Europe vs. developing markets
- Part 5: EV transition analysis - how electrification reshapes demand across transit tiers
- Part 6: Geopolitical supply chain disruptions - chips, tariffs, COVID

---

## Series Navigation

- [Part 1 - EDA & Linear Regression](../README_Part1.md)
- [Part 2 - Advanced Modeling](../README_Part2.md)
- **Part 3 - Regional Analysis** *(this notebook)*
- Part 4 - International Comparison *(coming soon)*

---

*Data sources: Federal Reserve Economic Data (FRED), FHWA Highway Statistics, FTA National Transit Database. Analysis covers 9 U.S. metropolitan areas, 2002-2024.*
