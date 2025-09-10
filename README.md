#  Cross-Border-Payment

1[](https://github.com/lakshmivkotigiri-collab/Cross-Border-Payment/blob/main/Cross_Border_Payment_SEA_Main_Banner.jpeg)
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





# Dataset

The data for this project is sourced from the Kaggle dataset:

- **Dataset Link:** [Movies Dataset](https://www.kaggle.com/datasets/berkanoztas/synthetic-transaction-monitoring-dataset-aml?utm_source=chatgpt.com)

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

















