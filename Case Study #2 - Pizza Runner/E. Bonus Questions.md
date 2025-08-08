<h2 id="case-study-2-pizza-runner">🍕 Case Study #2: Pizza Runner – E. Bonus Questions</h2>

This section covers Part `E. Bonus Questions` from `Case Study #2: Pizza Runner`, including the question’s approach, solution & result.

<h2 id="questions-and-solutions">📌Questions & Solutions</h2>

#### If Danny wants to expand his range of pizzas - how would this impact the existing data design? Write an INSERT statement to demonstrate what would happen if a new Supreme pizza with all the toppings was added to the Pizza Runner menu?
#### 🧠 My Approach & Solution:
- New pizza (Supreme) added to pizza_names table
- Toppings added for Supreme in pizza_recipes
- Finally showed the pizza and respective toppings

````sql
INSERT INTO pizza_names (pizza_id, pizza_name)        
VALUES (3, 'Supreme');
    
INSERT INTO pizza_recipes (pizza_id, toppings)        
VALUES (3, '1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12');

-- Show result
SELECT pn.pizza_id, pn.pizza_name, pr.toppings
FROM pizza_names pn
INNER JOIN pizza_recipes pr ON pn.pizza_id = pr.pizza_id
````

#### 📊 Query Result:
| pizza_id | pizza_name | toppings                              |
| -------- | ---------- | ------------------------------------- |
| 1        | Meatlovers | 1, 2, 3, 4, 5, 6, 8, 10               |
| 2        | Vegetarian | 4, 6, 7, 9, 11, 12                    |
| 3        | Supreme    | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12 |

- Danny's existing data model won't be affected as the design is already scalable. That means the pizza_names table can store any number of pizzas, and pizza_recipes can link them to any toppings.
---
