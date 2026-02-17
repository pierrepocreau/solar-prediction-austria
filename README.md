# Solar Power Prediction Austria

This project implements a short-term forecasting model (2-hour horizon) for aggregated solar power generation in Austria. It analyses the relationship between meteorological forecasts and grid feed-in behaviour, comparing gross generation baselines against actual net feed-in data estimations provided by APG.

## Project Overview

The goal was to predict country-wide solar energy production using a gradient boosting approach. The project is divided into two phases:

1. **Baseline & Gross Generation:** Analysing historical gross generation (2015-2020) to establish a performance baseline using population-weighted weather data.
2. **Net Feed-In Prediction:** Forecasting grid feed-in (2020-2022) using granular weather forecast points and solar geometry.

## Data Sources

* **Net Feed-in Data (Target):** 15-minute resolution power plant feed-in data for Austria (2020-2022).
* *Note:* This data, obtained from the [APG (Austrian Power Grid) Transparency Platform](https://markt.apg.at/en/transparency/generation/generation-per-production-type/), is an **estimation** based on upscaling representative metering points, rather than a total measurement of all systems.


* **Weather Forecasts:** Historical forecast data (Temperature, Shortwave Radiation, Wind Speed) obtained via the [Open-Meteo API](https://open-meteo.com/).
* **Gross Generation (Baseline):** Aggregated solar generation provided by ENTSO-E (via Open Power System Data) for the 2015-2020 period.

## Methodology

### 1. Feature Engineering

Domain-specific features were engineered to improve upon raw weather data:

* **Solar features:** Manually calculated **Solar Elevation** and **Azimuth** angles for Austria's centroid. This proved to be the most critical feature, outperforming raw time-of-day variables.

### 2. Modelling Strategy

* **Algorithm:** XGBoost Regressor.
* **Validation:** Utilised **Walk-Forward Validation** (Time Series Cross-Validation) to prevent data leakage and simulate a production environment.
* *Training Window:* 1 year (rolling)
* *Test Window:* 1 month



## Key Results & Analysis

### Gross vs. Net Generation

* **Gross Generation (Baseline):** Achieved an **NRMSE of ~2.2%**. This data follows physical laws closely and is easier to predict.
* **Net Feed-in (Final Model):** Achieved an **NRMSE of ~2.79%**.

### Context: The COVID-19 Period

The Net Feed-in dataset covers the years 2020-2022. This represents a special time influenced by the COVID-19 pandemic. The anomalies in human behaviour, energy consumption, and load profiles during lockdowns likely introduced noise into the feed-in estimations that purely meteorological models cannot fully capture.

## Future Improvements

* **Capacity-Weighted Weather:** Instead of using population-weighted or uniform weather grids, the weather input should be tailored to **major solar production areas**. Weighting meteorological data by installed PV capacity per region would significantly reduce noise and align forecasts with actual generation centres.
* **Net Feed-in Decomposition:** Net feed-in is physically defined as `Gross Production - Local Consumption`. Since consumption profiles fluctuate heavily (especially during anomalies like COVID-19), explicitly **estimating consumption** and subtracting it from a gross production model could yield better results than forecasting net feed-in directly.
* **Data Quality:** Access to precise metering data (ground truth) rather than APG estimations would be the single most effective step to validate model performance.

## Project Structure

* `01_Baseline_Gross_Generation.ipynb`: Initial EDA, correlation analysis, and baseline modelling on Gross Generation data.
* `02_Net_Feed_In.ipynb`: End-to-end pipeline for APG Net Feed-in data, including solar position calculation and walk-forward validation.
* `data/`: Contains the CSVs for weather and energy production.

## Installation & Usage

This project uses `uv` for dependency management.

```bash
# Clone the repository
git clone https://github.com/yourusername/solar-prediction-austria.git
cd solar-prediction-austria

# Install dependencies (using uv or pip)
uv sync

# Run the notebooks
jupyter notebook

```
