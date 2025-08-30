## Case Study #7 - Balanced Tree Clothing Co. | B. Transaction Analysis | Questions | Solutions | Results

#### Q1: How many unique transactions were there?
#### 🧠 My Approach & Solution:

````sql
SELECT COUNT(DISTINCT txn_id) AS total_unique_transactions
FROM balanced_tree.sales;
  ````

#### 📊 Query Result & Insights:
| total_unique_transactions |
| ------------------------- |
| 2500                      |

- There were 2500 total unique transactions.
---
#### Q2: What was the average number of unique products purchased in each transaction?
#### 🧠 My Approach & Solution:
- Created CTE to count unique products per transaction. In the main query, I found the average across all transactions

````sql
WITH unique_products_per_transaction AS (
    SELECT 
        txn_id,
        COUNT(DISTINCT prod_id) AS unique_product_count
    FROM balanced_tree.sales
    GROUP BY txn_id
)
SELECT AVG(unique_product_count)::numeric(10,0) AS avg_unique_products_per_transaction
FROM unique_products_per_transaction;
  ````

#### 📊 Query Result & Insights:
| avg_unique_products_per_transaction |
| ----------------------------------- |
| 6                               |

- The average number of unique products purchased in each transaction was 6.
---
#### Q3: What were the 25th, 50th, and 75th percentile values for the revenue per transaction?
#### 🧠 My Approach & Solution:
- First, I created a CTE to calculate the total revenue for each transaction by multiplying price and qty and summing them per txn_id.
- Then, I used PERCENTILE_CONT to find the 25th, 50th (median), and 75th percentiles of these transaction totals.

````sql
WITH transaction_totals AS (
    SELECT 
        txn_id,
        SUM(price * qty) AS total_amount
    FROM balanced_tree.sales
    GROUP BY txn_id
)
SELECT
    PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY total_amount) AS "Percentile 25th",
    PERCENTILE_CONT(0.5)  WITHIN GROUP (ORDER BY total_amount) AS "Percentile 50th",
    PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY total_amount) AS "Percentile 75th"
FROM transaction_totals;
  ````

#### 📊 Query Result & Insights:
| Percentile 25th | Percentile 50th | Percentile 75th |
| --------------- | --------------- | --------------- |
| 375.75          | 509.5           | 647             |

- One quarter of transactions made $375.75 or below this.
- Half of the transactions made $509.50 or less.
- Only the top 25% of transactions made more than $647.
---
#### Q4: What was the average discount value per transaction?
#### 🧠 My Approach & Solution:
- I created CTE txn_discounts to calculate the total discount in dollars for each transaction. Then, the main query takes the average of these totals across all transactions and rounds it to 0 decimal places.

````sql
WITH txn_discounts AS (
    SELECT 
        txn_id,
        SUM(qty * price * discount / 100) AS total_discount
    FROM balanced_tree.sales
    GROUP BY txn_id
)
SELECT ROUND(AVG(total_discount), 0) AS avg_discount_per_transaction
FROM txn_discounts;
  ````

#### 📊 Query Result & Insights:
| avg_discount_per_transaction |
| ---------------------------- |
| 60                           |

-  The average discount value per transaction was 60.
---
#### Q5: What was the percentage split of all transactions for members vs non-members?
#### 🧠 My Approach & Solution:
- First, I identified distinct transactions and marked each as member (1) or non-member (0).
- Then, I summed the member flags to count member transactions and subtracted from total transactions to get non-member transactions.
- Finally, I calculated the percentage of member and non-member transactions by dividing by the total number of transactions.

````sql
WITH txn_member AS (
    SELECT DISTINCT 
        txn_id,
        CASE WHEN member = 't' THEN 1 ELSE 0 END AS member
    FROM balanced_tree.sales
)
SELECT 
    ROUND(100.0 * SUM(member) / COUNT(txn_id), 0) AS "percentage transactions for members",
    ROUND(100.0 * (COUNT(txn_id) - SUM(member)) / COUNT(txn_id), 0) AS "percentage transactions for non-members"
FROM txn_member;
  ````

#### 📊 Query Result & Insights:
| percentage transactions for members | percentage transactions for non-members |
| ----------------------------------- | --------------------------------------- |
| 60                                  | 40                                      |

- The percentage split of all transactions for members was 60, and for non-members was 40.
---
#### Q6: What was the average revenue for member transactions and non-member transactions?
#### 🧠 My Approach & Solution:
- Calculated the total revenue for each transaction by multiplying price and qty and summing per txn_id. Labeled each transaction as a member or a non-member based on the member column.
- Then, in the main query, I used group by member status to distribute the average revenue for each group.
````sql
WITH txn_revenue AS (
    SELECT 
        txn_id,
        CASE WHEN member = 't' THEN 'Member' ELSE 'Non-Member' END AS member_status,
        SUM(price * qty) AS total_revenue
    FROM balanced_tree.sales
    GROUP BY txn_id, member
)
SELECT 
    member_status,
    ROUND(AVG(total_revenue), 1) AS avg_revenue
FROM txn_revenue
GROUP BY member_status;
  ````

#### 📊 Query Result & Insights:
| member_status | avg_revenue |
| ------------- | ----------- |
| Non-Member    | 515.0     |
| Member        | 516.3      |

- The average revenue for member transactions was 515.0 and for non-member transactions was 516.3
---
