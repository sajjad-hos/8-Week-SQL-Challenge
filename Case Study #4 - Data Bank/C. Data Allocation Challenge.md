## Case Study #4 - C. Data Allocation Challenge | Solutions | Result

To test out a few different hypotheses - the Data Bank team wants to run an experiment where different groups of customers would be allocated data using 3 different options:

- Option 1: data is allocated based off the amount of money at the end of the previous month
- Option 2: data is allocated on the average amount of money kept in the account in the previous 30 days
- Option 3: data is updated real-time

For this multi-part challenge question - you have been requested to generate the following data elements to help the Data Bank team estimate how much data will need to be provisioned for each option:

- running customer balance column that includes the impact each transaction
- customer balance at the end of each month
- minimum, average and maximum values of the running balance for each customer

Using all of the data available - how much data would have been required for each option on a monthly basis?

---
#### Q1: Running customer balance column that includes the impact each transaction.

#### 🧠 My Approach & Solution:
- Converted all transaction amounts into signed amounts (positive for deposits, negative for withdrawals/purchases).
- Used a window function to compute a running sum per customer, ordered by date.

````sql
WITH signed_transactions AS (
    SELECT 
        customer_id,
        txn_date,
        txn_type,
        txn_amount,
        CASE 
            WHEN txn_type = 'deposit' THEN txn_amount
            WHEN txn_type IN ('withdrawal','purchase') THEN -txn_amount
            ELSE 0
        END AS signed_amount
    FROM data_bank.customer_transactions
)
SELECT
    customer_id,
    txn_date,
    txn_type,
    txn_amount,
    SUM(signed_amount) OVER (
        PARTITION BY customer_id
        ORDER BY txn_date
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS running_balance
FROM signed_transactions
ORDER BY customer_id, txn_date;
  ````

#### 📊 Query Result & Insights:
The result is displayed here only for the first five customers, as the entire result set is large.
| customer_id | txn_date   | txn_type   | txn_amount | running_balance |
| ----------- | ---------- | ---------- | ---------- | ------------------ |
| 1           | 2020-01-02 | deposit    | 312        | 312                |
| 1           | 2020-03-05 | purchase   | 612        | -300               |
| 1           | 2020-03-17 | deposit    | 324        | 24                 |
| 1           | 2020-03-19 | purchase   | 664        | -640               |
| 2           | 2020-01-03 | deposit    | 549        | 549                |
| 2           | 2020-03-24 | deposit    | 61         | 610                |
| 3           | 2020-01-27 | deposit    | 144        | 144                |
| 3           | 2020-02-22 | purchase   | 965        | -821               |
| 3           | 2020-03-05 | withdrawal | 213        | -1034              |
| 3           | 2020-03-19 | withdrawal | 188        | -1222              |
| 3           | 2020-04-12 | deposit    | 493        | -729               |
| 4           | 2020-01-07 | deposit    | 458        | 458                |
| 4           | 2020-01-21 | deposit    | 390        | 848                |
| 4           | 2020-03-25 | purchase   | 193        | 655                |
| 5           | 2020-01-15 | deposit    | 974        | 974                |
| 5           | 2020-01-25 | deposit    | 806        | 1780               |
| 5           | 2020-01-31 | withdrawal | 826        | 954                |
| 5           | 2020-03-02 | purchase   | 886        | 68                 |
| 5           | 2020-03-19 | deposit    | 718        | 786                |
| 5           | 2020-03-26 | withdrawal | 786        | 0                  |
| 5           | 2020-03-27 | withdrawal | 700        | -700               |
| 5           | 2020-03-27 | deposit    | 412        | -288               |
| 5           | 2020-03-29 | purchase   | 852        | -1140              |
| 5           | 2020-03-31 | purchase   | 783        | -1923              |
| 5           | 2020-04-02 | withdrawal | 490        | -2413              |

---

#### Q2: Customer balance at the end of each month.
#### 🧠 My Approach & Solution:
- Calculated monthly closing balance per customer (deposits positive, withdrawals/purchases negative).
- Grouped by customer and month and display month start, month name, and balance in order.

````sql
SELECT 
    customer_id,
    DATE_TRUNC('month', txn_date)::date AS month_start,
    TO_CHAR(txn_date, 'Month') AS month_name,
    SUM(CASE 
            WHEN txn_type = 'deposit' THEN txn_amount
            WHEN txn_type IN ('withdrawal','purchase') THEN -txn_amount
            ELSE 0
        END) AS closing_balance
FROM data_bank.customer_transactions
GROUP BY customer_id, month_start, month_name
ORDER BY customer_id, month_start;
  ````

#### 📊 Query Result & Insights:
The result is displayed here only for the first five customers, as the entire result set is large.
| customer_id | month_start | month_name | closing_balance |
| ----------- | ----------- | ---------- | ---------------- |
| 1           | 2020-01-01  | January    | 312              |
| 1           | 2020-03-01  | March      | -952             |
| 2           | 2020-01-01  | January    | 549              |
| 2           | 2020-03-01  | March      | 61               |
| 3           | 2020-01-01  | January    | 144              |
| 3           | 2020-02-01  | February   | -965             |
| 3           | 2020-03-01  | March      | -401             |
| 3           | 2020-04-01  | April      | 493              |
| 4           | 2020-01-01  | January    | 848              |
| 4           | 2020-03-01  | March      | -193             |
| 5           | 2020-01-01  | January    | 954              |
| 5           | 2020-03-01  | March      | -2877            |
| 5           | 2020-04-01  | April      | -490             |

---

#### Q3: Minimum, average and maximum values of the running balance for each customer.
#### 🧠 My Approach & Solution:
- Calculated running balance using a window function (SUM() OVER) with PARTITION BY customer_id ORDER BY txn_date.
- Then, I calculated the minimum, maximum, and average balance for each customer.

````sql
WITH running_balance AS (
    SELECT 
        customer_id,
        txn_date,
        txn_type,
        txn_amount,
        SUM(CASE 
                WHEN txn_type = 'deposit' THEN txn_amount
                WHEN txn_type IN ('withdrawal','purchase') THEN -txn_amount
                ELSE 0
            END
        ) OVER (PARTITION BY customer_id ORDER BY txn_date) AS running_balance
    FROM data_bank.customer_transactions
)
SELECT 
    customer_id,
    MIN(running_balance) AS min_running_balance,
    MAX(running_balance) AS max_running_balance,
    ROUND(AVG(running_balance),1) AS avg_running_balance
FROM running_balance
GROUP BY customer_id
ORDER BY customer_id;
  ````

#### 📊 Query Result & Insights:
| customer_id | min_running_balance | max_running_balance | avg_running_balance |
| ----------- | ------------------- | ------------------- | ------------------- |
| 1           | -640                | 312                 | -151.0              |
| 2           | 549                 | 610                 | 579.5               |
| 3           | -1222               | 144                 | -732.4              |
| 4           | 458                 | 848                 | 653.7               |
| 5           | -2413               | 1780                | -135.5              |

---

Now, for each option, the running balance, month-end balance, and the minimum, average, and maximum balances will be applied to determine the total amount of data required on a monthly basis.
#### Option 1: Data is allocated based off the amount of money at the end of the previous month.
#### 🧠 My Approach & Solution:
````sql
WITH transactions AS
(
    SELECT customer_id,
           txn_date,
           EXTRACT(MONTH FROM txn_date) AS txn_month,
           txn_type,
           CASE 
               WHEN txn_type = 'deposit' THEN txn_amount 
               ELSE -txn_amount 
           END AS net_amount
    FROM customer_transactions
),

running_balance AS
(
    SELECT customer_id,
           txn_date,
           txn_month,
           net_amount,
           SUM(net_amount) OVER(
               PARTITION BY customer_id, txn_month 
               ORDER BY txn_date
               ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
           ) AS balance
    FROM transactions
),

month_end_balance AS
(
    SELECT customer_id,
           txn_month,
           MAX(balance) AS end_balance
    FROM running_balance
    GROUP BY customer_id, txn_month
)

SELECT txn_month,
       SUM(end_balance) AS total_balance_per_month
FROM month_end_balance
GROUP BY txn_month
ORDER BY total_balance_per_month DESC;
  ````

#### 📊 Query Result & Insights:
| txn_month | total_balance_per_month |
| --------- | ----------------------- |
| 1         | 362688                  |
| 3         | 147514                  |
| 2         | 130592                  |
| 4         | 53982                   |

---
#### Option 2: data is allocated on the average amount of money kept in the account in the previous 30 days.
#### 🧠 My Approach & Solution:
````sql
WITH monthly_transactions AS
(
    SELECT 
        customer_id,
        EXTRACT(MONTH FROM txn_date) AS txn_month,
        SUM(CASE 
                WHEN txn_type = 'deposit' THEN txn_amount
                ELSE -txn_amount
            END) AS net_amount
    FROM customer_transactions
    GROUP BY customer_id, EXTRACT(MONTH FROM txn_date)
),

running_balance AS
(
    SELECT 
        customer_id,
        txn_month,
        net_amount,
        SUM(net_amount) OVER(PARTITION BY customer_id ORDER BY txn_month) AS balance
    FROM monthly_transactions
),

avg_balance_per_customer AS
(
    SELECT 
        customer_id,
        AVG(balance) AS avg_balance
    FROM running_balance
    GROUP BY customer_id
)

SELECT 
    r.txn_month,
    ROUND(SUM(a.avg_balance), 0) AS total_avg_balance_per_month
FROM running_balance r
JOIN avg_balance_per_customer a
    ON r.customer_id = a.customer_id
GROUP BY r.txn_month
ORDER BY total_avg_balance_per_month;
  ````

#### 📊 Query Result & Insights:
| txn_month | total_avg_balance_per_month |
| --------- | --------------------------- |
| 2         | -79372                      |
| 3         | -75818                      |
| 1         | -65492                      |
| 4         | -63347                      |

---
#### Option 3: Data is updated real-time.
#### 🧠 My Approach & Solution:
````sql
WITH transactions AS
(
    SELECT 
        customer_id,
        txn_date,
        EXTRACT(MONTH FROM txn_date) AS txn_month,
        txn_type,
        txn_amount,
        CASE 
            WHEN txn_type = 'deposit' THEN txn_amount 
            ELSE -txn_amount 
        END AS net_amount
    FROM customer_transactions
),

running_balance AS
(
    SELECT 
        customer_id,
        txn_month,
        SUM(net_amount) OVER (PARTITION BY customer_id ORDER BY txn_month) AS balance
    FROM transactions
)

SELECT 
    txn_month,
    SUM(balance) AS total_balance_per_month
FROM running_balance
GROUP BY txn_month
ORDER BY total_balance_per_month;
  ````

#### 📊 Query Result & Insights:
| txn_month | total_balance_per_month |
| --------- | ----------------------- |
| 3         | -1292510                |
| 4         | -553878                 |
| 2         | -250274                 |
| 1         | 100631                  |

---
