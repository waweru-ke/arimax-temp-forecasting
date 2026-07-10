# Nyeri Climate Forecasting Benchmark
> **A comparative time-series framework evaluating Holt-Winters Exponential Smoothing, ARIMAX, and Automated Pipeline models on regional ERA5 temperature data.**

---

## 📌 Project Overview
This repository contains a rigorous, data-driven time-series analysis and forecasting framework focused on regional climate data for **Nyeri, Kenya**. Using historical single-levels timeseries data from the **ERA5 reanalysis dataset** spanning from **2000 to March 2026** (comprising over 229,000 hourly observations), this project benchmarks classical statistical smoothing models against advanced automated autoregressive architectures (`pmdarima` pipelines) to evaluate their predictive accuracy on regional surface temperature variations.

---

## 📊 Visualizing the Climate Data

### 1. Historical Temperature Trends (2000 - 2026)
The framework ingests raw hourly temperatures, converts them from Kelvin to Celsius, and aggregates them into clean daily (`temp_daily`) and monthly (`temp`) resampled aggregate dataframes to capture systemic seasonal behaviors over a 26-year horizon.

![Daily timeseries of Nyeri Temperature](path/to/your/daily_timeseries_plot.png)

### 2. Time-Series Decomposition & Stationarity Testing
To determine the optimal differencing parameters required for autoregressive processing, the data was subjected to both **Augmented Dickey-Fuller (ADF)** and **KPSS** tests:
* **ADF Test Statistic:** `-3.82` (p-value: `0.00`) $\rightarrow$ Rejects the null hypothesis of a unit root (Stationary).
* **KPSS Test Statistic:** `0.64` (p-value: `0.02`) $\rightarrow$ Rejects the null hypothesis of stationarity around a constant trend.

Because the tests presented contrasting indications, programmatic log-differencing and autocorrelation plotting (ACF/PACF) were deployed to accurately stabilize variances.

---

## 🔬 The Forecasting Tournament & Critical Findings

A rolling-window validation strategy was applied by partitioning the dataset, reserving a **12-month test window** (`TEST_LENGTH = 12`) to evaluate out-of-sample forecasts.

The project evaluates four distinct modeling paradigms:

### 1. Simple Exponential Smoothing (SES) $\rightarrow$ *The Flat Forecast Phenomenon*
* **The Finding:** When attempting to apply `SimpleExpSmoothing` to the monthly temperature aggregates, the resulting forecasts produced a completely **flat linear projection**. 
* **The Diagnosis:** Because SES possesses no trend or seasonal components (`smoothing_level=1.0`), it computes forecasts as a static weighted average of past levels. This served as a structural baseline proving that classical single-parameter smoothing is completely inadequate for modeling highly oscillatory geographic weather systems.

### 2. Holt-Winters Exponential Smoothing
* **Approach:** Upgraded the smoothing infrastructure to an `ETS(ANA)` structure (Additive Error, No Trend, Additive Seasonality) to capture the cyclical movements of the Nyeri climate.

### 3. Classical ARIMAX vs. Automated `auto_arima` Pipelines
* **Approach:** Programmatic modeling using automated grid searches to minimize information criteria (AIC/BIC), testing raw `ARIMA(1,1,1)` and `ARIMA(2,1,2)` structures against custom integrated preprocessing pipelines.

---

## 📈 Benchmark Performance & Model Evaluation
Models were strictly evaluated on the unseen test matrix using **Mean Absolute Percentage Error (MAPE)**. 

| Forecasting Model Architecture | Out-of-Sample MAPE (%) | Optimal Selection Summary / Status |
| :--- | :---: | :--- |
| **Simple Exponential Smoothing (SES)** | *Flat Baseline* | Inadequate for climate dynamics |
| **Holt-Winters ETS (ANA)** | `Log-Likelihood: -68.25` | Baseline Seasonal Model |
| **Standard `auto-ARIMAX`** | **`3.14%`** | Secondary baseline autoregressive profile |
| **Automated ARIMA Pipeline (`auto-pipe`)** | **`1.21%`** | **Winner** – Superior generalization across historical lags |

---

## 🛠️ Tech Stack & Engineering Pipeline
* **Language:** Python 3.x
* **Core Data Infrastructure:** `pandas`, `numpy`
* **Time-Series Engines:** `statsmodels.tsa.holtwinters`, `pmdarima`
* **Visualization Layer:** `matplotlib.pyplot`, `seaborn`
