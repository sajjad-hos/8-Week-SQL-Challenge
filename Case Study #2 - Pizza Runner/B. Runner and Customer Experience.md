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

#### 2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?
#### 🧠 My Solution:

````sql
WITH runner_pickups AS (
  SELECT DISTINCT ON (roc.order_id)
      roc.runner_id AS runner_id,
      roc.order_id,
      coc.order_time,
      roc.pickup_time,
      (roc.pickup_time - coc.order_time) AS time_to_pickup,
      ROUND(EXTRACT(EPOCH FROM (roc.pickup_time - coc.order_time)) / 60, 2) AS time_to_pickup_mins
  FROM runner_orders_cleaned roc
  INNER JOIN customer_orders_cleaned coc ON roc.order_id = coc.order_id
  WHERE roc.pickup_time IS NOT NULL AND coc.order_time IS NOT NULL
)

SELECT 
  runner_id, ROUND(AVG(time_to_pickup_mins), 1) AS avg_arrival_minutes
FROM runner_pickups
GROUP BY runner_id
ORDER BY runner_id;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>

- Created a `CTE` to join the `runner_orders_cleaned` & `customer_orders_cleaned` tables, and filter out missing rows of pickup or order times.
- Calculated the time difference (in minutes) between when the order was placed and when the runner picked it up.
- Grouped the results by runner and calculated the average pickup time to see how long each runner usually took to arrive at the HQ.
</details>

#### 📊 Query Result:
| runner_id | avg_arrival_minutes |
| --------- | ------------------- |
| 1         | 14.3                |
| 2         | 20.0                |
| 3         | 10.5                |

####  ⚡Insights:
- Runner 3 was the fastest, taking an average of just 10.5 minutes to reach HQ.
- Runner 1 was the second fastest with 14.3 minutes on average, still reasonably efficient.
- Runner 2 had the longest average pickup time at 20 minutes. Maybe due to traffic delays, longer travel distances, or slower response times!
---

#### 3. Is there any relationship between the number of pizzas and how long the order takes to prepare?
#### 🧠 My Solution:

````sql
WITH pizza_counts AS (
  SELECT 
       order_id,
       COUNT(order_id) AS num_pizzas
  FROM customer_orders_cleaned
  GROUP BY order_id),

prep_times AS (
  SELECT 
    roc.order_id,
    coc.order_time,
    roc.pickup_time,
    EXTRACT(EPOCH FROM (roc.pickup_time::timestamp - coc.order_time::timestamp)) / 60 AS prep_time_mins
  FROM runner_orders_cleaned roc
  JOIN customer_orders_cleaned coc ON roc.order_id = coc.order_id
  WHERE roc.pickup_time IS NOT NULL AND coc.order_time IS NOT NULL
)

SELECT 
  pc.num_pizzas,
  ROUND(AVG(pt.prep_time_mins), 2) AS avg_prep_time_mins
FROM pizza_counts pc
JOIN prep_times pt ON pc.order_id = pt.order_id
GROUP BY pc.num_pizzas
ORDER BY pc.num_pizzas;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>

- Created a `CTE` by joining the `customer_orders_cleaned` and `runner_orders_cleaned` tables for filtering out orders with missing pickup or order times.
- Inside the CTE, counted the number of pizzas per order and calculated the preparation time as the difference between pickup time and order time.
- Grouped the results by number of pizzas and calculated the average prep time for each group to identify any relationship between order size and preparation time.
</details>

#### 📊 Query Result:
| num_pizzas | avg_prep_time_mins |
| ---------- | ------------------ |
| 1          | 12.36              |
| 2          | 18.38              |
| 3          | 29.28              |

####  ⚡Insights:
- There’s a clear positive relationship between the number of pizzas and preparation time.
- Orders with 1 pizza take about 12 minutes, while those with 3 pizzas take nearly 30 minutes on average.
- This trend shows that more pizzas mean slightly longer prep times. This is helpful insight for kitchen workload planning and delivery estimates.
---

#### 4. What was the average distance travelled for each customer?
#### 🧠 My Solution:

````sql
SELECT 
  coc.customer_id,
  AVG(roc.distance) AS avg_distance_km
FROM customer_orders_cleaned coc
JOIN runner_orders_cleaned roc ON coc.order_id = roc.order_id
WHERE roc.distance IS NOT NULL
GROUP BY coc.customer_id
ORDER BY coc.customer_id;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>

- Joined the customer_orders_cleaned and runner_orders_cleaned tables using order_id to connect each order with its delivery distance.
- Filtered out null values by excluding any rows where the distance was missing to ensure accuracy.
- Grouped by customer, calculated the average distance traveled for each customer_id by grouping the results and using `AVG(distance)`.

</details>

#### 📊 Query Result:
| customer_id | avg_distance_km    |
| ----------- | ------------------ |
| 101         | 20                 |
| 102         | 16.733333333333334 |
| 103         | 23.399999999999995 |
| 104         | 10                 |
| 105         | 25                 |

####  ⚡Insights:
- Customer 105 had the longest average delivery distance (25 km).
- Customer 104 had the shortest (10 km). Probably lives nearby!
- Customer 103 also receives deliveries from a relatively long distance, around 23.4 km.
- Customer 102 is closer to the middle range, averaging 16.7 km.
---

#### 5. What was the difference between the longest and shortest delivery times for all orders?
#### 🧠 My Solution:

````sql
SELECT  MAX(duration) - MIN(duration) AS "Difference between the longest and shortest delivery times"
FROM runner_orders_cleaned
WHERE duration IS NOT NULL;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>

- Filtered out any records with missing delivery time (`duration`) from the runner_orders_cleaned table.
- Found the `maximum` and `minimum` delivery times across all orders.
- Calculated how much delivery times varied by finding the difference between the fastest and slowest orders.
</details>

#### 📊 Query Result:
| Difference between the longest and shortest delivery times |
| ---------------------------------------------------------- |
| 30                                                         |

####  ⚡Insights:
- The difference between the longest and shortest delivery times for all orders was 30 minutes.
---

#### 6. What was the average speed for each runner for each delivery and do you notice any trend for these values?
#### 🧠 My Solution:

````sql
SELECT DISTINCT ON (roc.order_id)
  roc.order_id,
  roc.runner_id,
  coc.customer_id,
  roc.distance, 
  roc.duration,
  ROUND((roc.distance::numeric / (roc.duration::numeric / 60)), 2) AS avg_speed_kmph
FROM runner_orders_cleaned roc
LEFT JOIN customer_orders_cleaned coc ON coc.order_id = roc.order_id
WHERE roc.distance IS NOT NULL AND roc.duration IS NOT NULL
ORDER BY roc.order_id, roc.runner_id;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>

- Filtered orders to include only those with valid distance and duration values.
- Calculated the average speed for each delivery by dividing distance by time (converted duration to `hours`).
- Selected distinct orders with their runner, customer, and calculated average speed to analyze delivery performance.
</details>

#### 📊 Query Result:
| order_id | runner_id | customer_id | distance | duration | avg_speed_kmph |
| -------- | --------- | ----------- | -------- | -------- | -------------- |
| 1        | 1         | 101         | 20       | 32       | 37.50          |
| 2        | 1         | 101         | 20       | 27       | 44.44          |
| 3        | 1         | 102         | 13.4     | 20       | 40.20          |
| 4        | 2         | 103         | 23.4     | 40       | 35.10          |
| 5        | 3         | 104         | 10       | 15       | 40.00          |
| 7        | 2         | 105         | 25       | 25       | 60.00          |
| 8        | 2         | 102         | 23.4     | 15       | 93.60          |
| 10       | 1         | 104         | 10       | 10       | 60.00          |

####  ⚡Insights:
- Runner 1 maintained average speeds between 37.5 and 60 km/h.
- Runner 2 had the widest speed range from 35.1 to a suspiciously high 93.6 km/h in Order 8, likely a data error!
- Runner 3 maintained an average speed of 40.2 km/h.
---

#### 7. What is the successful delivery percentage for each runner?
#### 🧠 My Solution:

````sql
SELECT 
  runner_id,
  ROUND(COUNT(CASE WHEN distance IS NOT NULL AND duration IS NOT NULL THEN 1 END) * 100.0 / COUNT(*), 
    0) || ' %' AS successful_delivery_rate
FROM runner_orders_cleaned
GROUP BY runner_id
ORDER BY runner_id;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>

- Counted how many deliveries had both `distance` and `duration`, which were considered successful.
- Divided the successful deliveries by the total number of deliveries for each runner.
- Converted the result into a percentage to show each runner’s delivery success rate.
</details>

#### 📊 Query Result:
| runner_id | successful_delivery_rate |
| --------- | ------------------------ |
| 1         | 100 %                    |
| 2         | 75 %                     |
| 3         | 50 %                     |

####  ⚡Insights:
- Runner 1 completed all deliveries successfully with a 100% success rate.
- Runner 2 had a 75% success rate, meaning some deliveries were missing data.
- Runner 3’s success rate was 50%. Many deliveries may need better tracking or follow-up!
---
