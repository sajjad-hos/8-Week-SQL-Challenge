## Case Study #4 - B. Customer Transactions | Solutions | Result

#### Q1: What is the unique count and total amount for each transaction type?
#### 🧠 My Approach & Solution:

````sql
SELECT
  txn_type, 
  COUNT(customer_id) AS unique_count, 
  SUM(txn_amount) AS total_amount
FROM data_bank.customer_transactions
GROUP BY txn_type;
  ````

#### 📊 Query Result & Insights:
| txn_type   | unique_count | total_amount |
| ---------- | ------------ | ------------ |
| purchase   | 1617         | 806537       |
| deposit    | 2671         | 1359168      |
| withdrawal | 1580         | 793003       |

---
#### Q2: What is the average total historical deposit counts and amounts for all customers?
#### 🧠 My Approach & Solution:
- Created a CTE to filter for 'deposit' transactions, group by customer_id, and calculate each customer's total deposit count and total deposit amount. Then, in the main query, compute the average of the per-customer metrics (count and amount) from the CTE.

````sql
WITH customer_deposits AS (
  SELECT
    customer_id,
    COUNT(*) AS deposit_count,
    AVG(txn_amount) AS total_deposit_amount
  FROM data_bank.customer_transactions
  WHERE txn_type = 'deposit'
  GROUP BY customer_id
)
SELECT
  ROUND(AVG(deposit_count)) AS avg_deposit_count,
  ROUND(AVG(total_deposit_amount)) AS avg_total_deposit_amount
FROM customer_deposits;
  ````

#### 📊 Query Result & Insights:
| avg_deposit_count | avg_total_deposit_amount |
| ----------------- | ------------------------ |
| 5                 | 509                      |

- On average, each customer has made 5 deposit transactions historically.
- The average total amount deposited per customer is $509.
---
#### Q3: For each month - how many Data Bank customers make more than 1 deposit and either 1 purchase or 1 withdrawal in a single month?
#### 🧠 My Approach & Solution:
- First, created a CTE to group transactions by month and customer, counting the number of each transaction type (deposit, purchase, withdrawal) per customer per month.
- In the main query, filtered for customers who made more than 1 deposit AND at least 1 purchase OR 1 withdrawal in that same month. And counted the distinct customers who meet this criteria for each month.

````sql
WITH transactions_aggregate AS (
    SELECT
        DATE_PART('month', txn_date) AS txn_month,
        customer_id,
        SUM(CASE WHEN txn_type = 'deposit' THEN 1 ELSE 0 END) AS deposit_count,
        SUM(CASE WHEN txn_type = 'purchase' THEN 1 ELSE 0 END) AS purchase_count,
        SUM(CASE WHEN txn_type = 'withdrawal' THEN 1 ELSE 0 END) AS withdrawal_count
    FROM data_bank.customer_transactions
    GROUP BY txn_month, customer_id
)

SELECT 
    txn_month,
    COUNT(DISTINCT customer_id) AS customer_count
FROM transactions_aggregate
WHERE deposit_count > 1 AND (purchase_count >= 1 OR withdrawal_count >= 1)
GROUP BY txn_month
ORDER BY txn_month;
  ````

#### 📊 Query Result & Insights:
| txn_month | customer_count |
| --------- | -------------- |
| 1         | 168            |
| 2         | 181            |
| 3         | 192            |
| 4         | 70             |

- Customer count increased from 168 to 192 from January to March. Then dropped sharply to 70 in April.
---
#### Q4: What is the closing balance for each customer at the end of the month?
#### 🧠 My Approach & Solution:
- In CTE 1 (daily_balances): I used a CASE statement to standardize transactions, turning deposits into positive values and withdrawals/purchases into negative values. Then summed up these values for each customer per day to get a daily net change.
- In CTE 2 (all_customer_dates): created a complete calendar of all month-end dates using GENERATE_SERIES. This is then combined with every customer ID to form a grid, ensuring we account for every customer in every month, even if they had no activity.
- In CTE 3 (customer_closing_balances): I joined the date grid and daily balances. The key step is using the SUM() OVER window function to calculate a running total (closing balance) for each customer, ordered by time.
- Finally, I grouped customer and month to produce the final result. The MIN function is used on the closing balance to return the single correct value for each month, giving one clear row per customer per period.

````sql
-- CTE 1: Calculate daily net transaction changes. Converts individual transactions to daily net balance changes
WITH daily_balances AS (
  SELECT 
    customer_id, 
    (DATE_TRUNC('month', txn_date) + INTERVAL '1 MONTH - 1 DAY') AS end_of_month, 
    SUM(CASE 
          WHEN txn_type IN ('withdrawal', 'purchase') THEN -txn_amount 
          ELSE txn_amount 
        END) AS daily_net_change
  FROM data_bank.customer_transactions
  GROUP BY customer_id, txn_date
)

-- CTE 2: Generate all required date combinations. Creates a comprehensive grid of all customers with all end-of-month dates
, all_customer_dates AS (
  SELECT
    DISTINCT customer_id,
    ('2020-01-31'::DATE + GENERATE_SERIES(0,3) * INTERVAL '1 MONTH') AS end_of_month
  FROM data_bank.customer_transactions
)

-- CTE 3: Calculate running balances and monthly activities. Computes both the monthly net change and cumulative closing balance
, customer_closing_balances AS (
  SELECT 
    d.customer_id, 
    d.end_of_month,
    SUM(b.daily_net_change) OVER (
      PARTITION BY d.customer_id, d.end_of_month
    ) AS monthly_activity,
    SUM(b.daily_net_change) OVER (
      PARTITION BY d.customer_id 
      ORDER BY d.end_of_month
      ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS closing_balance
  FROM all_customer_dates d
  LEFT JOIN daily_balances b
    ON d.end_of_month = b.end_of_month
    AND d.customer_id = b.customer_id
)

-- Final result: Clean and aggregate the calculated balances. Ensures we have one row per customer per month with consistent values
SELECT 
  customer_id, 
  end_of_month, 
  COALESCE(monthly_activity, 0) AS monthly_net_change, 
  MIN(closing_balance) AS closing_balance
FROM customer_closing_balances
GROUP BY customer_id, end_of_month, monthly_activity
ORDER BY customer_id, end_of_month;
  ````

#### 📊 Query Result & Insights:
Showing the result for the first 5 customer IDs.
| customer_id | end_of_month        | monthly_net_change | closing_balance |
| ----------- | ------------------- | ------------------ | --------------- |
| 1           | 2020-01-31 00:00:00 | 312                | 312             |
| 1           | 2020-02-29 00:00:00 | 0                  | 312             |
| 1           | 2020-03-31 00:00:00 | -952               | -964            |
| 1           | 2020-04-30 00:00:00 | 0                  | -640            |
| 2           | 2020-01-31 00:00:00 | 549                | 549             |
| 2           | 2020-02-29 00:00:00 | 0                  | 549             |
| 2           | 2020-03-31 00:00:00 | 61                 | 610             |
| 2           | 2020-04-30 00:00:00 | 0                  | 610             |
| 3           | 2020-01-31 00:00:00 | 144                | 144             |
| 3           | 2020-02-29 00:00:00 | -965               | -821            |
| 3           | 2020-03-31 00:00:00 | -401               | -1222           |
| 3           | 2020-04-30 00:00:00 | 493                | -729            |
| 4           | 2020-01-31 00:00:00 | 848                | 458             |
| 4           | 2020-02-29 00:00:00 | 0                  | 848             |
| 4           | 2020-03-31 00:00:00 | -193               | 655             |
| 4           | 2020-04-30 00:00:00 | 0                  | 655             |
| 5           | 2020-01-31 00:00:00 | 954                | 954             |
| 5           | 2020-02-29 00:00:00 | 0                  | 954             |
| 5           | 2020-03-31 00:00:00 | -2877              | -1923           |
| 5           | 2020-04-30 00:00:00 | -490               | -2413           |

---
#### Q5: What is the percentage of customers who increase their closing balance by more than 5%?
#### 🧠 My Approach & Solution:

This solution will be updated!

---

