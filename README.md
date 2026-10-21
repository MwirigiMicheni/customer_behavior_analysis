# Customer Shopping Behavior Analysis

## Project Overview
An end-to-end data analytics project examining **3,900 customer transactions** to uncover purchasing patterns, customer segments, product preferences, and subscription behavior, translating findings into actionable business recommendations.

## Tools & Technologies
**Python (Pandas) · PostgreSQL · SQL · Power BI · PowerPoint**

## Workflow
1. **Data Cleaning:** Used Pandas to inspect data quality, handle missing review ratings using product-category medians, standardize column names, and engineer features.
2. **SQL Analysis:** Loaded the cleaned dataset into PostgreSQL and answered 10 business questions using aggregations, subqueries, CTEs, CASE expressions, and window functions.
3. **Dashboard Development:** Connected Power BI to PostgreSQL to explore customer behavior and visualize key findings.
4. **Business Reporting:** Synthesized analytical findings into a visual PowerPoint report with business recommendations.

## Key Findings
- **Unexpected subscription gap:** Dashboard exploration revealed that no female customers in the dataset were subscribed. The finding was subsequently verified using SQL, highlighting an opportunity to investigate potential barriers to subscription.
- **Revenue disparity:** Male customers generated $157,890 in revenue, compared with $75,191 from female customers.
- **Subscription performance:** Subscribers averaged $59.49 per purchase, compared with $59.87 for non-subscribers, raising questions about the subscription program's value proposition.
- **Customer loyalty:** Approximately 80% of customers were classified as loyal, having made more than 10 previous purchases.
- **Product satisfaction:** The average product rating was 3.75/5, indicating room to investigate product quality and customer satisfaction.
- **Shipping behavior:** Two-day shipping customers recorded the highest average purchase amount ($60.73), while standard-shipping customers gave the highest average ratings (3.82/5).

## Business Recommendations
Investigate the subscription gap, reassess subscription benefits, strengthen customer retention, address product satisfaction issues, and evaluate shipping profitability alongside customer experience.

## Deliverables
- Python data cleaning and feature engineering
- SQL queries answering 10 business questions
- Power BI dashboard
- Visual PowerPoint insights report
