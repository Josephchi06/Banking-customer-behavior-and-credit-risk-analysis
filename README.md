# Banking Customer Behavior & Credit Risk Analysis

## Table of Contents

- [Project Overview](#project-overview)
- [Business Problem](#business-problem)
- [Project Objectives](#project-objectives)
- [Dataset Description](#dataset-description)
- [Tools & Technologies Used](#tools--technologies-used)
- [Business Questions & SQL Analysis](#business-questions--sql-analysis)
- [Dashboard Features & Preview](#dashboard-features--preview)
- [Key Insights](#key-insights)
- [Recommendations](#recommendations)
- [Conclusion](#conclusion)

---------------

## Project Overview

This project analyzes customer banking behavior, credit risk distribution, and financial exposure using SQL and Power BI. The dashboard was developed to help identify high-risk customers, monitor lending exposure, evaluate customer account balance patterns, and support data-driven financial decision-making within the banking sector.

-----

## Business Problem

Banks manage large volumes of customer financial data across loans, accounts, and transactions. Without proper analysis, it becomes difficult to identify high-risk customers, monitor lending exposure, understand customer banking behavior, and support informed financial decision-making.

This project was developed to analyze customer risk patterns, evaluate financial exposure, and provide actionable business insights through interactive data visualization and analytical reporting.


-----

## Project Objectives

The objectives of this project were to:

- Analyze customer banking behavior across accounts, loans, and transactions.
- Identify high-risk customer segments using credit score analysis.
- Monitor customer financial exposure by comparing loan amounts against account balances.
- Evaluate account balance distribution across account types.
- Provide interactive visual insights to support data-driven banking decisions.

----

## Dataset Description

The dataset used in this project contains banking-related customer information across multiple tables, including customer details, account information, loan records, and transaction history.

### Dataset Source
[Synthetic Banking Dataset (Kaggle)](https://www.kaggle.com/datasets/akrambelha/synthetic-banking-dataset-csv-sql-sqlite)
### Tables Used
- Customers
- Accounts
- Loans
- Transactions

### Key Fields
- `customer_id` – Unique customer identifier
- `credit_score` – Customer credit risk score
- `loan_amount` – Customer loan value
- `balance_usd` – Customer account balance
- `account_type` – Type of customer account
- `amount_usd` – Transaction amount value

-----

## Tools & Technologies Used

- **SQL Server** – Data cleaning, aggregation, joins, and business analysis queries
- **Power BI** – Interactive dashboard creation and data visualization
- **DAX (Data Analysis Expressions)** – KPI calculations and dashboard measures
- **Power Query** – Data transformation and data loading
- **GitHub** – Project documentation and portfolio presentation


-----

## Business Questions & SQL Analysis

### 1. How are customers distributed across credit risk categories?

SQL analysis was performed to categorize customers into risk segments based on their credit scores in order to evaluate the distribution of low, medium, and high-risk customers across different cities.

```sql
SELECT COUNT(Customer_id) AS Total_customer,

CASE 
	WHEN credit_score >=750 THEN 'Low '
	WHEN credit_score BETWEEN 600 AND 749 THEN 'Medium'
	ELSE 'High'
END AS Risk_Category

FROM dbo.customers

GROUP BY 
CASE 
	WHEN credit_score >=750 THEN 'Low '
	WHEN credit_score BETWEEN 600 AND 749 THEN 'Medium'
	ELSE 'High'
END

ORDER BY COUNT(Customer_id) DESC
```


### 2. Which account types hold the highest balances?

SQL analysis was performed to aggregate customer account balances across different account types in order to identify which banking products held the highest concentration of customer funds.

```sql
SELECT
account_type,
ROUND(SUM(balance_usd),2) AS Total_balance_usd

FROM dbo.accounts

GROUP BY account_type

ORDER BY Total_balance_usd DESC

```

### 3. Which cities contain the highest concentration of high-risk customers?

SQL analysis was performed to identify cities with the largest number of high-risk customers based on credit score classification in order to evaluate regional credit risk concentration across the bank’s customer base.

```sql
SELECT TOP 10
    city,
    COUNT(customer_id) AS Total_Customers

FROM dbo.customers

WHERE credit_score < 600

GROUP BY city

ORDER BY Total_Customers DESC;
```

### 4. Is there a relationship between customer account balances and loan exposure?

SQL analysis was performed to compare customer account balances against loan amounts in order to evaluate potential financial exposure patterns and identify customers with elevated lending risk.

```sql
WITH CTE_Account AS
(
SELECT customer_id,
ROUND(SUM(balance_usd),2) AS Total_balance

FROM dbo.accounts

GROUP BY customer_id
)

,CTE_loan AS 
(
SELECT customer_id,
ROUND(SUM(loan_amount),2) AS Total_loan_amount

FROM dbo.loans

GROUP BY customer_id
)

SELECT TOP 10 C.customer_id,
CONCAT(first_name,' ',last_name) AS Customer_name,
CA.Total_balance,
CL.Total_loan_amount

FROM dbo.customers AS C

INNER JOIN CTE_Account AS CA

ON C.customer_id=CA.customer_id

INNER JOIN CTE_loan CL

ON C.customer_id=CL.customer_id

ORDER BY Total_balance DESC, Total_loan_amount DESC
```

### 5. Which customers have loan amounts greater than their account balances?

SQL analysis was performed to compare total customer loan amounts against aggregated account balances in order to identify customers with higher financial exposure and potential lending risk.

```sql
WITH Loans AS (

SELECT
customer_id,
ROUND(SUM(loan_amount),2) AS Total_loan_amount

FROM dbo.loans

GROUP BY customer_id )

, Accounts AS (
SELECT
customer_id,
ROUND(SUM(balance_usd),2) AS Total_balance_usd

FROM dbo.accounts

GROUP BY customer_id )

SELECT TOP 10
C.customer_id,
CONCAT(first_name,' ',last_name) AS Customer_names,
L.Total_loan_amount,
A.Total_balance_usd,
L.Total_loan_amount-A.Total_balance_usd AS Exposure_Diff

FROM dbo.customers AS C

INNER JOIN Loans AS L

ON C.customer_id=L.customer_id

INNER JOIN Accounts AS A 

ON C.customer_id=A.customer_id

WHERE L.Total_loan_amount>A.Total_balance_usd

ORDER BY Total_loan_amount DESC, Total_balance_usd DESC
```
------
## Dashboard Features & Preview

The Power BI dashboard was designed to provide interactive analysis of customer banking behavior, credit risk distribution, and financial exposure.

### Dashboard Components
- KPI Cards for customer, loan, balance, and transaction analysis
- Credit Risk Distribution Visualization
- Account Balance Analysis by Account Type
- High-Risk Customer Distribution by City
- Balance vs Loan Exposure Analysis
- High Exposure Customer Monitoring Table
- Interactive Page Navigation
- Dedicated Insights and Recommendations Pages

### Interactive Features
- Risk Category Slicers
- Account Type Filtering
- Interactive Cross-Visual Filtering

![Main Dashboard](https://github.com/user-attachments/assets/eab91170-7512-413d-8705-6eb3210e6df4)

-----

## Key Insights

### Credit Risk:
The analysis showed that high-risk customers represented the largest customer segment, suggesting that a significant portion of the bank’s customer base may carry elevated credit default risk. This increases the importance of stronger credit monitoring and lending control strategies to reduce potential financial exposure.

### Customer Balance:
The analysis showed that total account balances were relatively similar across account types, suggesting that customer funds were evenly distributed rather than concentrated in a single banking product. This indicates balanced customer engagement across account offerings and may reduce dependency on a single account category for deposit retention.

### Geographic Risk:
The analysis revealed that high-risk customers were distributed across multiple cities rather than concentrated in a single location, indicating that credit risk exposure exists across several regions of the bank’s operations. However, some cities recorded slightly higher concentrations, suggesting the need for closer regional risk monitoring and targeted financial strategies.


### Financial Exposure:
The analysis identified a small group of customers whose total loan amounts exceeded their account balances, indicating elevated financial exposure and increased lending risk for the bank. These customers may present higher repayment vulnerability, making continuous exposure monitoring and stricter credit control measures essential.


------

## Recommendations

### Credit Risk:
The bank should strengthen credit risk assessment and customer monitoring strategies for high-risk customer groups in order to reduce potential loan default exposure and improve overall lending stability.


### Exposure Monitoring:
The bank should implement continuous monitoring procedures for customers whose loan amounts exceed their account balances in order to reduce financial exposure risk and improve lending control efficiency.


### Geographic Risk:
The bank should strengthen regional risk monitoring in cities with higher concentrations of high-risk customers in order to support targeted financial strategies and improve overall credit risk management across different operational regions.


---

## Conclusion

This project demonstrates how SQL and Power BI can be combined to analyze customer banking behavior, evaluate credit risk exposure, and support data-driven financial decision-making. Through interactive visualization, business-focused SQL analysis, and analytical reporting, the dashboard provides valuable insights into customer risk patterns, financial exposure, and banking operations.
