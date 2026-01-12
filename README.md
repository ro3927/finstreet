# **Adaptive Bayesian Quantitative Strategy**

### *A Probabilistic Approach to Systematic Trading*

This repository contains a full-scale quantitative trading pipeline designed for the Indian Equity Market. Unlike traditional "fixed-rule" strategies, this system uses **Bayesian Ridge Regression** to dynamically update its beliefs about market direction based on incoming data.

---

## **The Strategy Philosophy**

The core of this strategy is the **Bayesian Information Ratio (IR)**.

Most models only predict a "price target." Our model predicts both a **Mean ()** (expected return) and **Uncertainty ()** (standard deviation of the prediction). We only enter a trade when the model is mathematically "confident"—meaning the expected return is significantly higher than the uncertainty.

### **Key Features**

* **Adaptive Learning:** The model uses a "Walk-Forward" window, re-training itself every single day to stay current with market regimes.
* **Multivariate Alpha:** It processes a "cockpit" of indicators including Momentum, Volatility (Parkinson & EWMA), RSI, MACD, and Bollinger Band Width.
* **Smart Risk Management:** It automatically scales position sizes down during high-volatility events and ignores redundant data through Spearman Correlation filtering.

---

## **Project Structure**

The notebook is organized into five logical modules:

### **1. Data Acquisition (`fyers-apiv3`)**

* Programmatically fetches daily OHLCV bars for symbols like `SONATSOFTW`, `IRCON`, and `RITES`.
* **Timezone Alignment:** Raw API data arrives in UTC epoch format; we localize it to **Asia/Kolkata (IST)** to align with NSE market sessions (09:15 to 15:30).

### **2. Feature Engineering**

We transform raw price data into 7+ technical signals:

* **Trend:** Log Returns and 5-Day Momentum.
* **Mean Reversion:** RSI (Relative Strength Index).
* **Volatility:** Parkinson High-Low estimator (more efficient than close-to-close returns).
* **Regime:** Bollinger Band Width (detecting "squeezes" before breakouts).

### **3. Statistical Auditing (Feature Selection)**

To prevent "Overfitting" (teaching the model to memorize noise), we run a **Spearman Rank Correlation** filter. If two indicators are more than 70% similar (like Parkinson Vol vs. Standard Deviation), we drop the redundant one to keep the model lean.

### **4. The Bayesian Inference Engine**

We use `BayesianRidge` to perform daily predictions.

* **Signal Rule:** `Signal = Predicted Mean / Predicted Std`.
* **Decision:**
* **BUY** if Signal > Dynamic Threshold.
* **SHORT** if Signal < -Dynamic Threshold.
* **CASH** if the model is uncertain.



### **5. Backtesting & Benchmarking**

The strategy is audited against a **Buy & Hold Benchmark**. We include real-world constraints:

* **Slippage:** 10 bps (0.10%) per trade to account for taxes and brokerage.
* **Position Sizing:** Maximum 75% exposure, scaled by the stock's volatility.

---

## **Performance Visuals**

The system generates two critical plots:

1. **Feature Heatmap:** Shows how the indicators interact and where redundancy was removed.
2. **Equity Curve:** A direct comparison between the Bayesian Strategy and the Benchmark, highlighting **Alpha Generation**.

---

## **How to Run**

1. **Get Credentials:** You need a `client_id` and `access_token` from the **Fyers API Dashboard**.
2. **Configure:** Set your `SYMBOL` and `INITIAL_CAPITAL` in the configuration block.
3. **Install Dependencies:**
```bash
pip install fyers-apiv3 pandas scikit-learn seaborn matplotlib

```


4. **Execute:** Run the cells sequentially. The model will automatically create a `/data` folder to store historical CSVs.

---



**Author:** Rohan | IIT Guwahati
**Event:** FinStreet Quant Challenge 2026
