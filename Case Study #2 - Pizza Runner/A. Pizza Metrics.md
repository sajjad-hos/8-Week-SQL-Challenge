<h2 id="case-study-2-pizza-runner">🍕 Case Study #2: Pizza Runner – A. Pizza Metrics</h2>

This section focuses exclusively on the solutions for `Case Study #2: Pizza Runner`, specifically covering section `A: Pizza Metrics`. Below, you’ll find the questions along with their detailed solutions.

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

### 1. How many pizzas were ordered?
#### 🧠 My Solution:

````sql
SELECT COUNT(order_id) AS "Total pizzas Ordered"
FROM customer_orders_cleaned;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>

I just counted all the orders in the `customer_orders_cleaned` table. Each row is one pizza, so counting the `order_id` gives us the total number of pizzas ordered. </details>

#### 📊 Query Result:
| Total pizzas Ordered |
| -------------------- |
| 14                   |

####  ⚡Insights:
- Total 14 pizzas were ordered.
---

### 2. How many unique customer orders were made?
#### 🧠 My Solution:

````sql
SELECT COUNT(DISTINCT order_id) AS "Total Unique Customer Orders"
FROM customer_orders_cleaned;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>

I counted the `distinct` `order_ids` in the `customer_orders_cleaned` table for the total number of separate orders placed by customers.</details>

#### 📊 Query Result:
| Total Unique Customer Orders |
| ---------------------------- |
| 10                           |

#### ⚡Insights:
- Total 10 unique customer were made the orders.

---


<h2 id="final-notes">📝 Final Notes</h2>
Thank you for reviewing my solution! If you spot any errors or have suggestions on how I can improve, please feel free to reach out to me on <a href="https://www.linkedin.com/in/sajjad-hos" target="_blank" rel="noopener noreferrer">LinkedIn</a>. I appreciate your feedback!
