# 📉 Customer Churn & Subscription Analytics Portfolio Project

An end-to-end data analytics project combining **Python (Pandas)** and **SQLite** to process, clean, and analyze a customer subscription database. This project computes **core Key Performance Indicators (KPIs)** using advanced SQL queries, uncovering critical retention drivers and risk behaviors.

---

## 🛠️ Tech Stack & Tools
* **Programming Language:** Python
* **Data Manipulation:** Pandas, NumPy
* **Database & Querying:** SQLite3, Advanced SQL (CTEs, Window Functions, Conditional Aggregations)
* **Environment:** JupyterLab

---

## 🔄 Data Pipeline & Transformation Workflow
1. **Data Ingestion:** Connected to `customer_churn.db` to load core relational tables (`df_customer`, `df_subscription`, `df_support`) into a unified workspace.
2. **Data Cleaning & Standardization:** 
   * Renamed ambiguous columns (e.g., `name` to `customer_name`) and pruned unnecessary fields (`interests`, `pincode`).
   * Standardized categorical variables (`gender`) and formatted datetime structures (`dob`).
   * Imputed missing geographic records dynamically using state-to-country mapping.
3. **Database Persistence:** Exported the processed data into a clean, centralized master table (`master_file_table`) for reliable analytics queries.

---

## 📊 The Core Key Performance Indicators (KPIs)

The analytics engine evaluates the `master_file_table` to track the following business metrics:

1. **Overall Churn Rate (`28.57%`):** Calculated via SQL aggregation (`AVG(churn_flag) * 100`) to measure total subscription attrition.
2. **Retention Rate (`71.43%`):** Measured as the inverse of the churn rate (`100 - (AVG(churn_flag) * 100)`).
3. **Churn Breakdown by Plan Type:** Evaluates risk distribution across subscription tiers:
   * **Basic:** Highest risk tier (`60.00%` churn)
   * **Standard:** Moderate risk (`22.22%` churn)
   * **Premium:** Lowest risk tier (`14.28%` churn)
4. **Geographic Churn & Revenue Analysis (by State):** Evaluates regional user distribution, user counts, and total revenue generation (e.g., Uttar Pradesh yielding top revenue at `115.96` with `0%` churn).
5. **Acquisition / Subscription Type Analysis:** Compares acquisition channels against user volume, revenue, and risk:
   * **Organic:** `0%` churn across `9` users (`145.91` total revenue)
   * **Paid:** `16.67%` churn across `6` users (`174.94` total revenue)
   * **Referral:** High risk tier (`83.33%` churn across `6` users)
6. **Average Revenue Per User (ARPU) (`18.85`):** Computed via `AVG(monthly_charges)` to evaluate baseline monetization.
7. **Average Tenure Days (`1528.71` days):** Dynamically calculated using conditional `CASE` statements and `JULIANDAY` metrics for active vs. churned users.
8. **Revenue at Loss (Churned vs. Retained Revenue):** Aggregates financial loss using conditional `SUM(CASE WHEN churn_flag = 1 ...)` logic to isolate lost recurring income.
9. **Escalation Rate (`19.05%`):** Evaluates the proportion of customer accounts facing support escalations (`AVG(CASE WHEN escalations = 'Y' ...)`).
10. **Average Complaints Per User (`0.43`):** Measures support ticket load per customer via `SUM(complaint_count) * 1.0 / COUNT(customerid)`.
11. **Correlation Between Escalations & Churn (`0.77`):** Quantifies the strong positive statistical correlation between customer service escalations and final churn status.
12. **Risk Segmentation via Churn Scores:** Segments the user base into actionable risk tiers (`low`, `mid`, `high`) using conditional score logic:
    * **Low Risk:** `churn_score < 50`
    * **Mid Risk:** `50 <= churn_score < 70`
    * **High Risk:** `churn_score >= 70`

---
## 🚀 Key Business Insights & Recommendations
* **Plan & Channel Vulnerabilities:** Basic tier plans (`60%` churn) and Referral acquisition channels (`83.33%` churn) show critical attrition, signaling onboarding friction or misaligned incentives.
* **Support Impact:** The strong statistical correlation (`0.77`) between support escalations and churn highlights the urgent necessity for proactive customer service resolution protocols.
* **Proactive Retention:** Utilizing the `churn_risk` segmentation model allows retention teams to target high-risk accounts (`churn_score >= 70`) ahead of cancellation.

## 💻 Sample Code Highlights

```python
import sqlite3
import pandas as pd

# Connect to database and execute complex SQL KPI query (e.g., Geographic Aggregation)
conn = sqlite3.connect(db_path)
query = """
    SELECT state, 
           AVG(churn_flag) * 100 AS churn_percent, 
           COUNT(customerid) AS total_users, 
           SUM(monthly_charges) AS total_revenue 
    FROM master_file_table 
    group by state
"""
df_by_state = pd.read_sql(query, conn)
conn.close()


