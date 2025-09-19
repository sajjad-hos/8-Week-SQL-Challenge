## Case Study #4 - D. Extra Challenge | Solutions | Result

Data Bank wants to try another option which is a bit more difficult to implement - they want to calculate data growth using an interest calculation, just like in a traditional savings account you might have with a bank.

If the annual interest rate is set at 6% and the Data Bank team wants to reward its customers by increasing their data allocation based off the interest calculated on a daily basis at the end of each day, how much data would be required for this option on a monthly basis?

Special notes:

- Data Bank wants an initial calculation which does not allow for compounding interest, however they may also be interested in a daily compounding interest calculation so you can try to perform this calculation if you have the stamina!

---
#### 🧠 My Approach & Solution:

````sql
WITH monthly_txn AS (
    SELECT 
        customer_id,
        txn_date,
        SUM(txn_amount) AS total_amount,
        CAST(DATE_TRUNC('month', txn_date) AS DATE) AS txn_month,
        EXTRACT(DAY FROM (txn_date - DATE_TRUNC('month', txn_date))) AS day_offset,
        CAST(SUM(txn_amount) AS DECIMAL(18, 2)) 
            * POWER((1 + 0.06/365), (txn_date - DATE '1900-01-01')) AS daily_interest
    FROM customer_transactions
    GROUP BY customer_id, txn_date
)

SELECT 
    customer_id,
    txn_month,
    ROUND(SUM(daily_interest * day_offset), 2) AS interest_sum
FROM monthly_txn
GROUP BY customer_id, txn_month
ORDER BY interest_sum DESC;
  ````

#### 📊 Query Result & Insights:
The result is displayed here only for a few rows, as the entire result set is large.
| customer_id | txn_month  | interest_sum |
| ----------- | ---------- | ------------ |
| 61          | 2020-03-01 | 196015935.04 |
| 82          | 2020-01-01 | 185392242.92 |
| 424         | 2020-02-01 | 151295061.95 |
| 5           | 2020-03-01 | 149674652.59 |
| 155         | 2020-03-01 | 135978971.53 |
| 131         | 2020-01-01 | 133980657.43 |
| 192         | 2020-02-01 | 133551309.87 |
| 45          | 2020-01-01 | 132359329.64 |
