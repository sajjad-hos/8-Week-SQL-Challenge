<h2 id="case-study-2-pizza-runner">🍕 Case Study #2: Pizza Runner – B. Runner and Customer Experience</h2>

This section covers the solutions for `B. Runner and Customer Experience` from `Case Study #2: Pizza Runner`. Below are the questions accompanied by their solution and detailed answers.

<h2 id="data-cleaning">🔖 Required Data </h2>

The two tables `customer_orders` and `runner_orders` contains all the data for answering all questions in the A: Pizza Metrics section. Since both tables have data quality and consistency issues, I cleaned the data and stored the results in temporary tables for error-free analysis. Please make sure to use the cleaned tables (`customer_orders_cleaned` and/or `runner_orders_cleaned`) with the solutions below, as using the original tables may lead to incorrect results.

#### Table: `customer_orders`
The query to create a cleaned, temporary table called customer_orders_cleaned.
````sql
DROP TABLE IF EXISTS customer_orders_cleaned;
CREATE TEMPORARY TABLE customer_orders_cleaned AS
SELECT
      order_id,
      customer_id,
      pizza_id,
      CASE WHEN exclusions IS NULL OR exclusions LIKE 'null' THEN '' ELSE exclusions END AS exclusions,
      CASE WHEN extras IS NULL OR extras LIKE 'null' THEN '' ELSE extras END AS extras,
      order_time
FROM customer_orders;
  ````

#### Table: `runner_orders`
The query to create a cleaned, temporary table called runner_orders_cleaned.
````sql
DROP TABLE IF EXISTS runner_orders_cleaned;
CREATE TEMPORARY TABLE runner_orders_cleaned AS
SELECT 
    order_id,
    runner_id,
    CASE WHEN pickup_time IS NULL OR pickup_time LIKE 'null' THEN NULL ELSE pickup_time END AS pickup_time,
    CASE WHEN distance IS NULL OR distance LIKE 'null' THEN NULL ELSE regexp_replace(distance, '[a-zA-Z ]+', '', 'g') END AS distance,
    CASE WHEN duration IS NULL OR duration LIKE 'null' THEN NULL ELSE regexp_replace(duration, '[a-zA-Z ]+', '', 'g') END AS duration,
    CASE WHEN cancellation IS NULL OR cancellation LIKE 'null' THEN NULL ELSE cancellation END AS cancellation
FROM runner_orders;

-- Change Data Type
ALTER TABLE runner_orders_cleaned
ALTER COLUMN pickup_time TYPE TIMESTAMP USING pickup_time::TIMESTAMP,
ALTER COLUMN distance TYPE FLOAT USING distance::FLOAT,
ALTER COLUMN duration TYPE INT USING duration::INT;
  ````

<h2 id="questions-and-solutions">📌Questions & Solutions</h2>

#### 1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)
#### 🧠 My Solution:

````sql
SELECT
  ((registration_date::date - DATE '2021-01-01') / 7 + 1) AS week_number,
  COUNT(runner_id) AS runners_signed_up
FROM runners
GROUP BY week_number
ORDER BY week_number;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>

- First, calculate the week number. For each runner’s `registration_date`, identify how many days have passed since the start date (`2021-01-01`), divide by 7 to get the week index, and add 1 so the first week starts at 1.
- Next, group the data by this calculated `week_number` and count the number of unique `runner_ids` who signed up within each week.
</details>

#### 📊 Query Result:
| week_number | runners_signed_up |
| ----------- | ----------------- |
| 1           | 2                 |
| 2           | 1                 |
| 3           | 1                 |

####  ⚡Insights:
- In the first week of January 2021, 2 runners signed up.
- In weeks 2 & 3, sign-ups slowed down to just 1 runner per week.
---
