<h2 id="case-study-2-pizza-runner">🍕 Case Study #2: Pizza Runner – D. Pricing and Ratings</h2>

This section covers the solutions for `D. Pricing and Ratings` from `Case Study #2: Pizza Runner`. Below are the questions accompanied by their solution and detailed answers.

<h2 id="data-cleaning">🔖 Required Data </h2>
To Be Added Later..

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

- Joined relevant tables such as customer_orders_cleaned, pizza_names, and runner_orders_cleaned to access pizza details and delivery status.
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
