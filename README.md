
# Smart Factory Energy Prediction Challenge 
# ⚡ Equipment Energy Consumption Forecasting — A Time Series Journey 
## 📘 Problem Statement

The project started with a seemingly straightforward goal: **predict the energy consumption of equipment** using other recorded features such as humidity, wind speed, temperature, and more. At first glance, it looked like a classic regression task — clean the data, select a model, and tune it for performance.

But as I dove deeper, I began to realize: **this wasn’t just a regression problem. It was a time series problem in disguise**.

---

## 🧠 The Turning Point: Realizing It's Time-Based

I initially approached the problem using standard regression methods — trying out Random Forest and XGBoost directly on the raw features. But something felt off. The scores were poor, especially the R² score, which was close to zero or even negative.

That’s when I started questioning the structure of the dataset. I noticed columns like `year`, `month`, `day`, `hour`, and `minute` — the kind of features you see in **time series data**.

This changed everything.

It meant that the order of the data mattered. Observations were **not independent**; there was likely a pattern over time that the model was failing to capture. So I paused, stepped back, and began to study **time series modeling**.

---

## 📚 Exploring Classical Time Series Models

With this realization, I explored classical time series models:

* **ARIMA** and **SARIMA**: But these models assume univariate data or require heavy feature engineering for multivariate setups.
* **VAR (Vector AutoRegression)**: Better, but still not optimal. It made too many assumptions about stationarity and lag dependencies . 
* **I also explored classical multivariate time series models like VAR, but they quickly became computationally inefficient due to the size of the dataset (16,000+ rows × 30+ features). Since VAR models require estimating parameters for every combination of variable and lag, this led to a parameter explosion and sluggish performance — making them impractical for this use case.
---

## 🌲 Switching Gears: Why I Chose Random Forest Again

Instead of forcing the data into classical time series molds, I pivoted back to **Random Forest** and **XGBoost** — this time, with a **time-aware perspective**.

These models are:

* Robust to outliers
* Don’t require normalization or stationarity
* Great at capturing nonlinear interactions

But I knew this time I had to **transform the dataset to reflect its temporal nature** — otherwise, even the best model wouldn’t perform well.

---

## 🧹 Data Cleaning & Preprocessing

Here’s where the real engineering began.

### 🔴 Handling Negative Values

I noticed negative values in features like `humidity`, `wind speed`, etc. — which don’t make sense. Negative wind? Negative humidity?

I had two choices:

1. Assume it's a sign error and make them positive.
2. Drop the rows entirely.

Since these values made up only \~2% of the dataset, I chose to **remove them** rather than assume anything. For temperature (in Celsius), I allowed negative values.

### 🟡 Handling Missing Values

There were `NaN` values scattered throughout. I had two options again:

* **Forward-fill** (`ffill`) — makes sense because data is recorded every 10 minutes.
* **Median imputation** — simpler and less risky.

I used **median imputation**, and the model still performed well, but acknowledged that **forward-filling** could work better in production scenarios.

---

## 📊 Exploratory Data Analysis

To understand the nature of the data:

* I plotted features over time
* Checked for **seasonality**, **trends**, and **outliers**
* Verified that features had **low multicollinearity**, which is ideal for tree-based models

This gave me more confidence in the data — I knew it was clean, and I understood its behavior.

---

## 🧠 Feature Engineering: The Game-Changer

This was the most crucial step.

After realizing that the model lacked temporal awareness, I introduced:

### ⏱️ Lag Features

I added:

* `lag_1`: energy consumption from 10 minutes ago
* `lag_2`: from 20 minutes ago
* `lag_3`: from 30 minutes ago

This gave the model a **memory** of past consumption.

### 📈 Rolling Statistics

Then I added:

* `rolling_mean_3`: average of the last 3 time steps
* `rolling_std_3`: standard deviation of the last 3 steps

These features captured:

* **Trend (rolling mean)** — is it increasing or decreasing?
* **Volatility (rolling std)** — is it stable or fluctuating?

This combination made a **huge difference**.

> 🧪 Before: R² ≈ -0.01
> 🚀 After: R² ≈ **0.976**

---

## 🤖 Final Model and Results

### 📌 Model Used:

* **Random Forest Regressor**

### 🧪 Performance Metrics:

| Metric   | Score     |
| -------- | --------- |
| MSE      | 0.0001219 |
| R² Score | 0.9762    |

### 🔍 Feature Importance:

Time-based features (lags, rolling stats) were **far more important** than the original sensor features — showing how critical temporal awareness was.

---

## 💡 Insights & Learnings

* **Rolling mean and std aren’t redundant** — mean shows **trend**, std shows **volatility**
* Tree models like Random Forest can outperform ARIMA/SARIMA when features are well-designed
* Domain expertise is crucial — it helped me handle weird values and make the right assumptions
* EDA and feature engineering often matter **more than fancy algorithms**

---

## 🧠 What Worked

* Treating the problem as time-series instead of regression
* Using tree models instead of linear or ARIMA-based ones
* Engineering temporal features (lags, rolling stats)
* Carefully handling outliers and missing data

---

## 💥 What Didn’t Work
🗓️ Tried Date-Based Features (That Didn’t Help Much)
I engineered time-based features like:

Day of the week

Week of the month

Season of the year

But these features turned out to be ineffective for this dataset — likely because energy usage was more short-term reactive than cyclical on a calendar level.

* ARIMA/SARIMA/VAR: ineffective for this dataset
* Treating data as i.i.d. — led to poor scores
* Using only original features — lacked temporal signal


---

## 🧪 Final Thoughts

This project was a rollercoaster — it began as a regression task, evolved into a time series problem, and ended as a lesson in **how critical domain knowledge and feature engineering are**.

It was challenging, intense, and deeply satisfying to see the model improve step by step — and that final 97% R² score was earned through **insight**, not brute force.

