# Customer Churn Analysis using SQL

## 📌 Project Overview
हा प्रोजेक्ट एका टेलिकॉम/बँकिंग कंपनीच्या ग्राहकांचा डेटा विश्लेषित करण्यासाठी तयार केला आहे. कंपनी सोडून जाणाऱ्या ग्राहकांचे (Churned Customers) प्रमाण शोधणे, त्यांची कारणे समजून घेणे आणि ग्राहकांना रोखण्यासाठी (Customer Retention) बिझनेस इनसाइट्स मिळवणे हा या प्रोजेक्टचा मुख्य उद्देश आहे.

## 📊 Database Schema (टेबल स्ट्रक्चर)
या प्रोजेक्टमध्ये आपण `customer_churn` नावाचा टेबल वापरला आहे, ज्यामध्ये खालील मुख्य कॉलम्स आहेत:
- `customer_id`: प्रत्येक ग्राहकाचा युनिक आयडी (Primary Key)
- `age`: ग्राहकाचे वय
- `gender`: लिंग (Male/Female)
- `tenure`: ग्राहक कंपनीसोबत किती महिने जोडलेला आहे
- `balance` / `monthly_charges`: ग्राहकाचे बँक बॅलन्स किंवा मासिक बिल
- `churn_status`: ग्राहक सोडून गेला की नाही (1 = सोडून गेला, 0 = अजूनही ग्राहक आहे)

## 🛠️ Tech Stack Used
- **Database:** MySQL / PostgreSQL / MS SQL Server (तुम्ही जे वापरत आहात ते लिहा)
- **Concepts:** Data Aggregation, Windows Functions, CTEs, Case Statements

---

## 🔍 Business Problems & SQL Queries (महत्त्वाचे प्रश्न आणि कोड)

### 1. एकूण ग्राहक आणि चर्न रेट (Total Customers & Churn Rate)
**प्रश्न:** एकूण किती ग्राहक आहेत आणि त्यापैकी किती टक्के ग्राहक सोडून गेले?
```sql
SELECT 
    COUNT(customer_id) AS Total_Customers,
    SUM(churn_status) AS Total_Churned_Customers,
    ROUND(SUM(churn_status) * 100.0 / COUNT(customer_id), 2) AS Churn_Rate_Percentage
FROM customer_churn;
```

### 2. लिंगानुसार चर्न विश्लेषण (Churn by Gender)
**प्रश्न:** महिला ग्राहक जास्त सोडून जात आहेत की पुरुष ग्राहक?
```sql
SELECT 
    gender,
    COUNT(customer_id) AS Total_Customers,
    SUM(churn_status) AS Churned_Customers,
    ROUND(SUM(churn_status) * 100.0 / COUNT(customer_id), 2) AS Churn_Rate
FROM customer_churn
GROUP BY gender;
```

### 3. वयोगटानुसार ग्राहक वर्गीकरण (Churn by Age Group)
**प्रश्न:** कोणत्या वयाचे ग्राहक कंपनी सर्वात जास्त सोडून जात आहेत?
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

### 4. टेन्युअरनुसार चर्न (Churn by Tenure)
**प्रश्न:** नवीन ग्राहक लवकर सोडून जातात की जुने ग्राहक? (टॉप ५ टेन्युअर महिने शोधणे)
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

## 💡 Key Insights & Business Recommendations (निष्कर्ष)
- **निष्कर्ष १:** ५० वर्षांपेक्षा जास्त वय असलेल्या ग्राहकांचा चर्न रेट सर्वात जास्त आहे. कंपनीने त्यांच्यासाठी सोप्या सेवा किंवा ऑफर्स आणल्या पाहिजेत.
- **निष्कर्ष २:** जे ग्राहक पहिल्या ६ महिन्यांत (Low Tenure) सोडून जात आहेत, त्यांना आकर्षित करण्यासाठी सुरुवातीला उत्तम सर्व्हिस देणे गरजेचे आहे.
