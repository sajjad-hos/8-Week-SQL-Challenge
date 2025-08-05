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

- Split the comma-separated toppings in the pizza_recipes table into individual topping IDs.
- Join the split topping IDs with the pizza_toppings table to get the corresponding topping names.
- Join with the pizza_names table to retrieve the human-readable pizza names.
- Group the data by pizza name, and use STRING_AGG to concatenate the ingredient names back into a readable list for each pizza.
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
