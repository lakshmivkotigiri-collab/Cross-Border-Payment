#  Cross-Border-Payment

![](https://github.com/lakshmivkotigiri-collab/Cross-Border-Payment/blob/main/Cross_Border_Payment_SEA_Main_Banner.jpeg)
## Project Overview
This project focuses on analyzing cross-border payment transactions using SQL. The dataset contains details such as transaction time, date, sender and receiver accounts, amount, currency, bank locations, payment type, and labels for money laundering activities.
The main objective is to perform data-driven analysis to detect suspicious activities, identify high-risk transaction patterns, and gain valuable insights into international money flows.

## Objectives

- To understand global money movement trends between countries.
- To identify high-value transactions and unusual behavior patterns.
- To detect potential money laundering cases using SQL-based analysis.
- To compare domestic vs cross-border transactions.
- To provide insights that can assist regulators, banks, and compliance teams in detecting fraudulent activities.

## Business Problems Addressed

- Which countries transact with each other most frequently?
- What is the monthly trend of laundering vs non-laundering transactions?
- Which sender accounts are linked to multiple receivers (possible layering)?
- Who are the top senders and receivers by transaction amount?
- What are the typical payment types used in laundering transactions?
- How do domestic and cross-border transactions differ in volume and value?
- Which accounts or transactions should be flagged as high-risk based on thresholds?

# Dataset

The data for this project is sourced from the Kaggle dataset:

- **Dataset Link:** [Anti Money Laundering](https://www.kaggle.com/datasets/berkanoztas/synthetic-transaction-monitoring-dataset-aml?utm_source=chatgpt.com)

## Schema

```sql
DROP TABLE IF EXISTS cross_bp;

CREATE TABLE cross_bp (
    time_col TIME,
    date_col DATE,
    sender_account VARCHAR(50),
    receiver_account VARCHAR(50),
    amount NUMERIC(15,2),
    payment_currency VARCHAR(30),   
    received_currency VARCHAR(30),
    sender_bank_location VARCHAR(100),
    receiver_bank_location VARCHAR(100),
    payment_type VARCHAR(50),
    is_laundering BOOLEAN,
    laundering_type VARCHAR(50)
);
```
## Importing Data
```sql
COPY cross_bp
FROM 'C:/Program Files/PostgreSQL/17/data/SAML-D.csv'
DELIMITER ',' 
CSV HEADER;
```

## Business Problems and Solutions

### 1. Find the top 5 sender accounts by total transaction amount

```sql
SELECT 
    sender_account,
    SUM(amount) AS total_amount,
    RANK() OVER (ORDER BY SUM(amount) DESC) AS rnk
FROM cross_bp
GROUP BY sender_account
LIMIT 5;
```



### 2. Hourly transaction volume trend

```sql
SELECT 
    EXTRACT(HOUR FROM time_col) AS hour_of_day,
    COUNT(*) AS tx_count,
    SUM(amount) AS total_amount
FROM cross_bp
GROUP BY hour_of_day
ORDER BY hour_of_day;
```

### 3. Monthly laundering vs non-laundering trend

```
SELECT 
    EXTRACT(MONTH FROM date_col) AS month,
    is_laundering,
    SUM(amount) AS total_amount,
    COUNT(*) AS tx_cnt
FROM cross_bp
GROUP BY 1, 2
ORDER BY 1, 2;
```

### 4. Top sender–receiver pairs (by transaction count and total amount)

```sql
SELECT 
    sender_account,
    receiver_account,
    COUNT(*) AS tx_count,
    SUM(amount) AS total_amount
FROM cross_bp
GROUP BY sender_account, receiver_account
HAVING COUNT(*) > 5
ORDER BY total_amount DESC
LIMIT 10;
```

### 5. Flagging unusually high-value transactions using CASE

```sql
SELECT 
    cr_id,
    sender_account,
    receiver_account,
    amount,
    CASE
        WHEN amount > 100000 THEN 'High Risk'
        WHEN amount > 50000 THEN 'Risk'
        ELSE 'Normal'
    END AS risk_level
FROM cross_bp
ORDER BY amount DESC
LIMIT 15;
```


### 6. Average transaction size by payment type and laundering type

```sql
SELECT 
    payment_type,
    laundering_type,
    AVG(amount) AS avg_amount,
    SUM(amount) AS total_amount,
    COUNT(*) AS tx_count
FROM cross_bp
GROUP BY payment_type, laundering_type
ORDER BY avg_amount DESC;
```

### 7. Detect accounts sending money to multiple receivers (possible layering)

```sql
SELECT 
    sender_account,
    COUNT(DISTINCT receiver_account) AS unique_receivers,
    SUM(amount) AS total_amount
FROM cross_bp
GROUP BY sender_account
HAVING COUNT(DISTINCT receiver_account) > 3
ORDER BY unique_receivers DESC, total_amount DESC
LIMIT 10;
```

### 8. Find the largest laundering transaction per day (window + CTE)

```sql
WITH daily_rank AS (
    SELECT 
        date_col,
        sender_account,
        receiver_account,
        amount,
        RANK() OVER (PARTITION BY date_col ORDER BY amount DESC) AS rnk
    FROM cross_bp
    WHERE is_laundering = TRUE
)
SELECT *
FROM daily_rank
WHERE rnk = 1
ORDER BY date_col;
```

### 9. Cross-border vs domestic transactions (using CASE)

```sql
SELECT 
    CASE 
        WHEN sender_bank_location <> receiver_bank_location THEN 'Cross-Border'
        ELSE 'Domestic'
    END AS tx_type,
    COUNT(*) AS tx_count,
    SUM(amount) AS total_amount
FROM cross_bp
GROUP BY tx_type;
```
### 10. Rolling 7-day transaction totals per sender (window + time)

```sql
SELECT 
    sender_account,
    date_col,
    SUM(amount) OVER (
        PARTITION BY sender_account 
        ORDER BY date_col
        RANGE BETWEEN INTERVAL '7 days' PRECEDING AND CURRENT ROW
    ) AS rolling_7d_amount
FROM cross_bp
ORDER BY sender_account, date_col;
```

### 11. Sender to Receiver account country

```sql
SELECT 
    sender_bank_location AS sender_country,
    receiver_bank_location AS receiver_country,
    COUNT(*) AS tx_count,
    SUM(amount) AS total_amount
FROM cross_bp
GROUP BY sender_country, receiver_country
ORDER BY tx_count DESC
LIMIT 20;
```


## SQL Techniques Used

- CTEs (Common Table Expressions) for step-by-step breakdown of queries.
- Window Functions (RANK, ROW_NUMBER, SUM OVER) for ranking and rolling sums.
- Aggregations (COUNT, SUM, AVG) for trend analysis.
- CASE Statements for classification (e.g., High / Medium / Low risk).
- Joins for sender–receiver relationship analysis.
- Time functions for extracting monthly and hourly patterns.

## Key Findings

- A small percentage of accounts are responsible for a large share of laundering activity.
- Cross-border transactions tend to have higher average amounts compared to domestic ones.
- Payment type analysis shows that specific methods (like wire transfers) are disproportionately used in laundering cases.
- Time-based analysis reveals spikes in suspicious transactions during certain months.


## Conclusion

The project demonstrates how SQL can be used to uncover hidden patterns in financial transactions and provide actionable insights for fraud detection and compliance monitoring.By combining business logic with SQL analytics, the project highlights the importance of data-driven approaches in the fight against money laundering and financial crime.



























