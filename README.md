# Rainfall Time Series Forecasting

## Project Overview

This project develops a time series forecasting solution using historical rainfall data to predict future rainfall patterns.

The project addresses two main business objectives:

1. Forecast monthly total rainfall for the next 12 months.
2. Forecast the maximum rainfall occurring in a single day for each future month and identify the month expected to have the highest maximum-daily rainfall.

The dataset contains monthly rainfall observations from **January 1982 to June 2020**.

The project includes comprehensive data understanding, data-quality validation, exploratory data analysis, time-series analysis, feature engineering, baseline forecasting, machine learning models, SARIMA modeling, hyperparameter experiments, final holdout testing, and rolling-origin robustness evaluation.

---

## Business Problem

The objective of the project is to:

> Develop a Machine Learning model to predict rainfall for the next one year using historical data and forecast the highest rainfall occurring in a day for a single month.

The problem was divided into two forecasting tasks:

### Objective 1
Predict monthly total rainfall for the next 12 months.

### Objective 2
Predict monthly maximum-daily rainfall and identify the month expected to contain the highest single-day rainfall during the forecast period.

---

## Dataset

The project uses three monthly rainfall datasets:

- Monthly Total Rainfall
- Monthly Maximum Daily Rainfall
- Monthly Number of Rain Days

### Dataset Characteristics

- **Time period:** January 1982 – June 2020
- **Frequency:** Monthly
- **Total observations:** 462 months
- **Missing months:** 0
- **Duplicate months:** 0
- **Missing values:** 0

The datasets were merged using the monthly date variable to create a single time-series dataset.

### Final Variables

| Variable | Description |
|---|---|
| `month` | Monthly date |
| `total_rainfall` | Total rainfall recorded during the month |
| `maximum_rainfall_in_a_day` | Highest rainfall recorded in a single day during the month |
| `no_of_rainy_days` | Number of rainy days during the month |

---

## Data Quality Checks

Several checks were performed before modeling:

- Missing-value verification
- Duplicate-row detection
- Duplicate-month detection
- Chronological ordering verification
- Missing-month detection
- Negative rainfall checks
- Rainy-day range validation
- Alignment verification across all three datasets

The final dataset contained a continuous monthly sequence with no missing months.

Potential extreme rainfall observations were retained because they represent meaningful weather events rather than automatically being treated as data errors.

---

## Exploratory Data Analysis

EDA was performed to understand rainfall distributions, variability, seasonal patterns, relationships, and extreme events.

### Monthly Total Rainfall

- Mean: **176.24 mm**
- Median: **158.45 mm**
- Minimum: **0.20 mm**
- Maximum: **765.90 mm**

### Maximum Daily Rainfall

- Mean: **52.36 mm**
- Median: **43.45 mm**
- Minimum: **0.20 mm**
- Maximum: **216.20 mm**

### Number of Rainy Days

- Mean: **13.96 days**
- Median: **14 days**
- Minimum: **1 day**
- Maximum: **27 days**

Both rainfall-amount variables showed right-skewed distributions, reflecting occasional extreme rainfall events.

---

## Seasonal Rainfall Patterns

Calendar-month analysis revealed clear annual seasonality.

Historically, higher average monthly total rainfall was observed during:

- November
- December
- January

December had the highest historical average monthly total rainfall.

February showed comparatively lower average rainfall.

These patterns motivated the use of a **12-month seasonal period** in the time-series models.

---

## Correlation Analysis

The following Pearson correlations were observed:

| Variables | Correlation |
|---|---:|
| Total Rainfall vs Maximum Daily Rainfall | 0.803 |
| Total Rainfall vs Number of Rainy Days | 0.702 |
| Maximum Daily Rainfall vs Number of Rainy Days | 0.411 |

Monthly total rainfall therefore showed strong positive association with maximum daily rainfall and a substantial positive relationship with the number of rainy days.

Contemporaneous future rainfall variables were not used as same-month predictors because they would not be available at forecast time and could introduce data leakage.

---

## Time Series Analysis

Several statistical time-series techniques were used.

### Seasonal Decomposition

Additive seasonal decomposition with a period of 12 months revealed:

- Clear annual seasonality
- Gradual changes in the trend component
- Irregular residual rainfall extremes

December showed the strongest positive seasonal contribution to monthly total rainfall.

### Augmented Dickey-Fuller Test

For monthly total rainfall:

- ADF Statistic: **-5.3168**
- p-value: **0.000005**

The null hypothesis of a unit root was rejected under the test specification.

For maximum daily rainfall:

- ADF Statistic: **-4.9623**
- p-value: **0.000026**

The null hypothesis was also rejected.

### ACF and PACF

The ACF and PACF showed meaningful dependence around the **12-month lag**, supporting the presence of annual seasonal structure.

---

## Feature Engineering

For machine learning models, historical and calendar-based features were created.

### Calendar Features

- Month
- Quarter
- Month sine transformation
- Month cosine transformation

### Lag Features

- Lag 1
- Lag 2
- Lag 3
- Lag 6
- Lag 12
- Lag 24

### Rolling Features

- 3-month rolling mean
- 6-month rolling mean
- 12-month rolling mean
- 12-month rolling standard deviation

Rolling features were calculated only from previously available observations using a shift before rolling calculations to prevent future-data leakage.

After feature engineering and removal of rows unavailable because of historical lags, the modeling dataset contained **438 observations**.

---

## Chronological Data Splitting

Random train-test splitting was intentionally avoided because this is a time-series forecasting problem.

The data was split chronologically into:

### Training Set
January 1984 – June 2018

### Validation Set
July 2018 – June 2019

### Test Set
July 2019 – June 2020

The final test period remained untouched during model development and model selection.

---

## Baseline Models

Two baseline forecasting approaches were evaluated:

### Naive Forecast
Uses the most recently observed rainfall value as the forecast.

### Seasonal Naive Forecast
Uses rainfall from the same month of the previous year.

Baseline models provided reference performance against which more advanced models could be evaluated.

---

## Models Evaluated

The project evaluated multiple forecasting approaches:

- Naive Forecast
- Seasonal Naive Forecast
- SARIMA
- Random Forest Regressor
- Gradient Boosting Regressor

Hyperparameter experiments were also performed for the machine learning models.

---

## Monthly Total Rainfall — Validation Results

| Model | MAE (mm) | RMSE (mm) |
|---|---:|---:|
| **SARIMA** | **52.39** | **66.33** |
| Naive | 60.33 | 77.39 |
| Tuned Gradient Boosting | 69.44 | 85.77 |
| Initial Gradient Boosting | 76.54 | 89.84 |
| Initial Random Forest | 69.63 | 91.23 |
| Tuned Random Forest | 71.67 | 91.92 |
| Seasonal Naive | 82.13 | 106.43 |

The selected model was:

**SARIMA(1,0,1)(1,0,0,12)**

It achieved the strongest validation performance among the evaluated models.

Its validation RMSE improved by approximately:

- **14.29%** compared with the Naive baseline
- **37.68%** compared with the Seasonal Naive baseline

---

## Machine Learning Experiments

### Random Forest

The initial Random Forest showed a substantial difference between training and validation performance, indicating overfitting.

A hyperparameter experiment using different tree depths and minimum leaf sizes reduced the training-validation gap but did not improve validation performance beyond the initial model.

This result was retained and reported rather than selecting a model based only on training performance.

### Gradient Boosting

Gradient Boosting was also evaluated and tuned.

The tuned Gradient Boosting model achieved:

- Validation MAE: **69.44 mm**
- Validation RMSE: **85.77 mm**

This improved over the initial Gradient Boosting model but remained behind SARIMA on the validation period.

---

## Final Holdout Test — Monthly Total Rainfall

After model selection, the selected SARIMA model was retrained using all data available before the final test period.

### Final Test Performance

- MAE: **89.47 mm**
- RMSE: **112.93 mm**

Compared with validation performance:

- Validation MAE: **52.39 mm**
- Validation RMSE: **66.33 mm**

The decrease in performance on the unseen test period demonstrates the difficulty of forecasting unusual rainfall conditions.

One of the largest errors occurred in **December 2019**, when actual rainfall was substantially higher than the model forecast.

This result was retained as an important limitation rather than being hidden.

---

## Rolling-Origin Robustness Evaluation

To evaluate performance beyond a single validation period, rolling-origin evaluation was performed across five historical 12-month forecasting windows.

### Monthly Total Rainfall

- Average MAE: **68.30 mm**
- Average RMSE: **83.28 mm**
- RMSE standard deviation: **23.14 mm**
- Best RMSE: **57.38 mm**
- Worst RMSE: **112.93 mm**

The results show meaningful year-to-year variation in forecasting performance.

### Maximum Daily Rainfall

- Average MAE: **18.48 mm**
- Average RMSE: **21.58 mm**
- RMSE standard deviation: **3.89 mm**
- Best RMSE: **17.15 mm**
- Worst RMSE: **26.15 mm**

The maximum-daily rainfall forecasts showed lower year-to-year performance variability within their own target scale.

A numerical convergence warning occurred during at least one rolling SARIMA fit and is acknowledged as a limitation.

---

# Final Forecast Results

## Objective 1 — Forecast Monthly Total Rainfall

The selected SARIMA model was refitted using all available observations through June 2020 and used to forecast the next 12 months.

### Forecast Period
**July 2020 – June 2021**

| Month | Forecast Total Rainfall (mm) |
|---|---:|
| Jul 2020 | 125.35 |
| Aug 2020 | 125.21 |
| Sep 2020 | 128.01 |
| Oct 2020 | 167.75 |
| Nov 2020 | 157.53 |
| **Dec 2020** | **230.88** |
| Jan 2021 | 144.79 |
| Feb 2021 | 138.71 |
| Mar 2021 | 149.98 |
| Apr 2021 | 170.40 |
| May 2021 | 187.82 |
| Jun 2021 | 182.15 |

### Key Results

**Forecast rainfall across the 12 monthly point forecasts: 1908.60 mm**

**Average monthly forecast: 159.05 mm**

**Highest monthly total rainfall forecast: December 2020 — 230.88 mm**

---

## Objective 2 — Forecast Maximum Daily Rainfall

The model was also used to forecast the maximum rainfall expected to occur in a single day within each future month.

| Month | Forecast Maximum Daily Rainfall (mm) |
|---|---:|
| Jul 2020 | 38.97 |
| Aug 2020 | 38.50 |
| Sep 2020 | 40.52 |
| **Oct 2020** | **52.79** |
| Nov 2020 | 49.72 |
| Dec 2020 | 49.83 |
| Jan 2021 | 43.94 |
| Feb 2021 | 41.61 |
| Mar 2021 | 45.42 |
| Apr 2021 | 46.11 |
| May 2021 | 46.41 |
| Jun 2021 | 44.32 |

### Key Result

**October 2020 is forecast to have the highest monthly maximum-daily rainfall during the 12-month forecast horizon, with a predicted value of 52.79 mm.**

This represents the forecast maximum rainfall occurring in a single day within that month. It does **not** predict the exact calendar day on which the rainfall will occur.

---

## Key Business Insights

The project identified several useful rainfall patterns:

- Rainfall exhibits meaningful annual seasonality.
- November, December, and January historically tend to have higher monthly rainfall.
- Extreme rainfall events remain substantially harder to predict than regular seasonal patterns.
- December 2020 has the highest forecast monthly total rainfall.
- October 2020 has the highest forecast monthly maximum-daily rainfall.
- Monthly total rainfall and maximum daily rainfall measure different aspects of rainfall intensity and accumulation.

Forecasts should therefore be used as planning estimates rather than guarantees of exact rainfall amounts.

---

## Model Limitations

The project has several important limitations:

1. Extreme rainfall events are inherently difficult to forecast accurately.
2. Forecast intervals are relatively wide, indicating substantial uncertainty.
3. Model performance varies across different historical years.
4. The dataset contains monthly observations from a single climate station.
5. External meteorological predictors were not included.
6. Statistical forecast intervals can produce negative lower bounds even though rainfall cannot physically be negative.
7. At least one rolling SARIMA fit generated a numerical convergence warning.
8. Monthly maximum-daily forecasting identifies the expected magnitude within a month but not the exact day of occurrence.

---

## Future Improvements

Potential improvements include:

- Incorporating temperature, humidity, wind, atmospheric pressure, and other meteorological variables
- Exploring SARIMAX with suitable external predictors
- Performing broader time-series cross-validation for model selection
- Investigating probabilistic forecasting approaches
- Exploring extreme-value models for rare rainfall events
- Evaluating ensemble forecasting approaches
- Incorporating rainfall observations from additional stations
- Regularly retraining the model as new rainfall data becomes available

---

## Technologies Used

- Python
- Google Colab
- Pandas
- NumPy
- Matplotlib
- Seaborn
- Statsmodels
- Scikit-learn

---

## Project Workflow

```text
Data Collection
      ↓
Metadata Understanding
      ↓
Data Quality Validation
      ↓
Data Merging
      ↓
Exploratory Data Analysis
      ↓
Seasonality & Trend Analysis
      ↓
Stationarity Testing
      ↓
ACF / PACF Analysis
      ↓
Feature Engineering
      ↓
Chronological Train / Validation / Test Split
      ↓
Baseline Forecasting
      ↓
SARIMA Modeling
      ↓
Random Forest
      ↓
Gradient Boosting
      ↓
Hyperparameter Experiments
      ↓
Model Comparison
      ↓
Final Holdout Testing
      ↓
Rolling-Origin Robustness Evaluation
      ↓
12-Month Future Forecast
      ↓
Business Interpretation
```

---

## Repository Contents

```text
Rainfall-Time-Series-Forecasting/
│
├── Rainfall_Time_Series_Forecasting.ipynb
└── README.md
```

The Jupyter notebook contains the complete end-to-end implementation, analysis, model evaluation, visualizations, and forecasting results.

---

## How to Run the Project

1. Download or clone this repository.
2. Open `Rainfall_Time_Series_Forecasting.ipynb` in Google Colab or Jupyter Notebook.
3. Install/import the required Python libraries.
4. Run the notebook cells sequentially from top to bottom.
5. The notebook performs data loading, preprocessing, EDA, modeling, evaluation, and forecasting.

---

## Conclusion

This project developed an end-to-end rainfall forecasting pipeline using historical monthly rainfall data from January 1982 to June 2020.

SARIMA provided the strongest validation performance for monthly total rainfall among the evaluated approaches. The final 12-month forecast from July 2020 through June 2021 produced **1908.60 mm** across the monthly point forecasts, with **December 2020** having the highest predicted monthly total rainfall at **230.88 mm**.

For the second forecasting objective, **October 2020** was predicted to have the highest maximum rainfall occurring in a single day, at **52.79 mm**.

Final holdout and rolling-origin evaluations also demonstrated that forecast accuracy varies across years, particularly during unusual or extreme rainfall periods. Therefore, the forecasts are best interpreted together with their uncertainty and used as decision-support estimates rather than exact guarantees.
