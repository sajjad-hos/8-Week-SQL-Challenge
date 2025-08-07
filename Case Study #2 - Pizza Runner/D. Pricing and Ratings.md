<h2 id="case-study-2-pizza-runner">🍕 Case Study #2: Pizza Runner – D. Pricing and Ratings</h2>

This section covers Part `D. Pricing and Ratings` from `Case Study #2: Pizza Runner`, including each question’s approach, solution, and result.

<h2 id="data-cleaning">🔖 Required Data </h2>

I used the cleaned tables (`customer_orders_cleaned` and/or `runner_orders_cleaned`) as the original tables contain nulls or formatting issues.

#### Query for cleaned and temporary `customer_orders` table data
````sql
DROP TABLE IF EXISTS customer_orders_cleaned;
CREATE TEMPORARY TABLE customer_orders_cleaned AS
    SELECT 
          order_id,
          customer_id, 
          pizza_id,
          CASE WHEN exclusions IS NULL OR exclusions ILIKE 'null' THEN '' ELSE exclusions END AS exclusions,
          CASE WHEN extras IS NULL OR extras ILIKE 'null' THEN '' ELSE extras END AS extras,
          order_time
FROM customer_orders;
````
#### Query for cleaned and temporary `runner_orders` table data
````sql
DROP TABLE IF EXISTS runner_orders_cleaned;
CREATE TEMPORARY TABLE runner_orders_cleaned AS
    SELECT order_id,runner_id,
        CASE WHEN pickup_time IS NULL OR pickup_time ILIKE 'null' THEN ''
            ELSE pickup_time
        END AS pickup_time,
        CASE
            WHEN distance IS NULL OR distance ILIKE 'null' THEN ''
            ELSE regexp_replace(distance, '[a-zA-Z ]+', '', 'g')
        END AS distance,
        CASE
            WHEN duration IS NULL OR duration ILIKE 'null' THEN ''
            ELSE regexp_replace(duration, '[a-zA-Z ]+', '', 'g')
        END AS duration,
        CASE
            WHEN cancellation IS NULL OR cancellation ILIKE 'null' THEN ''
            ELSE cancellation
        END AS cancellation
FROM runner_orders;

-- Convert data types on runner_orders_cleaned
ALTER TABLE runner_orders_cleaned
ALTER COLUMN pickup_time TYPE TIMESTAMP USING NULLIF(pickup_time, '')::TIMESTAMP,
ALTER COLUMN distance TYPE FLOAT USING NULLIF(distance, '')::FLOAT,
ALTER COLUMN duration TYPE INT USING NULLIF(duration, '')::INT;
````

<h2 id="questions-and-solutions">📌Questions & Solutions</h2>

#### 1. If a Meat Lovers pizza costs $12 and Vegetarian costs $10 and there were no charges for changes - how much money has Pizza Runner made so far if there are no delivery fees?
#### 🧠 My Solution:

````sql
SELECT 
      SUM(CASE
              WHEN coc.pizza_id = 1 THEN 12
              WHEN coc.pizza_id = 2 THEN 10
              ELSE 0
          END) AS total_revenue
FROM customer_orders_cleaned coc
INNER JOIN pizza_names pn ON coc.pizza_id = pn.pizza_id 
INNER JOIN runner_orders_cleaned roc ON  coc.order_id = roc.order_id 
WHERE roc.cancellation = '';
````
<details> <summary><strong>Solution Approach:</strong></summary>

- Joined relevant tables such as customer_orders_cleaned, pizza_names, and runner_orders_cleaned to access both pizza details and delivery status.
- Assigned pizza prices using a CASE statement to assign $12 for Meat Lovers (pizza_id = 1) and $10 for Vegetarian (pizza_id = 2).
- Calculated the total revenue by summing the assigned prices for each valid order.
</details>

#### 📊 Query Result:
| total_revenue |
| ------------- |
| 138           |

####  ⚡Insights:
- Pizza Runner has earned a total of $138 from successfully delivered orders.
---

#### 2. What if there was an additional $1 charge for any pizza extras?
- Add cheese is $1 extra
#### 🧠 My Solution:

````sql
WITH pizza_prices AS (
    SELECT 
        pizza_id,
        CASE 
            WHEN pizza_id = 1 THEN 12  -- Meat Lovers
            WHEN pizza_id = 2 THEN 10  -- Vegetarian
            ELSE 0
        END AS base_cost
    FROM pizza_names
),
orders_with_extras AS (
    SELECT 
        coc.order_id,
        pp.base_cost,
        coc.extras,
        CASE 
            WHEN coc.extras IS NULL OR coc.extras = '' THEN 0
            ELSE array_length(string_to_array(coc.extras, ', '), 1)
        END AS extras_count
    FROM customer_orders_cleaned coc
    INNER JOIN pizza_prices pp ON coc.pizza_id = pp.pizza_id
    INNER JOIN runner_orders_cleaned roc ON coc.order_id = roc.order_id
    WHERE roc.cancellation = ''
)
SELECT 
    SUM(base_cost + extras_count) AS total_revenue_with_extras
FROM orders_with_extras;
````
<details> <summary><strong>Solution Approach:</strong></summary>

- Joined relevant tables such as customer_orders_cleaned, pizza_prices, and runner_orders_cleaned.
- Calculated pizza cost with CASE for base price and array_length(string_to_array(..., ', ')) to count $1 extras.
- Filtered delivered orders with WHERE cancellation = '' and compute SUM(base_cost + extras_count) for total revenue.
</details>

#### 📊 Query Result:
| total_revenue_with_extras |
| ------------------------- |
| 142                       |

####  ⚡Insights:
- The total revenue of $142 reflects all completed orders’ base pizza prices plus $1 for each extra topping added.
---

#### 3. The Pizza Runner team now wants to add an additional ratings system that allows customers to rate their runner, how would you design an additional table for this new dataset - generate a schema for this new table and insert your own data for ratings for each successful customer order between 1 to 5.

#### 🧠 My Solution:

````sql
DROP TABLE IF EXISTS runner_rating;
CREATE TABLE runner_rating ( order_id INTEGER, customer_rating INTEGER, customer_review VARCHAR(100));
INSERT INTO runner_rating (order_id, customer_rating, customer_review)
VALUES 
    (1, 3, 'Average service'),
    (2, 5, 'Timely and friendly delivery!'),
    (3, 2, 'Waited too long for my pizza'),
    (4, 4, 'Runner found the place eventually, pizza was warm'),
    (5, 1, 'Waited so long, not satisfied'),
    (6, NULL, NULL),                                                -- Order 6 was cancelled, so rating and review were kept as NULL
    (7, 5, 'Excellent service, very fast!'),
    (8, 3, 'Left pizza at the door, a bit careless'),
    (9, NULL, NULL),                                                -- Order 9 was cancelled, so rating and review were kept as NULL
    (10, 4, 'Timing was a little delayed, but tasty pizza');

SELECT * FROM runner_rating;
````
<details> <summary><strong>Solution Approach:</strong></summary>

- Some orders were canceled, so I kept them NULL, and the rating column and review value were added as my own.
</details>

#### 📊 Query Result:
| order_id | customer_rating | customer_review                                   |
| -------- | --------------- | ------------------------------------------------- |
| 1        | 3               | Average service                                   |
| 2        | 5               | Timely and friendly delivery!                     |
| 3        | 2               | Waited too long for my pizza                      |
| 4        | 4               | Runner found the place eventually, pizza was warm |
| 5        | 1               | Waited so long, not satisfied                     |
| 6        |                 |                                                   |
| 7        | 5               | Excellent service, very fast!                     |
| 8        | 3               | Left pizza at the door, a bit careless            |
| 9        |                 |                                                   |
| 10       | 4               | Timing was a little delayed, but tasty pizza      |

---


#### 4. Using your newly generated table - can you join all of the information together to form a table which has the following information for successful deliveries?
- customer_id
- order_id
- runner_id
- rating
- order_time
- pickup_time
- Time between order and pickup
- Delivery duration
- Average speed
- Total number of pizzas

#### 🧠 My Solution:

````sql
SELECT 
    coc.customer_id, 
    coc.order_id, 
    roc.runner_id, 
    rr.customer_rating, 
    coc.order_time, 
    roc.pickup_time, 
    DATE_PART('minute', roc.pickup_time - coc.order_time) AS time_between_order_and_pickup, 
    roc.duration AS delivery_duration, 
    ROUND(AVG((roc.distance / roc.duration * 60)::numeric), 1) AS average_speed, 
    COUNT(coc.pizza_id) AS total_number_of_pizzas
FROM customer_orders_cleaned coc
LEFT JOIN runner_orders_cleaned roc ON coc.order_id = roc.order_id 
LEFT JOIN runner_rating rr ON coc.order_id = rr.order_id
WHERE roc.cancellation = ''
GROUP BY 
    coc.customer_id, coc.order_id, roc.runner_id, rr.customer_rating,
    coc.order_time, roc.pickup_time, roc.duration  
ORDER BY coc.order_id;
````

#### 📊 Query Result:
| customer_id | order_id | runner_id | customer_rating | order_time          | pickup_time         | time_between_order_and_pickup | delivery_duration | average_speed | total_number_of_pizzas |
| ----------- | -------- | --------- | --------------- | ------------------- | ------------------- | ----------------------------- | ----------------- | ------------- | ---------------------- |
| 101         | 1        | 1         | 3               | 2020-01-01 18:05:02 | 2020-01-01 18:15:34 | 10                            | 32                | 37.5          | 1                      |
| 101         | 2        | 1         | 5               | 2020-01-01 19:00:52 | 2020-01-01 19:10:54 | 10                            | 27                | 44.4          | 1                      |
| 102         | 3        | 1         | 2               | 2020-01-02 23:51:23 | 2020-01-03 00:12:37 | 21                            | 20                | 40.2          | 2                      |
| 103         | 4        | 2         | 4               | 2020-01-04 13:23:46 | 2020-01-04 13:53:03 | 29                            | 40                | 35.1          | 3                      |
| 104         | 5        | 3         | 1               | 2020-01-08 21:00:29 | 2020-01-08 21:10:57 | 10                            | 15                | 40.0          | 1                      |
| 105         | 7        | 2         | 5               | 2020-01-08 21:20:29 | 2020-01-08 21:30:45 | 10                            | 25                | 60.0          | 1                      |
| 102         | 8        | 2         | 3               | 2020-01-09 23:54:33 | 2020-01-10 00:15:02 | 20                            | 15                | 93.6          | 1                      |
| 104         | 10       | 1         | 4               | 2020-01-11 18:34:49 | 2020-01-11 18:50:20 | 15                            | 10                | 60.0          | 2                      |

---

#### 5. If a Meat Lovers pizza was $12 and Vegetarian $10 fixed prices with no cost for extras and each runner is paid $0.30 per kilometre traveled - how much money does Pizza Runner have left over after these deliveries?

#### 🧠 My Solution:

````sql
WITH order_costs AS (
    SELECT 
        coc.order_id,
        SUM(CASE WHEN coc.pizza_id = 1 THEN 12 ELSE 10 END) AS pizza_cost,
        ROUND((0.30 * roc.distance)::numeric, 2) AS delivery_cost
    FROM customer_orders_cleaned coc
    INNER JOIN runner_orders_cleaned roc ON coc.order_id = roc.order_id
    WHERE cancellation = ''
    GROUP BY coc.order_id, roc.distance
)
SELECT 
    SUM(pizza_cost) AS total_sales_amount,
    SUM(delivery_cost) AS total_runner_cost,
    ROUND(SUM(pizza_cost - delivery_cost), 2) AS net_profit
FROM order_costs;;
````
<details> <summary><strong>Solution Approach:</strong></summary>

- Joined relevant tables such as customer_orders_cleaned, pizza_names, and runner_orders_cleaned to get the distance for delivery cost.
- Calculated total pizza sales by adding $12 for each Meat Lovers pizza and $10 for each Vegetarian pizza sold.
- Calculated total delivery cost by multiplying each delivery distance by $0.30.
- Subtracted total delivery cost from total pizza sales to find the net profit.
</details>

#### 📊 Query Result:
| total_sales_amount | total_runner_cost | net_profit |
| ------------------ | ----------------- | ---------- |
| 138                | 43.56             | 94.44      |

####  ⚡Insights:
- The total $138 earned from selling all pizzas.
- The total $43.56 paid to runners for delivering pizzas (based on distance).
- The total $94.44 was left after paying the runners. This is the profit that Pizza Runner made!
---
