# Great Barrier Reef Tourism Forecasting

**Tools:** Python · Scikit-learn · Pandas · Matplotlib  
**Domain:** Climate Analytics · Tourism Economics · Regression  
**Type:** Time Series Forecasting
---

## Overview

This project models the relationship between coral bleaching events at the Great Barrier Reef (GBR) and tourism demand in Australia. The motivation is straightforward — the GBR is one of Australia's most significant tourism assets, and coral bleaching driven by rising sea temperatures poses a direct threat to visitation numbers and regional economies.

The objective was to build a regression model (Time Series) that could forecast overnight tourism trips based on environmental and economic indicators, and validate it against official government forecasts for 2018.

---

## Data Sources

| Dataset | Source | Description |
|---|---|---|
| Coral bleaching data | NOAA / Reef Check | Historical bleaching severity, sea surface temperature anomalies, DHW values by site and year |
| Tourism visitations | Australian Trade and Investment Commission | Annual overnight trips by management area |
| Economic indicators | World Bank | GDP data used as a demand-side control variable |

**Key variables:**
- `AvgTSA` — Average thermal stress anomaly
- `SSTA` / `SSTA_DHW` — Sea surface temperature anomaly and degree heating weeks
- `AvgCyclone_Frequency` — Cyclone frequency in the reef area
- `GDP` — Economic indicator for tourism demand
- `GSP` — Gross state product for Queensland
- `Quarters` — Seasonal effects

---

## Methodology

### 1. Exploratory Data Analysis
- Merged bleaching data with tourism visitation records by year
- Calculated average environmental metrics per year
- Identified multicollinearity between several climate variables

### 2. Baseline Model
- Simple average prediction (always predict the mean) — MAE of 236.08
- Linear Regression with forward stepwise feature selection — selected `GDP`, `GSP`, `AvgTSA` — MAE of 180.97

### 3. Model Comparison

Three regularized regression models were tested:

**Lasso (degree 2 polynomial features):**
- Best alpha: 54.29
- Total features: 136
- Non-zero coefficients: 10
- Key retained features: AvgTSA, GDP, interaction terms between cyclone frequency and GSP

**Lasso (degree 1):**
- Non-zero coefficients: 2
- Retained only AvgTSA and GDP — the most parsimonious model

**Ridge:**
- Best alpha: 79.06
- All 15 features retained with varying coefficients
- Seasonal quarters had significant coefficients (Q3: +20.5, Q2: -17.8)

### 4. Key Feature Relationships

From the Lasso degree 1 model:
- `GDP coefficient: +180.1` — Higher economic output is positively associated with tourism demand
- `AvgTSA coefficient: -45.1` — Higher thermal stress anomalies are negatively associated with tourism — as bleaching worsens, visitation declines

This makes intuitive sense: tourists are less likely to visit a reef that is visibly degraded.

---

## Model Validation Against 2018 Forecast

A held-out 2018 dataset was provided with expected climate metrics (from NOAA) and an official tourism forecast (from Australian Trade and Investment Commission). The models were evaluated against this benchmark:

| Quarter | Official Forecast | Model Prediction |
|---|---|---|
| Q1 | 4,721 | 4,265 |
| Q2 | 4,744 | 4,599 |
| Q3 | 5,440 | 4,736 |
| Q4 | 5,091 | 4,647 |
| **Total** | **19,996** | **18,247** |

| Benchmark | % Difference from 2017 |
|---|---|
| Official forecast | +8.74% |
| Model prediction | -0.77% |

The model predicted within 0.77% of the 2017 baseline, compared to the official forecast's 8.74% optimistic projection. Whether the model is more accurate than the official forecast will only be known with actual 2018 data, but the model's conservative estimate reflects the negative trend in bleaching severity.

---

## Key Findings

1. Sea surface temperature anomaly (AvgTSA) is the strongest environmental predictor of tourism decline — selected in every model.
2. GDP is the dominant demand-side driver — economic conditions matter more than environmental ones in aggregate, but bleaching provides the directional signal.
3. Seasonality matters — Q3 (July–September) is the peak tourism quarter, Q2 the weakest. Any forecasting model should account for this.
4. The degree-2 Lasso model captured interaction effects (cyclone frequency × GSP) that the simpler models missed — suggesting that the relationship between bleaching and tourism is not purely linear.
5. The model's 2018 prediction is more conservative than official estimates — this may reflect that standard tourism forecasts do not adequately account for environmental degradation.

---

## Limitations

- Annual aggregation loses within-year variation — quarterly or monthly models would be more precise
- Tourism data covers all of Australia, not just GBR-specific visitors — introduces noise
- The bleaching dataset has geographic gaps in coverage across years
- 2018 actual visitation data was not available to fully validate the model

---
