Redfin Homeowner Affordability Analysis

When, where, and what type of home can a typical household actually afford?

This project analyzes Redfin's Home Affordability Tracker (2023–2026) to find the best months to buy, compare affordability across states and property types, track how home prices change year over year, and predict whether a housing market is affordable using logistic regression.

Team: Tara Rajagopalan & Justin Oliver Course: AAI-500, M.S. in Applied Artificial Intelligence, University of San Diego

Key Findings
- Buy in winter. January and February have the fewest homes sold and the highest share of affordable listings, which means less competition for affordable homes. May through August are the least favorable months.
- Affordability can be predicted. A logistic regression model predicts whether a market is affordable with 95% accuracy and 0.99 ROC-AUC.
- Income alone doesn't explain affordability. Across states, median household income explains only about 3% of the variation in affordable listing share (r = −0.18, R² = 0.03).
- Location creates a huge gap. Only 22.2% of listings are affordable in Montana, compared with 78.8% in Oklahoma.
- Starter homes are the fastest path to ownership. A typical household needs about 5.5 years to save for a starter home down payment, compared with 8.4 years for a single-family home.
- Smaller markets are more volatile. Condos and townhouses show sharp year-over-year price swings because fewer of them sell. Single-family and starter homes change more gradually.

Data

- Source: Redfin Data Center: Home Affordability Tracker Income group: Homeowner Years: 2023–2026 Property types: Single Family, Townhouse, Condo, Starter Home

- Each row is a unique combination of month, property type, state, and down payment tier (3.5%, 5%, 10%, 15%, or 20%).

- Variable categories:

  - Income: median household income
  - Home price: median sale price, year-over-year % change
  - Affordability and payment: share of income spent on housing, monthly payment, down payment amount, years to save
  - Market activity: homes sold, affordable listing share


Questions of Interest
1) Which months are ideal for buying a home?
2) How does seasonality differ by property type?
3) How affordable are homes relative to each state's median household income?
4) Which states are the most and least affordable?
5) How has the average median year-over-year % change in property cost evolved?
6) Can logistic regression predict whether a market is affordable?

Data Cleaning & Preparation
- Merged 4 property-type datasets into one combined dataset (43,260 rows, 42 columns)
- Converted date columns to datetime and engineered year, month, quarter, and season features
- Converted percentage columns to 0–1 proportions for easier comparison
- Grouped states into regions (Pacific Northwest, Southwest, Southeast, Midwest, Northeast, Mountain West, Non-Contiguous)
- Created the target variable affordable, following Redfin's definition: housing is affordable when ≤30% of income goes to housing costs
- Removed Puerto Rico, which was missing all income and affordability data (42,655 rows remaining)
- Checked correlations for multicollinearity and found a 63% / 37% class imbalance in the target

Data Quality Fix: Townhouse Price Anomaly

- The first year-over-year plot showed townhouse prices jumping more than 150%, which wasn't realistic. A boxplot of townhouse sale prices revealed 40 Louisiana records with prices as low as $1,975, most likely data-entry errors. Those periods, plus the matching periods one year later that were used in the year-over-year calculation, were excluded from the YoY analysis.

Analysis

Seasonality: 

- Calculated the average homes sold and average affordable listing share for each month from 2023 to 2026.
- Best months: January and February, with low buyer activity and many affordable listings
- Worst months: May through August, with peak buyer activity and fewer affordable listings. This lines up with summer breaks, when families with children tend to move.
- Highest affordable listing share by property type: starter homes. Lowest: single-family homes. Winter is the most affordable season.

  Year-Over-Year % Change in Property Cost:
  - Calculated the average median sale price YoY % change by property type across all states over time.
  - Townhouses and condos: fewer sales, so drastic YoY swings
  - Single-family and starter homes: more sales, so gradual YoY change

  Logistic Regression Model
  - Question: Is the housing market affordable at a given time, for a given property type, in a given state, at a given down payment percentage?
  - Target: affordable (1 = ≤30% of income on housing, 0 = >30%)
  - Predictors: Down Payment %, Month, Region, Median Household Income, Median Sale Price
  - Preprocessing:
    - Dropped 380 rows with missing sale price or target (~0.9% of the data)
    - One-hot encoded Month and Region, with one reference category dropped
    - Stratified 80/20 train/test split to preserve the 63/37 class balance
    - Balanced class weights so the model isn't biased toward the majority class
    - Standardized predictors so coefficients are comparable

    Results:
    - Accuracy = 0.95
    - F1 = 0.96
    - Precision = 0.97
    - Recall = 0.94
    - ROC-AUC = 0.99

    Full Vs. Partial Model:
    - Full: All 5 predictors
        - Accuracy = 0.95
        - ROC-AUC = 0.99

    - Partial: Sale Price + Household Income only
        - Accuracy = 0.89
        - ROC-AUC = 0.963

Median sale price and median household income are the strongest drivers. Adding down payment %, month, and region still improves performance.

Limitation: The model uses the median household income of the destination state, so it works best for buyers moving within a state. Someone moving from Texas to California would be compared against California incomes, not their own.




Log-Normal Distribution Check

Monetary variables such as sale price, monthly payment, mortgage payment, and income required are right-skewed. A log transformation makes them approximately normal, which supports using log-scale models and percentage-based interpretations in future work.

Recommendations
- Buyers: Shop in January–February, consider starter homes to cut savings time, and look at states with high affordable listing shares (e.g., Oklahoma, Minnesota, Louisiana).
- Policymakers in high-cost states (e.g., Montana, California, Hawaii): Income explains little of the affordability gap, so supply constraints, zoning, and local demand deserve closer attention.

Tools
- Python
- pandas
- scikit-learn
- matplotlib / seaborn

Reference

Redfin. (2026). Methodology. https://www.redfin.com/news/data-center/methodology/#affordability
