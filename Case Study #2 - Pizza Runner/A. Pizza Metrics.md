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

#### 1. How many pizzas were ordered?
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
- A total of 14 pizzas were ordered.
---

#### 2. How many unique customer orders were made?
#### 🧠 My Solution:

````sql
SELECT COUNT(DISTINCT order_id) AS "Total Unique Customer Orders"
FROM customer_orders_cleaned;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>

Counted the `distinct` `order_ids` in the `customer_orders_cleaned` table for the total number of separate orders placed by customers.</details>

#### 📊 Query Result:
| Total Unique Customer Orders |
| ---------------------------- |
| 10                           |

#### ⚡Insights:
- A total of 10 unique customers made the orders.

---

#### 3. How many successful orders were delivered by each runner?
#### 🧠 My Solution:

````sql
SELECT runner_id, count(*) AS "Total Successful Orders"
FROM runner_orders_cleaned
WHERE cancellation IS NULL or cancellation = ''
GROUP BY runner_id;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>
  <ul>
    <li>Filtered successful orders by checking where cancellation is NULL or '' (empty).</li>
    <li>Grouped the data by runner_id to separate results per runner.</li>
    <li>Counted the number of orders for each runner using COUNT(*).</li>
  </ul> </details>

#### 📊 Query Result:
| runner_id | Total Successful Orders |
| --------- | ----------------------- |
| 1         | 4                       |
| 2         | 3                       |
| 3         | 1                       |

####  ⚡Insights:
- Runner 1 led the pack with 4 successful deliveries, showing some serious hustle!
- Runner 2 wasn’t far behind with 3 orders delivered.
- Runner 3 had 1 successful delivery, doing their part to keep pizzas moving.

---

#### 4. How many of each type of pizza were delivered?
#### 🧠 My Solution:

````sql
SELECT 
    pn.pizza_name,
    COUNT(*) AS total_pizzas_delivered
FROM customer_orders_cleaned coc
INNER JOIN runner_orders_cleaned roc ON coc.order_id = roc.order_id
INNER JOIN pizza_names pn ON coc.pizza_id = pn.pizza_id
WHERE roc.cancellation IS NULL OR roc.cancellation = ''
GROUP BY pn.pizza_name
ORDER BY total_pizzas_delivered DESC;

  ````
<details> <summary><strong>Solution Approach:</strong></summary>
  <ul>
    <li> Joined customer_orders_cleaned, runner_orders_cleaned, and pizza_names to link orders with runners and pizza types.</li>
    <li> Useed WHERE roc.cancellation IS NULL OR roc.cancellation = '' to include only non-cancelled (delivered) orders.</li>
    <li> Grouped by pizza_name and use COUNT(*) to get the total number of each pizza type delivered.</li>
  </ul> </details>

#### 📊 Query Result:
| pizza_name | total_pizzas_delivered |
| ---------- | ---------------------- |
| Meatlovers | 9                      |
| Vegetarian | 3                      |

####  ⚡Insights:
- Meatlovers pizza was the clear favorite, with 9 delivered!
- Vegetarian pizzas were ordered too, but only 3 in total. Seems most people like meaty one!
---

#### 5. How many Vegetarian and Meatlovers were ordered by each customer?
#### 🧠 My Solution:

````sql
SELECT 
    coc.customer_id,
    pn.pizza_name,
    COUNT(coc.pizza_id) AS total_pizzas_delivered
FROM customer_orders_cleaned coc
INNER JOIN pizza_names pn ON coc.pizza_id = pn.pizza_id
WHERE pn.pizza_name IN ('Meatlovers', 'Vegetarian')
GROUP BY coc.customer_id, pn.pizza_name
ORDER BY coc.customer_id, total_pizzas_delivered DESC;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>
  <ul>
    <li> Joined customer_orders_cleaned with pizza_names tables to get pizza names per order.</li>
    <li> Filtered pizza types using WHERE to include only 'Meatlovers' and 'Vegetarian'.</li>
    <li> Useded Group by customer_id and pizza_name, then COUNT() to total pizzas ordered.</li>
  </ul> </details>

#### 📊 Query Result:
| customer_id | pizza_name | total_pizzas_delivered |
| ----------- | ---------- | ---------------------- |
| 101         | Meatlovers | 2                      |
| 101         | Vegetarian | 1                      |
| 102         | Meatlovers | 2                      |
| 102         | Vegetarian | 1                      |
| 103         | Meatlovers | 3                      |
| 103         | Vegetarian | 1                      |
| 104         | Meatlovers | 3                      |
| 105         | Vegetarian | 1                      |

####  ⚡Insights:
- Customers 101 to 104 liked both pizzas, but Meatlovers was their favorite.
- Customers 103 and 104 ordered 3 Meatlovers each. Big meat fans!
- Customer 105 only ordered 1 Vegetarian pizza. Veggies have fans too!

---

#### 6. What was the maximum number of pizzas delivered in a single order?
#### 🧠 My Solution:

````sql
SELECT customer_id,
       order_id,
       COUNT(order_id) AS pizza_count
FROM customer_orders_cleaned
GROUP BY customer_id, order_id
ORDER BY pizza_count DESC
LIMIT 1;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>
  <ul>
    <li> Grouped orders by <code>order_id</code> (and optionally <code>customer_id</code>) to count pizzas per order.</li>
    <li> Counted rows per order using <code>COUNT(*)</code> to get the number of pizzas in each order.</li>
    <li> Sorted in descending order and select the <code>top</code> result to find the maximum pizza count in a single order.</li>
  </ul> </details>

#### 📊 Query Result:
| customer_id | order_id | pizza_count |
| ----------- | -------- | ----------- |
| 103         | 4        | 3           |

####  ⚡Insights:
- Maximum 3 pizzas were delivered in a single order.

---

#### 7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
#### 🧠 My Solution:

````sql
SELECT
      coc.customer_id,
      SUM(CASE WHEN (coc.exclusions <> '' OR coc.extras <> '') THEN 1 ELSE 0 END) AS pizzas_with_changes,
      SUM(CASE WHEN (coc.exclusions = '' AND coc.extras = '') THEN 1 ELSE 0 END) AS pizzas_without_changes
FROM customer_orders_cleaned coc
INNER JOIN runner_orders_cleaned roc ON coc.order_id = roc.order_id
WHERE roc.cancellation IS NULL OR roc.cancellation = ''
GROUP BY coc.customer_id
ORDER BY coc.customer_id;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>
  <ul>
    <li> Joined customer_orders_cleaned with runner_orders_cleaned on order_id to filter only delivered (non-cancelled) pizzas.</li>
    <li> Used conditional aggregation with CASE statements to count pizzas that have any exclusions or extras (indicating changes) vs. those with none.</li>
    <li> Grouped results by customer_id to get counts per customer.</li>
  </ul> </details>

#### 📊 Query Result:
| customer_id | pizzas_with_changes | pizzas_without_changes |
| ----------- | ------------------- | ---------------------- |
| 101         | 0                   | 2                      |
| 102         | 0                   | 3                      |
| 103         | 3                   | 0                      |
| 104         | 2                   | 1                      |
| 105         | 1                   | 0                      |

####  ⚡Insights:
- Customers 101 and 102 made no changes at all. Simple and satisfied customers!
- Customer 103 went all-in on customization, with every pizza modified.
- Customer 104 mixed it up, such as 2 with changes, 1 without changes.
- Customer 105 made one order, but made it count with a personal touch!

---

#### 8. How many pizzas were delivered that had both exclusions and extras?
#### 🧠 My Solution:

````sql
SELECT coc.customer_id, COUNT(*) AS pizzas_with_exclusions_and_extras
FROM customer_orders_cleaned coc
INNER JOIN runner_orders_cleaned roc USING (order_id)
WHERE (roc.cancellation IS NULL OR roc.cancellation = '')
      AND coc.exclusions IS NOT NULL AND coc.exclusions <> ''
      AND coc.extras IS NOT NULL AND coc.extras <> ''
GROUP BY coc.customer_id
ORDER BY coc.customer_id;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>
  <ul>
    <li> To consider only successful deliveries, joined orders with delivery info.</li>
    <li> Filtered pizzas where both exclusions and extras columns are not null and not empty, meaning both changes exist</li>
    <li> Grouped by customer and count such pizzas per customer.</li>
  </ul> </details>

#### 📊 Query Result:
| customer_id | pizzas_with_exclusions_and_extras |
| ----------- | --------------------------------- |
| 104         | 1                                 |

####  ⚡Insights:
- Customer 104 made the only fully customized pizza order with both extras and exclusions!

---

#### 9. What was the total volume of pizzas ordered for each hour of the day?
#### 🧠 My Solution:

````sql
SELECT 
    EXTRACT(HOUR FROM coc.order_time) AS order_hour,
    COUNT(*) AS total_pizzas_ordered
FROM customer_orders_cleaned coc
GROUP BY order_hour
ORDER BY order_hour;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>
  <ul>
    <li> Extracted the hour from the order timestamp to group pizzas by hour of the day.</li>
    <li> Joined with delivery info and filter only successful (non-cancelled) orders to count delivered pizzas.</li>
    <li> Grouped by extracted hour and count pizzas for each hour to get the total volume ordered.</li>
  </ul> </details>

#### 📊 Query Result:
| order_hour | total_pizzas_ordered |
| ---------- | -------------------- |
| 11         | 1                    |
| 13         | 3                    |
| 18         | 3                    |
| 19         | 1                    |
| 21         | 3                    |
| 23         | 3                    |

####  ⚡Insights:
- Customers ordered more pizza at 1 PM and again at 6 PM, 9 PM, and 11 PM, with 3 orders each time.
- It’s quieter at 11 AM and 7 PM with just 1 order! Maybe too early for lunch and too late for dinner!

---

#### 10. What was the volume of orders for each day of the week?
#### 🧠 My Solution:

````sql
SELECT 
    TO_CHAR(order_time, 'Day') AS day_of_week,
    COUNT(*) AS total_pizzas_ordered
FROM customer_orders_cleaned coc
GROUP BY day_of_week
ORDER BY total_pizzas_ordered DESC;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>
  <ul>
    <li> Extracted the day of the week from the <code>order_time</code> column to categorize each order by day..</li>
    <li> Grouped orders by the day of the week to aggregate counts for each day.</li>
    <li> Counted total orders per day and ordered the results to identify the busiest days.</li>
  </ul> </details>

#### 📊 Query Result:
| day_of_week | total_pizzas_ordered |
| ----------- | -------------------- |
| Saturday    | 5                    |
| Wednesday   | 5                    |
| Thursday    | 3                    |
| Friday      | 1                    |

####  ⚡Insights:
- Saturday and Wednesday were the busiest days with 5 orders each. Customers love pizza both midweek and on weekends!
- Thursday had a decent number of orders with 3 orders!
- Friday was pretty quiet with just one order! Seems the customer was too busy to grab a slice!

---
