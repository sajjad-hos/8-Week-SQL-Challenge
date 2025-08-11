## 🥑 Case Study #3 - Foodie-Fi – A. Customer Journey | Questions | Solutions | Result

#### Q. Based off the 8 sample customers provided in the sample from the subscriptions table, write a brief description about each customer’s onboarding journey. Try to keep it as short as possible - you may also want to run some sort of join to make your explanations a bit easier!

#### 🧠 My Approach & Solution:

- I created a CTE named customerwithplans that selects only the relevant records and 8 sample customers by joining the subscriptions and plans tables.
- In the main query, I used STRING_AGG to concatenate each customer’s plan names along with their start dates chronologically.
- I used GROUP BY to ensure that each customer’s entire onboarding journey is presented in a single row.

````sql
WITH customerwithplans AS (
  SELECT 
    s.customer_id,
    p.plan_name,
    s.start_date
  FROM subscriptions s
  INNER JOIN plans p ON s.plan_id = p.plan_id
  WHERE s.customer_id IN (1,2,11,13,15,16,18,19)
)
SELECT 
  customer_id,
  STRING_AGG(plan_name || ' (' || start_date || ')', ' → ' ORDER BY start_date) AS "onboarding journey"
FROM customerwithplans
GROUP BY customer_id
ORDER BY customer_id;
  ````

#### 📊 Query Result & Insights:
| customer_id | onboarding journey                                                         |
| ----------- | -------------------------------------------------------------------------- |
| 1           | trial (2020-08-01) → basic monthly (2020-08-08)                            |
| 2           | trial (2020-09-20) → pro annual (2020-09-27)                               |
| 11          | trial (2020-11-19) → churn (2020-11-26)                                    |
| 13          | trial (2020-12-15) → basic monthly (2020-12-22) → pro monthly (2021-03-29) |
| 15          | trial (2020-03-17) → pro monthly (2020-03-24) → churn (2020-04-29)         |
| 16          | trial (2020-05-31) → basic monthly (2020-06-07) → pro annual (2020-10-21)  |
| 18          | trial (2020-07-06) → pro monthly (2020-07-13)                              |
| 19          | trial (2020-06-22) → pro monthly (2020-06-29) → pro annual (2020-08-29)    |

- Customer 1 – Started with a trial, then switched to a Basic Monthly plan.
- Customer 2 – Started with a trial, then committed to a Pro Annual plan.
- Customer 11 – Started with a trial, but canceled shortly after.
- Customer 13 – Started with a trial, moved to Basic Monthly, then upgraded to Pro Monthly.
- Customer 15 – Started with a trial, upgraded to Pro Monthly, then canceled.
- Customer 16 – Trial user who went Basic Monthly, then upgraded to Pro Annual.
- Customer 18 – Trial user who upgraded straight to Pro Monthly.
- Customer 19 – Trial user who upgraded to Pro Monthly, then to Pro Annual.
---

