🛍️ Customer Shopping Behavior Analysis
An end-to-end data analytics portfolio project using Python, SQL, and Power BI to uncover customer purchase patterns, loyalty segments, and revenue drivers from retail transaction data.

📌 Project Overview
This project simulates a corporate-grade, end-to-end data analytics workflow — the kind of work a professional data analyst does daily. Starting from a raw retail dataset, the project moves through data cleaning, exploratory analysis, SQL-based business querying, interactive dashboard creation, and stakeholder reporting.

The goal is to transform 3,900 rows of raw customer shopping data into actionable business intelligence — revealing which customer segments drive the most revenue, how loyalty and discounts affect spending, and which products deserve more marketing attention.

This project is ideal for:

📊 Data analyst aspirants building a portfolio for interviews and LinkedIn
📚 Anyone learning Python, SQL, and Power BI together in one project
💼 Professionals preparing for data analyst, business analyst, or product analyst roles
🧩 Business Problem
A retail company wants to understand its customers better. They have transaction-level data but no clear picture of:

Which customer segments (by age, gender, loyalty) generate the most revenue?
Do subscribed customers actually spend more than non-subscribers?
Which products have the highest discount dependency — and is that a margin risk?
Are frequent shoppers more likely to subscribe?
What are the top-performing products within each category?
Without answers to these questions, the marketing and product teams are making decisions based on gut feel rather than data. This project provides those answers.

🎯 Objectives
#	Objective
1	Clean and prepare raw retail data for analysis using Python and pandas
2	Engineer new features (age group, purchase frequency in days) to enable richer analysis
3	Load the cleaned dataset into a relational SQL database
4	Answer 10 key business questions using SQL queries of increasing complexity
5	Build an interactive Power BI dashboard for non-technical stakeholders
6	Deliver a written project report and presentation with findings and recommendations
🛠️ Tech Stack
Tool	Purpose
Python 3	Data cleaning, EDA, feature engineering
pandas	DataFrame manipulation and transformation
SQLAlchemy	Python-to-SQL database connector
PostgreSQL / MySQL / MS SQL Server	Relational database for storing and querying data
SQL	Business analysis queries (aggregations, subqueries, CTEs, window functions)
Power BI Desktop	Interactive dashboard and data visualization
Jupyter Notebook	Python development environment
Gamma AI	AI-assisted presentation slide generation
🔄 Project Workflow
Raw CSV Data
     │
     ▼
[Phase 1] Python — EDA & Cleaning
  • Load dataset with pandas
  • Inspect shape, types, null values
  • Impute missing Review Ratings (group-wise median)
  • Rename columns to snake_case
  • Engineer age_group column (pd.qcut)
  • Engineer purchase_frequency_days column (.map)
  • Drop redundant promo_code_used column
     │
     ▼
[Phase 2] SQL Database
  • Connect Python to database via SQLAlchemy
  • Load cleaned DataFrame into "customer" table
  • Run 10 business analysis queries
     │
     ▼
[Phase 3] Power BI Dashboard
  • Connect Power BI to SQL database
  • Build interactive visuals (bar charts, KPIs, slicers)
  • Write DAX measures for dynamic metrics
     │
     ▼
[Phase 4] Report & Presentation
  • Write project report (findings + recommendations)
  • Build stakeholder presentation deck
📂 Dataset Information
File: customer_shopping_behavior.csv
Rows: 3,900 | Columns: 18 (raw) → 19 (after feature engineering, minus 1 dropped)

Column	Type	Description
Customer ID	int	Unique customer identifier
Age	int	Customer age (18–70)
Gender	text	Male / Female
Item Purchased	text	Product name
Category	text	Clothing, Footwear, Outerwear, Accessories
Purchase Amount (USD)	int	Transaction value ($20–$100, avg $59.76)
Location	text	US state
Size	text	S / M / L / XL
Color	text	Product color
Season	text	Spring / Summer / Fall / Winter
Review Rating	float	2.5–5.0 (37 missing values — imputed)
Subscription Status	text	Yes / No
Shipping Type	text	Standard, Express, Free Shipping, Next Day Air, 2-Day, Store Pickup
Discount Applied	text	Yes / No
Promo Code Used	text	Yes / No (identical to Discount Applied — dropped)
Previous Purchases	int	Count of past purchases (1–50)
Payment Method	text	Cash, Credit Card, Debit Card, Venmo, PayPal, Bank Transfer
Frequency of Purchases	text	Weekly, Fortnightly, Monthly, Quarterly, Annually, etc.
Engineered columns added in Python:

New Column	Logic
age_group	Quartile-based age buckets: Young Adult / Adult / Middle-aged / Senior
purchase_frequency_days	Numeric mapping of frequency text to days (Weekly → 7, Monthly → 30, etc.)
📁 Folder Structure
customer-trends-data-analysis-SQL-Python-PowerBI/
│
├── customer_shopping_behavior.csv          # Raw dataset
├── Customer_Shopping_Behavior_Analysis.ipynb  # Python: EDA, cleaning, DB load
├── customer_behavior_sql_queries.sql       # 10 SQL business analysis queries
├── customer_behavior_dashboard.pbix        # Power BI dashboard file
├── Customer Shopping Behavior Analysis.pdf # Project report
├── Customer-Shopping-Behavior-Analysis.pptx  # Stakeholder presentation
├── Business Problem Document.pdf           # Business context document
└── README.md                               # This file
🐍 Python Analysis (Jupyter Notebook)
The notebook Customer_Shopping_Behavior_Analysis.ipynb covers the full data preparation pipeline:

1. Data loading & first inspection

import pandas as pd
df = pd.read_csv('customer_shopping_behavior.csv')
df.head()       # preview rows
df.info()       # column types and null counts
df.describe()   # statistical summary
2. Null value handling — group-wise median imputation

# Fill missing Review Ratings with the median of each product Category
df['Review Rating'] = df.groupby('Category')['Review Rating'] \
    .transform(lambda x: x.fillna(x.median()))
Only Review Rating had 37 missing values (< 1% of data). Group-wise imputation preserves category-level rating distributions rather than biasing all nulls toward one global average.

3. Column standardisation

df.columns = df.columns.str.lower().str.replace(' ', '_')
df = df.rename(columns={'purchase_amount_(usd)': 'purchase_amount'})
4. Feature engineering

# Age segmentation into 4 equal-sized quartile buckets
df['age_group'] = pd.qcut(df['age'], q=4,
                           labels=['Young Adult', 'Adult', 'Middle-aged', 'Senior'])

# Convert purchase frequency text to numeric days
frequency_mapping = {
    'Weekly': 7, 'Fortnightly': 14, 'Bi-Weekly': 14,
    'Monthly': 30, 'Quarterly': 90, 'Every 3 Months': 90, 'Annually': 365
}
df['purchase_frequency_days'] = df['frequency_of_purchases'].map(frequency_mapping)
5. Remove redundant column

# discount_applied and promo_code_used are 100% identical — confirmed before dropping
assert (df['discount_applied'] == df['promo_code_used']).all()
df = df.drop('promo_code_used', axis=1)
6. Load into SQL database

from sqlalchemy import create_engine
engine = create_engine("postgresql+psycopg2://user:password@localhost:5432/customer_behavior")
df.to_sql("customer", engine, if_exists="replace", index=False)
🗄️ SQL Analysis
File: customer_behavior_sql_queries.sql

10 business questions answered with SQL, progressing from basic aggregations to advanced window functions:

#	Question	SQL Concepts Used
Q1	Total revenue by gender	GROUP BY, SUM
Q2	Discount users who still spent above average	Subquery, AVG, WHERE
Q3	Top 5 products by average review rating	GROUP BY, AVG, ORDER BY, LIMIT
Q4	Average spend: Standard vs Express shipping	IN clause, GROUP BY
Q5	Subscriber vs non-subscriber: spend and revenue	Multiple aggregations, COUNT, SUM, AVG
Q6	Products with highest discount rate	CASE WHEN, conditional aggregation, percentage formula
Q7	Customer segmentation (New / Returning / Loyal)	CTE (WITH clause), CASE WHEN, BETWEEN
Q8	Top 3 products per category	Window function: ROW_NUMBER() OVER (PARTITION BY)
Q9	Do repeat buyers (> 5 purchases) tend to subscribe?	WHERE filter, COUNT, GROUP BY
Q10	Revenue contribution by age group	SUM, GROUP BY on engineered column
Example — Q7: Customer segmentation using a CTE

WITH customer_type AS (
  SELECT customer_id, previous_purchases,
    CASE
      WHEN previous_purchases = 1 THEN 'New'
      WHEN previous_purchases BETWEEN 2 AND 10 THEN 'Returning'
      ELSE 'Loyal'
    END AS customer_segment
  FROM customer
)
SELECT customer_segment, COUNT(*) AS "Number of Customers"
FROM customer_type
GROUP BY customer_segment;
Example — Q8: Top 3 products per category using a window function

WITH item_counts AS (
  SELECT category, item_purchased,
         COUNT(customer_id) AS total_orders,
         ROW_NUMBER() OVER (PARTITION BY category ORDER BY COUNT(customer_id) DESC) AS item_rank
  FROM customer
  GROUP BY category, item_purchased
)
SELECT item_rank, category, item_purchased, total_orders
FROM item_counts
WHERE item_rank <= 3;
📊 Power BI Dashboard
File: customer_behavior_dashboard.pbix

The dashboard connects directly to the SQL database and provides an interactive view of all key metrics. Stakeholders can filter by Season, Category, and Subscription Status using slicers.

Dashboard components:

Visual	Shows
KPI Cards	Total Revenue, Average Purchase Amount, Total Customers, Subscriber Count
Bar Chart	Revenue by Gender, Revenue by Age Group, Top Products by Category
Donut Chart	Subscription Status split, Discount Applied split
Table	Top products with ratings and order counts
Slicers	Season, Category, Subscription Status, Shipping Type
Sample DAX measures used:

Total Revenue = SUM(customer[purchase_amount])
Avg Spend = AVERAGE(customer[purchase_amount])
Subscriber Rate = DIVIDE(
    COUNTROWS(FILTER(customer, customer[subscription_status] = "Yes")),
    COUNTROWS(customer)
)
💡 Key Insights
Male customers dominate by volume (68% of transactions) but female customers should be analysed for per-transaction spend differences before drawing marketing conclusions.

Subscribers are a minority (27%) but likely high-value — Q5 reveals whether this segment punches above its weight in total revenue, justifying investment in subscriber acquisition.

Discount dependency varies widely by product — Q6 identifies products where 70–80%+ of purchases involved a discount, flagging a potential margin risk that the merchandising team should address.

Loyal customers (11+ previous purchases) coexist with non-subscribers — Q9 shows that repeat buyers don't automatically subscribe, indicating the subscription value proposition isn't reaching them. A targeted upsell campaign to customers with 5+ purchases is a quick win.

Category-level winners are consistent — Q8 reveals the top 3 products in each of the four categories (Clothing, Footwear, Accessories, Outerwear), giving the buying team clear data on what to stock more of.

Age groups show distinct revenue contributions — Q10 combined with Q1 can answer which age-gender combination is most profitable, enabling precise audience targeting in paid media.

🚀 Future Enhancements
 Add RFM (Recency, Frequency, Monetary) scoring to build a proper customer value model
 Build a churn prediction model using scikit-learn based on purchase frequency decline signals
 Add time-series analysis if a transaction date column becomes available (monthly revenue trends, seasonality)
 Create a product recommendation engine using collaborative filtering
 Automate the Python → SQL pipeline with Apache Airflow or a scheduled script
 Publish the Power BI dashboard to Power BI Service for online sharing
👤 Author
Maddepuri Ganesh

Aspiring Data Analyst | Python | SQL | Power BI | Data Visualization

💻 GitHub: https://github.com/Mganesh-creator
💼 LinkedIn: https://www.linkedin.com/in/maddepuri-ganesh-4226912aa/
📧 Email: ganeshmaddepuri@gmail.com
📜 License
MIT — free to fork, star ⭐, and use in your own portfolio. If this helped you, a star on the repo goes a long way!