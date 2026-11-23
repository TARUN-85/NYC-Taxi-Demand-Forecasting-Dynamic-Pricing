# 🚕 NYC Yellow Taxi Demand Forecasting & Dynamic Pricing

## 📌 Project Overview

This project explores how Machine Learning can be used to forecast taxi demand across different pickup locations and time periods in New York City, and how demand forecasts can support operational planning and pricing strategy.

Using January 2024 NYC Yellow Taxi trip data, the project analyzes demand patterns, develops and compares forecasting models, generates an exploratory 24-hour demand forecast, and evaluates pricing scenarios through an elasticity-based simulation.

The goal is to bridge **Data Science, Machine Learning, and business decision-making**.

---

## 🎯 Business Problem

Taxi demand varies significantly by:

- Hour of the day
- Day of the week
- Pickup location

This variability creates operational challenges such as:

- Inefficient driver allocation
- Vehicle idle time
- Unmet customer demand
- Reduced fleet utilization
- Revenue optimization opportunities

### Business Objective

Predict taxi demand by hour and pickup location to support:

1. Demand forecasting
2. Proactive driver positioning
3. Fleet utilization planning
4. Identification of peak and low-demand periods
5. Exploration of dynamic pricing strategies

---

## 📊 Dataset

**Source:** NYC Yellow Taxi Trip Records

**Period:** January 2024

The dataset contains approximately **2.96 million pickup events**.

### Dataset Summary

| Metric | Value |
|---|---:|
| Pickup Events | 2,964,606 |
| Pickup Locations | 260 |
| Hours in January | 744 |
| Complete Hour-Location Records | 193,440 |
| Locations Selected for Modeling | 10 |

Demand was defined as the number of pickups occurring within each hour for each pickup location.

The dataset was transformed into a complete hourly time series across all pickup locations. Missing hour-location combinations were filled with zero demand.

The top 10 pickup locations were selected using the training period to avoid data leakage.

---

## 🔍 Exploratory Data Analysis

The analysis explored demand patterns across time and geography.

Key areas of investigation:

- Hourly demand patterns
- Day-of-week demand variations
- Demand concentration across pickup locations
- Peak and low-demand periods
- Location-specific demand patterns

### Key Insights

- Taxi demand follows a strong daily pattern.
- Demand is generally lowest during early morning hours.
- Demand increases throughout the morning and afternoon.
- Evening demand peaks around 6 PM.
- Demand patterns vary across pickup locations.
- Location-specific forecasting can provide more actionable insights than city-wide demand forecasting.

---

## 🤖 Machine Learning Approach

Three forecasting approaches were evaluated.

### 1. Weekly Seasonal Naive

Forecast demand using the demand observed during the same hour one week earlier.

This serves as a baseline model.

### 2. Day-Hour Pattern

Forecast demand using the historical average for each:

- Pickup location
- Day of week
- Hour of day

This provides an interpretable benchmark.

### 3. Random Forest Regressor

A global Random Forest model was trained across the selected 10 pickup locations.

#### Model Configuration

```python
RandomForestRegressor(
    n_estimators=300,
    min_samples_leaf=2,
    random_state=42,
    n_jobs=-1
)
```

The model was not hyperparameter-tuned.

---

## 🛠️ Feature Engineering

The Random Forest model used temporal, geographical, lag, and rolling features.

### Temporal Features

- Hour
- Day of week
- Weekend indicator

### Location Feature

- Pickup Location ID

### Lag Features

- Lag 1 hour
- Lag 24 hours
- Lag 168 hours

### Rolling Features

- 24-hour rolling mean
- 168-hour rolling mean

Rolling features were shifted to prevent target leakage.

---

## 📈 Model Evaluation

A chronological train-test split was used to simulate forecasting future demand.

| Dataset | Period |
|---|---|
| Training | January 1–24, 2024 |
| Testing | January 25–31, 2024 |

### Model Performance

| Model | MAE | RMSE | WAPE |
|---|---:|---:|---:|
| Weekly Seasonal Naive | 23.61 | 37.65 | 14.62% |
| Day-Hour Pattern | 27.23 | 40.83 | 16.86% |
| Random Forest | **22.55** | **34.59** | **13.96%** |

### Best Model

🏆 **Random Forest**

The Random Forest model achieved the best performance across all three evaluation metrics.

Compared with the Weekly Seasonal Naive baseline:

- MAE improved by approximately 4.5%
- RMSE improved by approximately 8.1%
- WAPE improved by 0.66 percentage points

Although the improvement was modest, the results demonstrate the value of incorporating temporal and demand-based features.

---

## 🔮 24-Hour Demand Forecast

The trained model was used to generate an exploratory demand forecast for the next 24 hours across the selected top 10 pickup locations.

### Forecast Insights

- Demand is lowest during early morning hours.
- Demand begins increasing around the morning hours.
- Demand remains elevated during daytime.
- Aggregate demand peaks around **6 PM**.
- Forecast demand varies significantly across pickup locations.

### Potential Operational Applications

- Proactive driver repositioning
- Location-based fleet planning
- Anticipating peak-demand periods
- Reducing idle capacity during low-demand periods

> ⚠️ The 24-hour forecast is exploratory and requires further validation before operational deployment.

---

## 💰 Dynamic Pricing Simulation

An elasticity-based pricing simulation was developed to explore how changes in price could affect demand and revenue.

### Assumptions

| Parameter | Value |
|---|---:|
| Base Fare | $15 |
| Base Demand | Forecast Demand |
| Assumed Price Elasticity | -1.5 |
| Price Multipliers | 0.8, 0.9, 1.0, 1.1, 1.2 |

Demand was simulated using a constant elasticity model:

\[
Q_{new}=Q_{base}\left(\frac{P_{new}}{P_{base}}\right)^\epsilon
\]

Revenue was calculated as:

\[
Revenue=P_{new}\times Q_{new}
\]

Where:

- \(Q\) = Demand
- \(P\) = Price
- \(\epsilon\) = Price Elasticity

### Pricing Scenario Results

| Price | Simulated Demand | Simulated Revenue | Revenue Change |
|---|---:|---:|---:|
| $12.00 | 62,149 | $745,783 | +11.80% |
| $13.50 | 52,084 | $703,131 | +5.41% |
| $15.00 | 44,470 | $667,049 | 0.00% |
| $16.50 | 38,546 | $636,006 | -4.65% |
| $18.00 | 33,829 | $608,929 | -8.71% |

Under the assumed elasticity of **-1.5**, lower prices resulted in higher simulated revenue because the modeled increase in demand outweighed the reduction in fare.

⚠️ These are **simulated outcomes, not observed revenue results**.

---

## 📉 Elasticity Sensitivity Analysis

The optimal pricing strategy changes depending on customer price sensitivity.

| Elasticity | Best Price | Simulated Revenue Gain |
|---|---|---:|
| -0.5 | $18 | +9.54% |
| -1.0 | Any tested price | 0% |
| -1.5 | $12 | +11.80% |
| -2.0 | $12 | +25.00% |

### Key Insight

There is no universally optimal pricing strategy.

Pricing decisions depend heavily on actual customer price sensitivity, which should be estimated using real data or controlled experiments rather than assumed.

---

## 💡 Business Recommendations

Based on the analysis:

- Forecast demand at a pickup-location and hourly level.
- Use demand forecasts to support proactive driver positioning.
- Identify peak and low-demand periods for fleet planning.
- Explore demand-aware pricing strategies.
- Validate price elasticity before implementing pricing changes.
- Consider driver availability, traffic, operational constraints, and customer experience.
- Conduct controlled A/B testing before deploying pricing strategies.

---

## ⚠️ Limitations

- Only one month of data was analyzed.
- The evaluation period was limited to one test week.
- External factors such as weather, traffic, holidays, and special events were not included.
- The price elasticity parameter was assumed rather than estimated from actual behavior.
- Pricing results are simulated, not observed.
- Supply and operational constraints were not modeled.
- The exploratory 24-hour forecasting approach requires separate validation.

---

## 🚀 Future Improvements

- Validate models across multiple months and seasons.
- Incorporate weather, holidays, events, traffic, and other external features.
- Explore additional forecasting models.
- Perform hyperparameter tuning.
- Investigate location-specific forecasting strategies.
- Estimate price elasticity using real historical data.
- Conduct controlled pricing experiments.
- Build a production-ready forecasting pipeline.
- Develop a dashboard for monitoring demand forecasts and operational KPIs.

---

## 🧰 Tech Stack

- Python
- Pandas
- NumPy
- Scikit-learn
- Matplotlib
- Seaborn
- Jupyter Notebook
- Machine Learning
- Time Series Analysis
- Exploratory Data Analysis
- Feature Engineering

---

## 📁 Repository Structure

```text
NYC-Taxi-Demand-Forecasting-Dynamic-Pricing/
│
├── README.md
├── requirements.txt
├── .gitignore
│
├── notebooks/
│   └── NYC_Taxi_Demand_Forecasting.ipynb
│
├── presentation/
│   └── NYC_Taxi_Case_Study.pdf
│
└── images/
    ├── demand_heatmap.png
    ├── model_comparison.png
    ├── demand_forecast.png
    └── pricing_sensitivity.png
```

---

## ▶️ How to Run

### 1. Clone the repository

```bash
git clone https://github.com/YOUR_USERNAME/NYC-Taxi-Demand-Forecasting-Dynamic-Pricing.git
```

### 2. Navigate to the project

```bash
cd NYC-Taxi-Demand-Forecasting-Dynamic-Pricing
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Open the notebook

```bash
jupyter notebook
```

Navigate to the `notebooks/` directory and open the notebook.

---

## 📌 Conclusion

This case study demonstrates an end-to-end Data Science workflow—from data preparation and exploratory analysis to forecasting, model evaluation, and business-oriented simulation.

The key takeaway is:

**Predict demand first. Optimize operations second. Validate pricing through experimentation.**

The project highlights how Machine Learning can support better decision-making while emphasizing the importance of model validation, assumptions, and business context.

---

## 👨‍💻 Author

**Tarun Kumar Raghav**

Data Science | Machine Learning | Generative AI

Connect with me on LinkedIn: [LinkedIn Profile](YOUR_LINKEDIN_URL)

---

⭐ If you find this project interesting, feel free to explore the notebook and share your feedback.
