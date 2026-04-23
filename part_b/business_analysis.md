# PART B: BUSINESS CASE ANALYSIS
## Scenario: Promotion Effectiveness at a Fashion Retail Chain

---

## B1. Setting the Strategy (Problem Formulation)

### (a) Defining the Goal
We are building a **Supervised Regression** model.
* **In plain English:** We are using a "Teacher-Student" approach (Supervised). We show the computer years of past sales data (the teacher) so it can learn to predict a specific number (the student), which in this case is exactly how many **items** we will sell.
* **The "Ingredients" (Features):** We give the model details about the store (size, location, nearby competition), the promotion being run, and the timing (is it a weekend or a festival?).

### (b) Why Count Items instead of Dollars?
We chose **Items Sold** as our target rather than total revenue. 
* **The Reason:** Imagine if prices go up due to inflation, but fewer people are actually buying. Revenue might look "good," but the promotion actually failed to attract customers. By focusing on volume, we get a "pure" look at whether a promotion actually makes people grab clothes off the racks, regardless of price changes. This is a principle called **Target Proxy Selection**—choosing the metric that best reflects real human behavior.

### (c) Local vs. Global Thinking
A junior analyst suggested one "Global" model for all stores. I propose a **Segmented Approach**.
* **The Reason:** People in a quiet rural town shop differently than people in a busy city center. A global model "averages" everyone out, making it mediocre for everyone. By grouping stores by location type (Urban vs. Rural), the model can learn the specific "rhythm" of each neighborhood.

---

## B2. Preparing the Evidence (Data & EDA)

### (a) Putting the Puzzle Together
Our data is currently scattered across four different files (Transactions, Store Info, Promotions, and a Calendar). 
* **The Process:** I will use a **Left Join**, using the sales transactions as the foundation and "pinning" the store info and promotion details to each sale.
* **The Final Snapshot:** We will summarize this so that **one row represents one store for one month.** We will total up the sales and count how many festival days happened in that month.

### (b) The "Look Before You Leap" Phase (EDA)
Before training the computer, we perform **Exploratory Data Analysis (EDA)**:
1.  **The Boost Check:** A bar chart to see which of the 5 promotions usually gives the biggest "lift" in sales.
2.  **The Calendar Check:** A line graph to see if sales naturally spike every December (seasonality).
3.  **The Rivalry Check:** A scatter plot to see if having a lot of competitors nearby makes our promotions less effective.
4.  **The Mix-and-Match Check:** A heatmap to see if big stores and specific promotions (like BOGO) are a "perfect match."
* **The Result:** If we see that festivals always cause a 50% jump in sales, we tell the model to pay extra attention to that "Festival" column.

### (c) The "Quiet Days" Problem
80% of our data shows "No Promotion." 
* **The Problem:** If the computer mostly sees quiet days, it will get lazy and assume every day is a quiet day. It will miss the "spikes" when a sale actually happens.
* **The Fix:** We use **Sample Weighting**. We tell the computer: "Pay five times more attention to the days when we actually ran a promotion." This forces it to learn what makes a sale successful.

---

## B3. Testing and Real-World Use

### (a) The "Time Machine" Test
To see if the model works, we don't just pick random days to test it. We use a **Temporal Split**.
* **The Process:** we train the model on the first 30 months of data and "hide" the last 6 months. We then ask the model to predict those last 6 months.
* **Why?** In the real world, you can't use tomorrow's news to predict yesterday's weather. A random split would let the model "cheat" by seeing future trends.
* **The Scorecard:** We use **MAE (Mean Absolute Error)**. This tells a manager: "On average, the computer's guess is off by 50 items." It's a number a human can actually use to order stock.

### (b) Explaining the "Why" (Feature Importance)
Why would the model suggest "Loyalty Points" in December but a "Flat Discount" in March for Store 12? 
* **The Investigation:** We use a tool called **SHAP values** to peak inside the model's brain.
* **The Logic:** In December, the model sees that it's a festival season. People are already at the mall; you don't need to give them a discount. You give them Loyalty Points to keep them coming back next year. In March, it's a "dead" month. The model knows people need a "Flat Discount" just to get them through the door.

### (c) Bringing it to Life (Deployment)
1.  **Saving:** We "freeze" the model into a file (using **joblib**) so it can be moved to the company's servers.
2.  **The Monthly Routine:** Every month, the system pulls the new calendar and store data, runs all 5 promotion scenarios, and picks the winner.
3.  **The Alarm System:** We monitor for **Model Drift**. If the model's guesses start becoming more than 15% wrong compared to real sales, an alarm goes off telling us it's time to **"re-train"** the model with the latest data.
