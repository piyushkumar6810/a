# ⚡ Power Demand Forecasting

Predicts the **next hour's electricity demand (MW)** on the national grid using historical demand, weather, and economic data.

---

## 📁 Files Needed

Put these files in the same folder as the script:

```
power_demand_forecast.py
PGCB_date_power_demand.xlsx
weather_data.xlsx
economic_full_1.csv
```

---

## ▶️ How to Run

**1. Install dependencies**
```bash
pip install pandas numpy matplotlib xgboost scikit-learn openpyxl
```

**2. Run the script**
```bash
python power_demand_forecast.py
```

**3. Output**
- Prints `MAPE` and `MAE` scores in the terminal
- Saves two plots in the same folder:
  - `actual_vs_predicted.png` — first 7 days of 2024
  - `feature_importance.png` — top 15 features driving demand

---

## 🧠 How It Works (Simple Explanation)

| Step | What happens |
|---|---|
| **Load** | Reads demand, weather, and economic data |
| **Clean** | Fixes half-hourly timestamps, fills gaps, removes spikes |
| **Features** | Creates hour/day/month flags + lag & rolling demand values |
| **Split** | Train on 2015–2023, Test on 2024 (no shuffling) |
| **Model** | XGBoost regression (classical ML, no deep learning) |
| **Evaluate** | Reports MAPE — lower % = better |

---

## 📊 Key Features Used

- `lag_1h` to `lag_168h` — past demand values (1 hour ago up to 1 week ago)
- `roll_mean_24h` — average demand over last 24 hours
- `hour`, `month`, `is_weekend` — time of day / week / year
- `temp`, `humidity` — weather affects AC and heating load
- `gdp_usd`, `elec_access` — long-term economic growth signals

---

## 📈 Metric

**MAPE (Mean Absolute Percentage Error)**
- `< 5%` → Excellent
- `5–10%` → Good
- `> 10%` → Needs improvement

---

## 🗂️ Dataset Sources

| File | Description |
|---|---|
| `PGCB_date_power_demand.xlsx` | Hourly demand & generation data (target variable) |
| `weather_data.xlsx` | Hourly temperature, humidity, precipitation |
| `economic_full_1.csv` | Annual World Bank macroeconomic indicators |
