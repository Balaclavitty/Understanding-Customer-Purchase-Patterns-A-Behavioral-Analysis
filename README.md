# 🛍️ Customer Shopping Behavior Analysis (Python • SQL • Power BI)

## ⭐ Project Highlights 
- Cleaned and transformed **3,900 retail transactions** using Python (missing values, standardization, feature engineering).
- Built **customer segmentation logic (New / Returning / Loyal)** and analyzed subscription + discount behavior using PostgreSQL.
- Developed an interactive **Power BI dashboard** with KPIs and filters for revenue, sales, and demographic trends.
- Delivered actionable recommendations to improve **subscription growth, loyalty strategy, and discount optimization**.

---

## 📌 Business Problem Statement
A leading retail company wants to better understand its customers’ shopping behavior in order to improve **sales**, **customer satisfaction**, and **long-term loyalty**. Management has noticed changes in purchasing patterns across demographics, product categories, and shopping preferences.

The company is particularly interested in uncovering which factors—such as **discount usage, review ratings, customer loyalty, and subscription behavior**—drive purchasing decisions and repeat purchases.

### Main Business Question
**How can the company leverage consumer shopping data to identify trends, improve customer engagement, and optimize marketing and product strategies?**

---

## 🎯 Project Overview
This project analyzes customer shopping behavior using transactional data from **3,900 purchases** across multiple product categories. The goal is to uncover insights into:

- Customer spending patterns  
- Product category performance  
- Discount-driven purchasing behavior  
- Subscription and loyalty trends  
- Revenue contribution across demographics  

The project follows an end-to-end workflow:

- **Python** (EDA + Cleaning + Feature Engineering)  
- **PostgreSQL** (Business Analysis Queries)  
- **Power BI** (Dashboard & Visualization)  
- **Business Report & Recommendations**  

---

## 📊 Dataset Summary
- **Rows:** 3,900  
- **Columns:** 18  
- **Missing values:** 37 missing values in the `review_rating` column  

### Key Features
- Customer demographics (Age, Gender, Location, Subscription Status)  
- Purchase details (Category, Item Purchased, Purchase Amount, Season, Size, Color)  
- Behavior indicators (Discount Applied, Previous Purchases, Frequency of Purchases, Shipping Type, Review Rating)  

---

## 🛠️ Tools & Technologies
- **Python:** pandas, numpy, matplotlib, seaborn  
- **SQL:** PostgreSQL  
- **Visualization:** Power BI  
- **Notebook:** Jupyter  

---

## 🔍 Data Cleaning & Feature Engineering (Python)

### Handling Missing Review Ratings
Missing review ratings were imputed using the **median review rating per product category**:

```
df["review_rating"] = df.groupby("category")["review_rating"].transform(
    lambda x: x.fillna(x.median())
)
```

### Standardizing Column Names
To improve readability and database integration, columns were renamed into **snake_case**:

```
df.columns = (
    df.columns.str.strip()
              .str.lower()
              .str.replace(" ", "_")
)
```

### Feature Engineering: Age Group Segmentation
Customer age was grouped into meaningful segments for analysis:

```
bins = [0, 25, 35, 50, 100]
labels = ["Young Adult", "Adult", "Middle-aged", "Senior"]

df["age_group"] = pd.cut(df["age"], bins=bins, labels=labels)
```
### Loading Data into PostgreSQL
After cleaning, the dataset was loaded into PostgreSQL for SQL-based analysis:

```
from sqlalchemy import create_engine

engine = create_engine("postgresql://username:password@localhost:5432/customer_db")
df.to_sql("customer", engine, if_exists="replace", index=False)
```
## 🧾 SQL Business Analysis (Selected Queries)

### 1️⃣ Subscribers vs Non-Subscribers (Revenue + Average Spend)

```
SELECT subscription_status,
       COUNT(customer_id) AS total_customers,
       ROUND(AVG(purchase_amount),2) AS avg_spent,
       ROUND(SUM(purchase_amount),2) AS total_revenue
FROM customer
GROUP BY subscription_status
ORDER BY total_revenue DESC;
```
📌 **Why it matters**: Helps determine whether subscription programs improve customer value.

### 2️⃣ Customer Segmentation (New vs Returning vs Loyal)

```
WITH customer_type AS (
    SELECT customer_id, previous_purchases,
    CASE 
        WHEN previous_purchases = 1 THEN 'new'
        WHEN previous_purchases BETWEEN 2 AND 10 THEN 'returning'
        ELSE 'loyal'
    END AS customer_segment
    FROM customer
)
SELECT customer_segment, COUNT(*) AS number_of_customers
FROM customer_type
GROUP BY customer_segment
ORDER BY number_of_customers DESC;
```
📌 **Why it matters**: Enables loyalty strategy development and targeted marketing.

### 3️⃣ Top 3 Most Purchased Products Within Each Category

```
WITH item_counts AS (
    SELECT category,
           item_purchased,
           COUNT(customer_id) AS total_orders,
           ROW_NUMBER() OVER(PARTITION BY category 
                             ORDER BY COUNT(customer_id) DESC) AS item_rank
    FROM customer
    GROUP BY category, item_purchased
)
SELECT item_rank, category, item_purchased, total_orders
FROM item_counts
WHERE item_rank <= 3;
```
📌 **Why it matters**: Identifies best-performing products for inventory and promotion planning.

## 📊 Power BI Dashboard
An interactive Power BI dashboard was created to visualize KPIs and segment performance.

## Dashboard Features
* KPI Cards:
  - Number of Customers
  - Average Purchase Amount
  - Average Review Rating
* Revenue and sales by category
* Subscription distribution
* Revenue and sales by age group
* Interactive slicers:
  - Gender
  - Category
  - Subscription Status
  - Shipping Type

## 📌 Dashboard Preview:

<img width="1138" height="622" alt="image" src="https://github.com/user-attachments/assets/7ace1c2f-23d2-4fd3-9f38-4584d9fcddfd" />

## 📌 Key Findings
* Clothing generated the highest sales and revenue across categories.
* Most customers are non-subscribers, indicating an opportunity to grow subscription adoption.
* Certain products show strong dependency on discounts.
* Young adults represent a strong revenue-driving age segment.
* Loyal customers represent a key opportunity for retention-driven strategies.





