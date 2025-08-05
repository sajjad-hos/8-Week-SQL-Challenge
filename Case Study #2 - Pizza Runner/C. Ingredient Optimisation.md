<h2 id="case-study-2-pizza-runner">🍕 Case Study #2: Pizza Runner – C. Ingredient Optimisation</h2>

This section covers the solutions for `C. Ingredient Optimisation` from `Case Study #2: Pizza Runner`. Below are the questions accompanied by their solution and detailed answers.

<h2 id="data-cleaning">🔖 Required Data </h2>
To Be Added Later..

<h2 id="questions-and-solutions">📌Questions & Solutions</h2>

#### 1. What are the standard ingredients for each pizza?
#### 🧠 My Solution:

````sql
WITH split_toppings AS (
      SELECT pr.pizza_id, pn.pizza_name,
        	 TRIM(value)::int AS topping_id
      FROM pizza_recipes pr
      INNER JOIN pizza_names pn ON pr.pizza_id = pn.pizza_id,
      UNNEST(STRING_TO_ARRAY(pr.toppings, ',')) AS value
)

SELECT
      st.pizza_name,
      STRING_AGG(pt.topping_name::TEXT, ', ') AS standard_ingredients
FROM split_toppings st
INNER JOIN pizza_toppings pt ON st.topping_id = pt.topping_id
GROUP BY  st.pizza_name
ORDER BY  st.pizza_name;
````
<details> <summary><strong>Solution Approach:</strong></summary>

- Splited the comma-separated toppings in the pizza_recipes table into individual topping IDs.
- Joined the split topping IDs with the pizza_toppings table to get the corresponding topping names.
- Joined with the pizza_names table to retrieve the human-readable pizza names.
- Grouped the data by pizza name, and use STRING_AGG to concatenate the ingredient names back into a readable list for each pizza.
</details>

#### 📊 Query Result:
| pizza_name | standard_ingredients                                                  |
| ---------- | --------------------------------------------------------------------- |
| Meatlovers | BBQ Sauce, Pepperoni, Cheese, Salami, Chicken, Bacon, Mushrooms, Beef |
| Vegetarian | Tomato Sauce, Cheese, Mushrooms, Onions, Peppers, Tomatoes            |

####  ⚡Insights:
- Meatlovers pizza contains standard ingredients such as BBQ Sauce, Pepperoni, Cheese, Salami, Chicken, Bacon, Mushrooms, and Beef.
- Vegetarian pizza contains standard ingredients such as Tomato Sauce, Cheese, Mushrooms, Onions, Peppers, and Tomatoes.
---

#### 2. What was the most commonly added extra?
#### 🧠 My Solution:

````sql
WITH customer_orders_cleaned AS (
      SELECT 
          order_id,
          customer_id,
          pizza_id,
          TRIM(value) AS extras,
          order_time
      FROM customer_orders c,
           UNNEST(STRING_TO_ARRAY(c.extras, ',')) AS value
      WHERE c.extras IS NOT NULL
        AND LOWER(TRIM(c.extras)) != 'null'
        AND TRIM(value) != ''
)

SELECT 
      cco.extras,
      pt.topping_name,
      COUNT(*) AS extras_count
FROM customer_orders_cleaned cco
INNER JOIN pizza_toppings pt ON cco.extras::INT = pt.topping_id
GROUP BY cco.extras, pt.topping_name
ORDER BY extras_count DESC;
````
<details> <summary><strong>Solution Approach:</strong></summary>

- Created a CTE (customer_orders_cleaned) for storing the cleaned and split extras along with order details for easier reuse.
- Inside CTE, split extras into individual values using UNNEST with STRING_TO_ARRAY.
- Inside CTE, removed rows where extras is NULL, the string 'null' (case-insensitive), or empty strings after trimming spaces.
- Inside CTE, used TRIM to clean up whitespace from each extra value.
- Converted extras to integer and joined with the pizza_toppings table to get topping names.
- Grouped by the extra topping_id and name, count how many times each appears.
</details>

#### 📊 Query Result:
| extras | topping_name | extras_count |
| ------ | ------------ | ------------ |
| 1      | Bacon        | 4            |
| 5      | Chicken      | 1            |
| 4      | Cheese       | 1            |

####  ⚡Insights:
- Bacon was the favorite extra, chosen 4 times, while Chicken and Cheese were picked less often.
---
