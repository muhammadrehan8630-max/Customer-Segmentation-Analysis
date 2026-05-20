# 📊 Customer Segmentation & RFM Analysis

Analyzed 500K+ retail transactions to identify high-value, loyal, at-risk, and inactive customers using RFM segmentation with SQL and Power BI.

---

## 🛠 Tools Used

PostgreSQL 18 • SQL (CTEs, Window Functions, NTILE, CASE WHEN) • Power BI • Excel

---

## 📌 Project Objective

The goal of this project was to segment customers based on purchasing behavior and identify valuable business opportunities such as retention, re-engagement, and repeat purchase growth.

---

## 🔍 What I Did

- Cleaned 541,909 raw retail transactions
- Removed null values, cancellations, and negative quantities
- Calculated RFM metrics:
  - Recency
  - Frequency
  - Monetary Value
- Built RFM scoring logic using:
  - CTEs
  - CASE WHEN
  - NTILE() window functions
- Segmented customers into business-focused groups
- Created interactive Power BI dashboard for customer insights

---

## 👥 Customer Segments

| Segment | Business Meaning |
|----------|------------------|
| Champions | High-value loyal customers |
| Loyal Customers | Frequent repeat buyers |
| At Risk | Customers likely to churn |
| New Customers | Recently acquired customers |
| Lost Customers | Inactive or churned customers |

---

## 📈 Key Findings

- Champions generated the highest revenue despite smaller customer count
- At Risk customers showed strong win-back opportunity
- One-time buyers formed the largest customer group
- Loyal customers contributed consistent repeat revenue

---

## 💻 SQL Highlight

```sql
WITH base AS (
    SELECT
        customerid,
        COUNT(DISTINCT invoiceno) AS frequency,
        SUM(quantity * unitprice) AS monetary,
        CURRENT_DATE - MAX(invoicedate::DATE) AS recency_days
    FROM clean_retail
    GROUP BY customerid
),

rfm AS (
    SELECT *,
        NTILE(5) OVER (ORDER BY recency_days DESC) AS r_score,
        NTILE(5) OVER (ORDER BY frequency) AS f_score,
        NTILE(5) OVER (ORDER BY monetary) AS m_score
    FROM base
),

segments AS (
    SELECT *,
        CASE
            WHEN r_score >= 4 AND f_score >= 4 THEN 'Champions'
            WHEN r_score >= 3 AND f_score >= 3 THEN 'Loyal Customers'
            WHEN r_score <= 2 AND f_score >= 3 THEN 'At Risk'
            WHEN r_score >= 4 AND f_score <= 2 THEN 'New Customers'
            ELSE 'Lost Customers'
        END AS segment
    FROM rfm
)

SELECT * FROM segments;
