# Customer Shopping Behavior Analysis: Technical Report

## Project Overview

This project analyzed customer shopping behavior using transactional data from **3,900 purchases** across multiple product categories. The goal was to uncover insights about customer spending patterns, customer segments, product preferences, and subscription behavior to guide strategic business decisions.

## Dataset Summary

- **Rows:** 3,900
- **Columns:** 18
- **Key Features:**
  - Customer demographics: Age, Gender, Location, Subscription Status
  - Purchase details: Item Purchased, Category, Purchase Amount, Season, Size, Color
  - Shopping behavior: Discount Applied, Promo Code Used, Previous Purchases, Frequency of Purchases, Review Rating, Shipping Type
- **Missing Data:** 37 null values in the `Review Rating` column

## Python Data Processing

Data preparation and cleaning were performed using **Python Pandas**.

- Inspected the dataset structure and descriptive statistics using `df.info()` and `df.describe()`.
- Identified missing values using `df.isnull().sum()`.
- Handled the 37 missing review ratings using the median rating within each product category.
- Standardized column names before feature engineering.

### Handling Missing Values

```python
df['Review Rating'] = (
    df.groupby('Category')['Review Rating']
      .transform(lambda x: x.fillna(x.median()))
)
```

This approach preserves differences in rating distributions across product categories rather than applying a single overall median.

### Feature Engineering

- **Age Group:** Created an `age_group` column by dividing customer ages into four quantile-based groups.

```python
age_group_labels = [
    'Young Adult', 'Adult', 'Middle Aged', 'Senior'
]

df['age_group'] = pd.qcut(
    df['age'],
    q=4,
    labels=age_group_labels
)

df[['age', 'age_group']].head(10)
```

- **Purchase Frequency:** Created `purchase_frequency_days` by mapping purchase-frequency categories, such as fortnightly and monthly, to their corresponding number of days.
- **Redundant Column Removal:** Dropped `promo_code_used` because it duplicated information represented by `discount_applied`.

### Loading Data into PostgreSQL

Connected the Python script to PostgreSQL and loaded the cleaned DataFrame into the database for SQL analysis using SQLAlchemy.

```python
import os
from sqlalchemy import create_engine

# Connect to PostgreSQL using environment variables
username = os.environ["DB_USER"]
password = os.environ["DB_PASSWORD"]
host = os.environ.get("DB_HOST", "localhost")
port = os.environ.get("DB_PORT", "5432")
database = "customer_shopping_behavior"

engine = create_engine(
    f"postgresql+psycopg2://{username}:{password}"
    f"@{host}:{port}/{database}"
)

# Load the DataFrame into PostgreSQL
table_name = "shopping_behavior"

df.to_sql(
    table_name,
    engine,
    if_exists="replace",
    index=False
)

print(
    f"Data successfully loaded into table: "
    f"{table_name} in database: {database}."
)
```

Database credentials are read from environment variables rather than hardcoded into the script.

## SQL Data Analytics

Performed structured analysis in PostgreSQL to answer **10 business questions** about customer shopping behavior.

| # | Business Question | SQL Techniques Applied |
|---|---|---|
| 1 | Revenue by Gender | `SUM()`, `GROUP BY` |
| 2 | High-Spending Discount Users | `WHERE`, subquery, `AVG()` |
| 3 | Top 5 Products by Rating | `AVG()`, `ROUND()`, `GROUP BY`, `ORDER BY`, `LIMIT` |
| 4 | Shipping Type Comparison | `AVG()`, `ROUND()`, `WHERE` |
| 5 | Subscribed vs. Non-Subscribed Customers | `SUM()`, `AVG()`, `ROUND()`, `GROUP BY`, `ORDER BY` |
| 6 | Discounted Purchases | `COUNT()`, `ROUND()`, `GROUP BY`, `ORDER BY` |
| 7 | Customer Segmentation | CTE, `CASE`, `GROUP BY`, `ORDER BY` |
| 8 | Top 3 Products per Category | CTE, window functions, `WHERE`, `GROUP BY` |
| 9 | Repeat Buyers and Subscriptions | `COUNT()`, `GROUP BY`, `WHERE` |
| 10 | Revenue by Age Group | `SUM()`, `GROUP BY`, `ORDER BY` |

### Example 1: Top 5 Products by Rating

Used aggregation and sorting to identify the five products with the highest average review ratings.

```sql
SELECT
    ITEM_PURCHASED,
    ROUND(AVG(REVIEW_RATING::NUMERIC), 2)
        AS AVERAGE_PRODUCT_RATING
FROM SHOPPING_BEHAVIOR
GROUP BY ITEM_PURCHASED
ORDER BY AVERAGE_PRODUCT_RATING DESC
LIMIT 5;
```

### Example 2: Customer Segmentation Using a CTE

Classified customers as new, returning, or loyal based on their previous purchase counts, then summarized the size of each segment.

```sql
WITH CUSTOMER_TYPE AS (
    SELECT
        CUSTOMER_ID,
        PREVIOUS_PURCHASES,
        CASE
            WHEN PREVIOUS_PURCHASES = 1
                THEN 'New'
            WHEN PREVIOUS_PURCHASES BETWEEN 2 AND 10
                THEN 'Returning'
            ELSE 'Loyal'
        END AS CUSTOMER_SEGMENTS
    FROM SHOPPING_BEHAVIOR
)
SELECT
    CUSTOMER_SEGMENTS,
    COUNT(*) AS NUMBER_OF_CUSTOMERS
FROM CUSTOMER_TYPE
GROUP BY CUSTOMER_SEGMENTS
ORDER BY NUMBER_OF_CUSTOMERS DESC;
```

