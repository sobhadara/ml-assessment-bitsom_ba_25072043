# Retail Intelligence & Machine Learning Framework
## Graded Assessment: Data-Driven Retail Strategies

Welcome to the repository for the Machine Learning Fundamentals Graded Assessment. This project demonstrates an end-to-end data science workflow—from cleaning messy business data to building advanced "predictive brains" for retail optimization.

---

## 📂 Repository Structure

* **`q1_data_cleaning.ipynb`**: The "Digital Janitor" phase. Focused on fixing broken datasets.
* **`q2_unsupervised.ipynb`**: The "Pattern Finder." Using AI to group customers without being told how.
* **`q3_feature_engineering.ipynb`**: The "Sales Predictor." A high-tech pipeline to forecast shop inventory.
* **`part_b/business_analysis.md`**: The "Business Strategist." A deep dive into how these models actually make money.

---

## 🛠️ Project Highlights & Technical Specs

### 1. Data Sanitization (Q1)
Before we can use data, we have to fix it. This script handles:
* **The Missing Pieces:** Detecting empty spots in the data and filling them using the "median" (the middle-ground value) to avoid skewing results.
* **The Rule-Breakers:** Identifying and removing "Outliers"—those weird data points (like a $1,000,000 sale in a tiny shop) that would confuse the model.
* **The Translator:** Converting text categories into numbers so the math can actually process them.

### 2. Customer Segment Discovery (Q2)
We used **Unsupervised Learning** (specifically **K-Means Clustering**) to understand our customers.
* **The Elbow Method:** We didn't just guess how many groups we have; we used a mathematical "sweet spot" test to find the optimal number of customer personas.
* **PCA (Principal Component Analysis):** Since humans can't visualize 10 different variables at once, we used PCA to "squash" the data into a 2D map without losing the important patterns.

### 3. Sales Forecasting Pipeline (Q3)
This is a full **Regression Pipeline** designed to predict `items_sold`.
* **Time-Travel Protection:** We used a **Temporal Split**. We trained the model on the past to predict the "future" (the test set), ensuring we didn't accidentally leak future secrets into our training.
* **One-Stop-Shop Preprocessing:** We built a `ColumnTransformer` that scales numbers and encodes text in one automated move.
* **The Battle of the Models:** We compared **Linear Regression** (simple and fast) against **Random Forest** (complex and powerful) to see which one could handle the "messy" real-world sales patterns better.

### 4. Business Strategic Analysis (Part B)
This document translates our code into a boardroom-ready strategy.
* **Why Volume Matters:** We explain why counting **Items Sold** is a better success metric than total dollars.
* **Explaining the AI:** Using **SHAP values**, we explain exactly *why* a model recommends a specific promotion at a specific time (e.g., choosing Loyalty Points in December vs. Discounts in March).
* **The Long Game:** We outline a deployment plan, including "Model Drift" monitoring to ensure the AI doesn't get outdated as fashion trends change.

---

## 🚀 How to Run
1.  **Environment:** Ensure you have Python 3.x installed with `pandas`, `scikit-learn`, `seaborn`, and `matplotlib`.
2.  **Execution:** Run the `.ipynb` files in order. 
3.  **Data:** To get started, I’ve placed the raw data files in the 'Files' section on the left. The code is set up to pull them
    directly from there.

---
**Technical Note:** All models are built using `scikit-learn` pipelines to ensure the code is "production-ready"—meaning it’s easy to repeat, test, and deploy in a real business environment.
