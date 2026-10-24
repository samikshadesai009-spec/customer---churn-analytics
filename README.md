# Customer Churn Analysis using SQL

## 📌 Project Overview
This project focuses on analyzing customer data for a telecom/banking company to identify the key factors driving customer churn. The primary objective is to calculate the churn rate, understand customer behavior, and discover actionable business insights that can help the company improve customer retention strategies.

## 📊 Database Schema (Table Structure)
The project utilizes a database table named `customer_churn` with the following key attributes:
- `customer_id`: Unique identifier for each customer (Primary Key)
- `age`: Age of the customer
- `gender`: Gender of the customer (Male/Female)
- `tenure`: Number of months the customer has stayed with the company
- `balance` / `monthly_charges`: Account balance or monthly bill amount
- `churn_status`: Indicates if the customer has left (1 = Churned, 0 = Active)

## 🛠️ Tech Stack Used
- **Database:** SQL (MySQL / PostgreSQL / MS SQL Server)
- **Key Concepts:** Data Aggregation, Window Functions, CTEs (Common Table Expressions), Case Statements

---

## 🔍 Business Problems & SQL Queries

### 1. Total Customers & Churn Rate
**Problem:** What is the total number of customers and the overall customer churn rate percentage?
```sql
SELECT 
    COUNT(customer_id) AS Total_Customers,
    SUM(churn_status) AS Total_Churned_Customers,
    ROUND(SUM(churn_status) * 100.0 / COUNT(customer_id), 2) AS Churn_Rate_Percentage
FROM customer_churn;
```

### 2. Churn Analysis by Gender
**Problem:** Does gender play a role in customer churn? (Comparing male vs. female churn rates)
```sql
SELECT 
    gender,
    COUNT(customer_id) AS Total_Customers,
    SUM(churn_status) AS Churned_Customers,
    ROUND(SUM(churn_status) * 100.0 / COUNT(customer_id), 2) AS Churn_Rate
FROM customer_churn
GROUP BY gender;
```

### 3. Customer Segmentation by Age Group
**Problem:** Which age demographics experience the highest churn rate?
```sql
WITH AgeBuckets AS (
    SELECT *,
        CASE 
            WHEN age < 30 THEN 'Under 30'
            WHEN age BETWEEN 30 AND 50 THEN '30-50'
            ELSE 'Above 50'
        END AS age_group
    FROM customer_churn
)
SELECT 
    age_group,
    COUNT(customer_id) AS Total_Customers,
    SUM(churn_status) AS Churned_Customers,
    ROUND(SUM(churn_status) * 100.0 / COUNT(customer_id), 2) AS Churn_Rate
FROM AgeBuckets
GROUP BY age_group
ORDER BY Churn_Rate DESC;
```

### 4. Churn Analysis by Tenure
**Problem:** Are new customers more likely to churn compared to long-term customers? (Top 5 tenure months with highest churn)
```sql
SELECT 
    tenure,
    COUNT(customer_id) AS Total_Customers,
    SUM(churn_status) AS Churned_Customers,
    ROUND(SUM(churn_status) * 100.0 / COUNT(customer_id), 2) AS Churn_Rate
FROM customer_churn
GROUP BY tenure
ORDER BY Churn_Rate DESC
LIMIT 5;
```

---

## 💡 Key Insights & Business Recommendations
- **Insight 1:** Customers aged **above 50** exhibit the highest churn rate. The company should design senior-friendly loyalty programs or simplified user interfaces to retain them.
- **Insight 2:** A significant volume of churn occurs within the **first 6 months (Low Tenure)**. Enhancing the onboarding experience and offering early-stage discounts could boost customer retention.
