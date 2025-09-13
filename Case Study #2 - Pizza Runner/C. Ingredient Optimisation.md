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

#### 3. What was the most common exclusion?
#### 🧠 My Approach & Solution:

````sql
SELECT
    p.topping_name,
    COUNT(*) AS frequency
FROM pizza_runner.customer_orders co,
    REGEXP_SPLIT_TO_TABLE(co.exclusions, ',\s*') AS excl(new_exclusion)
    INNER JOIN pizza_runner.pizza_toppings p ON excl.new_exclusion::INTEGER = p.topping_id
WHERE excl.new_exclusion NOT IN ('', 'null')
GROUP BY p.topping_name
ORDER BY frequency DESC;
  ````

#### 📊 Query Result & Insights:
| topping_name | frequency |
| ------------ | --------- |
| Cheese       | 4         |
| Mushrooms    | 1         |
| BBQ Sauce    | 1         |

- The most common exclusion was Cheese.
---

#### 4. Generate an order item for each record in the customers_orders table in the format of one of the following:
- Meat Lovers
- Meat Lovers - Exclude Beef
- Meat Lovers - Extra Bacon
- Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers

#### 🧠 My Approach & Solution:
````sql
SELECT
    co.order_id,
    co.customer_id,
    CONCAT_WS(' - ',
        p.pizza_name,
        CASE WHEN excl_list IS NOT NULL THEN 'Exclude ' || excl_list END,
        CASE WHEN extra_list IS NOT NULL THEN 'Extra ' || extra_list END
    ) AS ordered_item
FROM pizza_runner.customer_orders co
JOIN pizza_runner.pizza_names p ON co.pizza_id = p.pizza_id
LEFT JOIN LATERAL (
    SELECT STRING_AGG(pt.topping_name, ', ') AS excl_list
    FROM REGEXP_SPLIT_TO_TABLE(co.exclusions, ',\s*') AS excl(topping_id)
    JOIN pizza_runner.pizza_toppings pt ON excl.topping_id::INTEGER = pt.topping_id
    WHERE excl.topping_id NOT IN ('', 'null')
) excl ON TRUE
LEFT JOIN LATERAL (
    SELECT STRING_AGG(pt.topping_name, ', ') AS extra_list
    FROM REGEXP_SPLIT_TO_TABLE(co.extras, ',\s*') AS extra(topping_id)
    JOIN pizza_runner.pizza_toppings pt ON extra.topping_id::INTEGER = pt.topping_id
    WHERE extra.topping_id NOT IN ('', 'null')
) extra ON TRUE
ORDER BY co.order_id;
  ````

#### 📊 Query Result & Insights:
| order_id | customer_id | ordered_item                                                    |
| -------- | ----------- | --------------------------------------------------------------- |
| 1        | 101         | Meatlovers                                                      |
| 2        | 101         | Meatlovers                                                      |
| 3        | 102         | Meatlovers                                                      |
| 3        | 102         | Vegetarian                                                      |
| 4        | 103         | Vegetarian - Exclude Cheese                                     |
| 4        | 103         | Meatlovers - Exclude Cheese                                     |
| 4        | 103         | Meatlovers - Exclude Cheese                                     |
| 5        | 104         | Meatlovers - Extra Bacon                                        |
| 6        | 101         | Vegetarian                                                      |
| 7        | 105         | Vegetarian - Extra Bacon                                        |
| 8        | 102         | Meatlovers                                                      |
| 9        | 103         | Meatlovers - Exclude Cheese - Extra Bacon, Chicken              |
| 10       | 104         | Meatlovers - Exclude BBQ Sauce, Mushrooms - Extra Bacon, Cheese |
| 10       | 104         | Meatlovers                                                      |

---
#### 5. Generate an alphabetically ordered comma separated ingredient list for each pizza order from the customer_orders table and add a 2x in front of any relevant ingredients
- For example: "Meat Lovers: 2xBacon, Beef, ... , Salami"

#### 🧠 My Approach & Solution:

````sql
WITH cleaned_orders AS (
    SELECT order_id, customer_id, pizza_id, order_time,
        NULLIF(NULLIF(extras, ''), 'null') AS extras,
        NULLIF(NULLIF(exclusions, ''), 'null') AS exclusions
    FROM pizza_runner.customer_orders
),
expanded_toppings AS (
    SELECT
        o.order_id, o.customer_id, o.pizza_id, o.order_time, pt.topping_id, pt.topping_name,
        -- mark as 2x if in extras
        CASE 
            WHEN o.extras IS NOT NULL AND pt.topping_id = ANY(string_to_array(o.extras, ',')::INT[]) 
            THEN '2x ' || pt.topping_name ELSE pt.topping_name END AS ingredient,
        CASE WHEN o.exclusions IS NOT NULL THEN string_to_array(o.exclusions, ',')::INT[] ELSE '{}'::INT[] END AS exclusions_array
    FROM cleaned_orders o
    CROSS JOIN LATERAL unnest(string_to_array((SELECT toppings FROM pizza_runner.pizza_recipes WHERE pizza_id = o.pizza_id), ',')::INT[]) AS t(topping_id)
    JOIN pizza_runner.pizza_toppings pt USING(topping_id)
)
SELECT
    order_id, STRING_AGG(ingredient, ', ' ORDER BY topping_name) AS ingredients_list
FROM expanded_toppings
WHERE NOT (topping_id = ANY(exclusions_array))
GROUP BY order_id, customer_id, pizza_id, order_time
ORDER BY order_id;
  ````

#### 📊 Query Result & Insights:
| order_id | ingredients_list                                                                                                             |
| -------- | ---------------------------------------------------------------------------------------------------------------------------- |
| 1        | BBQ Sauce, Bacon, Beef, Cheese, Chicken, Mushrooms, Pepperoni, Salami                                                        |
| 2        | BBQ Sauce, Bacon, Beef, Cheese, Chicken, Mushrooms, Pepperoni, Salami                                                        |
| 3        | BBQ Sauce, Bacon, Beef, Cheese, Chicken, Mushrooms, Pepperoni, Salami                                                        |
| 3        | Cheese, Mushrooms, Onions, Peppers, Tomato Sauce, Tomatoes                                                                   |
| 4        | BBQ Sauce, BBQ Sauce, Bacon, Bacon, Beef, Beef, Chicken, Chicken, Mushrooms, Mushrooms, Pepperoni, Pepperoni, Salami, Salami |
| 4        | Mushrooms, Onions, Peppers, Tomato Sauce, Tomatoes                                                                           |
| 5        | BBQ Sauce, 2x Bacon, Beef, Cheese, Chicken, Mushrooms, Pepperoni, Salami                                                     |
| 6        | Cheese, Mushrooms, Onions, Peppers, Tomato Sauce, Tomatoes                                                                   |
| 7        | Cheese, Mushrooms, Onions, Peppers, Tomato Sauce, Tomatoes                                                                   |
| 8        | BBQ Sauce, Bacon, Beef, Cheese, Chicken, Mushrooms, Pepperoni, Salami                                                        |
| 9        | BBQ Sauce, 2x Bacon, Beef, 2x Chicken, Mushrooms, Pepperoni, Salami                                                          |
| 10       | BBQ Sauce, 2x Bacon, Bacon, Beef, Beef, Cheese, 2x Cheese, Chicken, Chicken, Mushrooms, Pepperoni, Pepperoni, Salami, Salami |

---
#### 6. What is the total quantity of each ingredient used in all delivered pizzas sorted by most frequent first?

#### 🧠 My Approach & Solution:

````sql
WITH ingredient_cte AS (
    SELECT 
        c.order_id,
        pz.pizza_name,
        t.topping_name,
        CASE WHEN t.topping_id = ANY(string_to_array(NULLIF(c.extras, 'null'), ',')::INT[]) THEN 2 ELSE 1 END AS times_used_topping
    FROM customer_orders c
    INNER JOIN pizza_names pz ON c.pizza_id = pz.pizza_id
    INNER JOIN pizza_recipes rcp ON c.pizza_id = rcp.pizza_id
    CROSS JOIN LATERAL unnest(string_to_array(rcp.toppings, ',')::INT[]) AS t_id(topping_id)
    INNER JOIN pizza_toppings t ON t.topping_id = t_id.topping_id
    INNER JOIN runner_orders r ON c.order_id = r.order_id
    WHERE r.cancellation IS NULL
         AND (c.exclusions IS NULL OR t.topping_id <> ALL(string_to_array(NULLIF(c.exclusions, 'null'), ',')::INT[]))
)
SELECT
      topping_name,
      SUM(times_used_topping) AS total_quantity
FROM ingredient_cte
GROUP BY topping_name
ORDER BY total_quantity DESC;
  ````

#### 📊 Query Result & Insights:
| topping_name | total_quantity |
| ------------ | -------------- |
| Mushrooms    | 5              |
| Beef         | 3              |
| Chicken      | 3              |
| Pepperoni    | 3              |
| Bacon        | 3              |
| BBQ Sauce    | 3              |
| Salami       | 3              |
| Cheese       | 2              |
| Onions       | 2              |
| Tomato Sauce | 2              |
| Peppers      | 2              |
| Tomatoes     | 2              |

---
