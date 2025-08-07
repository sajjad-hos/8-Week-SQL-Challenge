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

- Joined relevant tables such as Combine customer_orders_cleaned, pizza_names, and runner_orders_cleaned to access both pizza details and delivery status.
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
