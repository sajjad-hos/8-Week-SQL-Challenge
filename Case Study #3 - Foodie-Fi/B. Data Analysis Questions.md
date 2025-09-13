## 🥑 Case Study #3 - Foodie-Fi – B. Data Analysis Questions | Solutions | Result

#### Q1: How many customers has Foodie-Fi ever had?
#### 🧠 My Approach & Solution:
````sql
SELECT COUNT(DISTINCT customer_id) AS total_unique_customers
FROM subscriptions;
  ````

#### 📊 Query Result & Insights:
| total_unique_customers |
| ---------------------- |
| 1000                   |

- Foodie-Fi has had 1000 unique customers in total.
---

#### Q2: What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value?
#### 🧠 My Approach & Solution:
````sql
SELECT
  EXTRACT(MONTH FROM start_date) AS month_number,
  TO_CHAR(start_date, 'Month') AS month_name,
  COUNT(s.customer_id) AS trial_plan_subscriptions
FROM foodie_fi.subscriptions AS s
INNER JOIN foodie_fi.plans AS p ON s.plan_id = p.plan_id
WHERE s.plan_id = 0
GROUP BY month_number, month_name, DATE_TRUNC('month', start_date)
ORDER BY month_number;
  ````

#### 📊 Query Result & Insights:
| month_number | month_name | trial_plan_subscriptions |
| ------------ | ---------- | ------------------------ |
| 1            | January    | 88                       |
| 2            | February   | 68                       |
| 3            | March      | 94                       |
| 4            | April      | 81                       |
| 5            | May        | 88                       |
| 6            | June       | 79                       |
| 7            | July       | 89                       |
| 8            | August     | 88                       |
| 9            | September  | 87                       |
| 10           | October    | 79                       |
| 11           | November   | 75                       |
| 12           | December   | 84                       |

#### Q3: What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name.
#### 🧠 My Approach & Solution:
````sql
SELECT 
  p.plan_name,
  COUNT(s.customer_id) AS count_of_events
FROM foodie_fi.subscriptions AS s
INNER JOIN foodie_fi.plans AS p ON s.plan_id = p.plan_id
WHERE s.start_date >= '2021-01-01'
GROUP BY p.plan_name;
  ````

#### 📊 Query Result & Insights:
| plan_name     | count_of_events |
| ------------- | --------------- |
| pro annual    | 63              |
| churn         | 71              |
| pro monthly   | 60              |
| basic monthly | 8               |

#### Q4: What is the customer count and percentage of customers who have churned rounded to 1 decimal place?
#### 🧠 My Approach & Solution:
````sql
SELECT
    COUNT(DISTINCT customer_id) AS total_churned,
    ROUND(100.0 * COUNT(DISTINCT customer_id) / (SELECT COUNT(DISTINCT customer_id) FROM foodie_fi.subscriptions), 1) || '%' AS churn_percent
FROM foodie_fi.subscriptions
WHERE plan_id = 4;
  ````

#### 📊 Query Result & Insights:
| total_churned | churn_percent |
| ------------- | ------------- |
| 307           | 30.7%         |

- A total of 307 customers have churned, which is about 30.7% of all customers.

#### Q5: How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?
#### Q6: What is the number and percentage of customer plans after their initial free trial?
#### Q7: What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?
#### Q8: How many customers have upgraded to an annual plan in 2020?
#### Q9: How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?
#### Q10: Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)
#### Q11: How many customers downgraded from a pro monthly to a basic monthly plan in 2020?

