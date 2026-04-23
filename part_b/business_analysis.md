# ================================================
# MACHINE LEARNING FUNDAMENTALS — GRADED ASSESSMENT
# PART B: BUSINESS CASE ANALYSIS
# SCENARIO: PROMOTION EFFECTIVENESS AT A FASHION RETAIL CHAIN
# ================================================

# ---------------------------------------------------------------------
# TASK B1: PROBLEM FORMULATION
# ---------------------------------------------------------------------

### **Task Brief:**
In this section, we define the core strategy for the Machine Learning project. 
We identify the goal (Target), the data needed (Features), and the type of 
mathematical approach required. We also justify the selection of volume 
metrics over revenue and propose a strategy for handling diverse store locations.

---

### (a) ML Problem Definition
* **Target Variable:** `items_sold` (The specific quantity of items purchased).
* **Candidate Input Features:**
    * *Store Attributes:* `store_id`, `store_size`, `location_type`, `competition_density`.
    * *Promotion Details:* `promotion_type`.
    * *Temporal Data:* `is_weekend`, `is_festival`, `month`.
* **ML Problem Type:** **Supervised Learning – Regression**.
* **Justification:** We are using historical data (Supervised) to predict a specific, continuous numerical quantity (Regression). This allows the business to estimate exactly how many units will sell under different promotional scenarios.

### (b) Target Variable Selection: Volume vs. Revenue
* **Why Volume (Items Sold) is better:** Revenue is often skewed by price changes, inflation, or the specific cost of an item. A promotion might be highly effective at clearing inventory (Volume) even if the total dollar amount (Revenue) is lower due to discounts. `items_sold` provides a "cleaner" signal of customer interest and promotional "pull."
* **Broader Principle:** This illustrates **Target Proxy Optimization**. In real-world projects, the chosen target must be stable and directly linked to the behavior you want to influence (demand), rather than accounting metrics affected by external pricing shifts.

### (c) Alternative Modeling Strategy: Location-Based Segmentation
* **Proposed Strategy:** **Clustered Modeling Approach**.
* **Justification:** Instead of one global model, I propose training specialized models for each `location_type` (Urban, Semi-Urban, Rural). 
* **Reasoning:** Consumer behavior in rural areas is fundamentally different from urban centers. A "global" model often averages these differences out, leading to mediocre predictions for everyone. Segmented models allow the algorithm to learn the specific "flavor" of promotion response in each region.

# ---------------------------------------------------------------------
# TASK B2: DATA AND EDA STRATEGY
# ---------------------------------------------------------------------

### **Task Brief:**
Before training, we must clean and explore the data. This involves merging 
disparate tables and performing Exploratory Data Analysis (EDA) to understand 
trends. We also address the challenge of "data imbalance" where promotions 
only happen 20% of the time.

---

### (a) Data Joining and Aggregation
* **Joining Process:** I would perform a "Left Join" using **Transactions** as the primary table. I would connect **Store Attributes** via `store_id`, **Promotion Details** via `promotion_id`, and the **Calendar** via `date`.
* **Data Grain:** The final grain will be **One Row per Store, per Month, per Promotion**.
* **Aggregations:** Since raw data is at the individual transaction level, I would sum the total `items_sold` and aggregate calendar flags (like `total_festivals_in_month`) to create a consistent monthly snapshot for each store.

### (b) EDA (Exploratory Data Analysis) Plan
1.  **Promotion Lift Analysis (Bar Chart):** Comparing average sales across the 5 promotion types to identify high-performing baseline offers.
2.  **Seasonality Check (Line Chart):** Plotting monthly sales over three years to confirm if peaks (like December) are consistent across all store types.
3.  **Competition vs. Sales (Scatter Plot):** Checking if stores in high-density areas are less sensitive to promotions.
4.  **Feature Interaction (Heatmap):** Looking for correlations between `store_size` and `promotion_type` to see if large stores prefer specific deals.
* **Influence:** If the EDA reveals that "Festival months" cause a 50% jump regardless of promotion, I will weight the `is_festival` feature more heavily in the final model.

### (c) Handling Data Imbalance (80% No Promotion)
* **The Problem:** If the model mostly sees "No Promotion" data, it will get very good at predicting normal sales but fail to understand the "spike" caused by a BOGO or Discount.
* **Proposed Steps:** 1. **Feature Engineering:** Create a strong binary `is_promo_active` flag. 2. **Oversampling:** During training, I would give higher "weight" to the rows where a promotion occurred, forcing the model to prioritize learning those specific patterns.

# ---------------------------------------------------------------------
# TASK B3: MODEL EVALUATION AND DEPLOYMENT
# ---------------------------------------------------------------------

### **Task Brief:**
Finalizing the model requires a rigorous testing strategy and a plan for 
real-world use. We explain the "why" behind model decisions to stakeholders 
and set up a deployment pipeline that keeps the model accurate over time.

---

### (a) Train-Test Split and Metrics
* **Split Strategy:** **Temporal (Time-Ordered) Split**. I would use the first 30 months for training and the final 6 months for testing.
* **Why not Random?** Random splits lead to "Data Leakage." In retail, predicting June based on July data is unrealistic. We must test the model's ability to look forward in time.
* **Metrics:** **MAE (Mean Absolute Error)**. 
    * *Interpretation:* It tells managers, "On average, our prediction is off by X items." This is more intuitive for inventory stocking than abstract squared errors.

### (b) Investigating Model Decisions (Feature Importance)
* **The Scenario:** Why recommend "Loyalty Points" in Dec and "Flat Discount" in March for Store 12?
* **Investigation:** I would use **Feature Importance (SHAP values)** to show that in December, the `is_festival` feature was high, making customers less price-sensitive and more interested in long-term points. In March, the `items_sold` baseline was low, triggering the model to recommend a "Flat Discount" to drive urgent footfall.
* **Communication:** I would tell the Marketing Team: "The model recognizes that holiday shoppers don't need a discount to buy, but spring shoppers need a price incentive to enter the store."

### (c) End-to-End Deployment Process
1.  **Serialization:** Save the trained model using `joblib` for easy loading.
2.  **Automated Data Prep:** Create a monthly pipeline that pulls the upcoming month's calendar and store data.
3.  **Inference:** Run all 5 promotion scenarios through the model and output the one with the highest predicted `items_sold`.
4.  **Monitoring & Retraining:** Monitor the "Model Drift." If the actual sales start deviating from predictions by more than 15%, the system will send an alert to retrain the model with the latest historical data to capture new market trends.
