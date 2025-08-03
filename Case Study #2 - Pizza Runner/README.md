<h2>📋 What's Inside </h2>
<table><tr>
  <td style="vertical-align: top; padding-right: 30px;">
    <img src="https://8weeksqlchallenge.com/images/case-study-designs/1.png" width="150"/>
  </td>
  <td style="vertical-align: top;">
    <ul>
      <li><a href="#introduction-and-problem-statement">🔰 Introduction and Problem Statement</a></li>
      <li><a href="#dataset">🛢️ Dataset</a></li>
      <li><a href="#entity-relationship-diagram">🧩 Entity Relationship Diagram</a></li>
      <li><a href="#tech-stack-tools">🛠️ Tech Stack & Tools</a></li>
      <li><a href="#data-cleaning">🧼 Data Cleaning</a></li>
      <li><a href="#questions-and-solutions">📌Questions & Solutions</a></li>
      <li><a href="#final-notes">📝 Final Notes</a></li>
    </ul>
  </td>
</tr></table>


<h2 id="introduction-and-problem-statement">🔰 Introduction and Problem Statement</h2>

| Introduction |  Source|
|------------|---------------------------------|
|Did you know that over 115 million kilograms of pizza is consumed daily worldwide??? (Well according to Wikipedia anyway…). Danny was scrolling through his Instagram feed when something really caught his eye - “80s Retro Styling and Pizza Is The Future!” Danny was sold on the idea, but he knew that pizza alone was not going to help him get seed funding to expand his new Pizza Empire - so he had one more genius idea to combine with it - he was going to Uberize it - and so Pizza Runner was launched! Danny started by recruiting “runners” to deliver fresh pizza from Pizza Runner Headquarters (otherwise known as Danny’s house) and also maxed out his credit card to pay freelance developers to build a mobile app to accept orders from customers.| [Link](https://8weeksqlchallenge.com/case-study-2/) |

<h2 id="dataset">🛢️ Dataset</h2>
<p>All datasets exist within the <code>pizza_runner</code>  database schema - be sure to include this reference within your SQL scripts as you start exploring the data and answering the case study questions.</p>

<h3>🗃️ Table 1: runners </h3>
<p> The <code>runners</code> table shows the <code>registration_date</code> for each new runner. </p>

<details>
  <summary> <strong>Click to view runners table</strong></summary>
  <br/>
  <table style="border: 0; border-collapse: collapse;">
    <thead style="font-weight: bold;">
      <tr>
        <td>runner_id</td>
        <td>registration_date</td>
      </tr>
    </thead>
    <tbody>
      <tr><td>1</td><td>2021-01-01</td></tr>
      <tr><td>2</td><td>2021-01-03</td></tr>
      <tr><td>3</td><td>2021-01-08</td></tr>
      <tr><td>4</td><td>2021-01-15</td></tr>
    </tbody>
  </table>
</details>

<h3>🗃️ Table 2: customer_orders </h3>
<p> Customer pizza orders are captured in the <code>customer_orders</code> table with 1 row for each individual pizza that is part of the order.
The <code>pizza_id</code> relates to the type of pizza which was ordered whilst the <code>exclusions</code> are the <code>ingredient_id</code> values which should be removed from the pizza and the <code>extras</code> are the <code>ingredient_id</code> values which need to be added to the pizza.

Note that customers can order multiple pizzas in a single order with varying <code>exclusions</code> and <code>extras</code> values even if the pizza is the same type! <strong>The <code>exclusions</code> and <code>extras</code> columns will need to be cleaned up before using them in your queries</strong>.</p>

<details>
  <summary><strong>Click to view customer_orders table</strong></summary>
  <br/>
  <table style="border: 0; border-collapse: collapse;">
    <thead style="font-weight: bold;">
      <tr>
        <td>order_id</td>
        <td>customer_id</td>
        <td>pizza_id</td>
        <td>exclusions</td>
        <td>extras</td>
        <td>order_time</td>
      </tr>
    </thead>
    <tbody>
      <tr><td>1</td><td>101</td><td>1</td><td></td><td></td><td>2021-01-01 18:05:02</td></tr>
      <tr><td>2</td><td>101</td><td>1</td><td></td><td></td><td>2021-01-01 19:00:52</td></tr>
      <tr><td>3</td><td>102</td><td>1</td><td></td><td></td><td>2021-01-02 23:51:23</td></tr>
      <tr><td>3</td><td>102</td><td>2</td><td></td><td>NaN</td><td>2021-01-02 23:51:23</td></tr>
      <tr><td>4</td><td>103</td><td>1</td><td>4</td><td></td><td>2021-01-04 13:23:46</td></tr>
      <tr><td>4</td><td>103</td><td>1</td><td>4</td><td></td><td>2021-01-04 13:23:46</td></tr>
      <tr><td>4</td><td>103</td><td>2</td><td>4</td><td></td><td>2021-01-04 13:23:46</td></tr>
      <tr><td>5</td><td>104</td><td>1</td><td>null</td><td>1</td><td>2021-01-08 21:00:29</td></tr>
      <tr><td>6</td><td>101</td><td>2</td><td>null</td><td>null</td><td>2021-01-08 21:03:13</td></tr>
      <tr><td>7</td><td>105</td><td>2</td><td>null</td><td>1</td><td>2021-01-08 21:20:29</td></tr>
      <tr><td>8</td><td>102</td><td>1</td><td>null</td><td>null</td><td>2021-01-09 23:54:33</td></tr>
      <tr><td>9</td><td>103</td><td>1</td><td>4</td><td>1, 5</td><td>2021-01-10 11:22:59</td></tr>
      <tr><td>10</td><td>104</td><td>1</td><td>null</td><td>null</td><td>2021-01-11 18:34:49</td></tr>
      <tr><td>10</td><td>104</td><td>1</td><td>2, 6</td><td>1, 4</td><td>2021-01-11 18:34:49</td></tr>
    </tbody>
  </table>
</details>


<h3>🗃️ Table 3: runner_orders</h3>
<p> After each orders are received through the system - they are assigned to a runner - however not all orders are fully completed and can be cancelled by the restaurant or the customer.

The <code>pickup_time</code> is the timestamp at which the runner arrives at the Pizza Runner headquarters to pick up the freshly cooked pizzas. The <code>distance</code> and <code>duration</code> fields are related to how far and long the runner had to travel to deliver the order to the respective customer.

There are <strong>some known data issues with this table</strong> so be careful when using this in your queries - <strong>make sure to check the data types for each column</strong> in the schema SQL!</p>

<details>
  <summary><strong>Click to view runner_orders table</strong></summary>
  <br/>
  <table style="border: 0; border-collapse: collapse;">
    <thead style="font-weight: bold;">
      <tr>
        <td>order_id</td>
        <td>runner_id</td>
        <td>pickup_time</td>
        <td>distance</td>
        <td>duration</td>
        <td>cancellation</td>
      </tr>
    </thead>
    <tbody>
      <tr><td>1</td><td>1</td><td>2021-01-01 18:15:34</td><td>20km</td><td>32 minutes</td><td></td></tr>
      <tr><td>2</td><td>1</td><td>2021-01-01 19:10:54</td><td>20km</td><td>27 minutes</td><td></td></tr>
      <tr><td>3</td><td>1</td><td>2021-01-03 00:12:37</td><td>13.4km</td><td>20 mins</td><td>NaN</td></tr>
      <tr><td>4</td><td>2</td><td>2021-01-04 13:53:03</td><td>23.4</td><td>40</td><td>NaN</td></tr>
      <tr><td>5</td><td>3</td><td>2021-01-08 21:10:57</td><td>10</td><td>15</td><td>NaN</td></tr>
      <tr><td>6</td><td>3</td><td>null</td><td>null</td><td>null</td><td>Restaurant Cancellation</td></tr>
      <tr><td>7</td><td>2</td><td>2020-01-08 21:30:45</td><td>25km</td><td>25mins</td><td>null</td></tr>
      <tr><td>8</td><td>2</td><td>2020-01-10 00:15:02</td><td>23.4 km</td><td>15 minute</td><td>null</td></tr>
      <tr><td>9</td><td>2</td><td>null</td><td>null</td><td>null</td><td>Customer Cancellation</td></tr>
      <tr><td>10</td><td>1</td><td>2020-01-11 18:50:20</td><td>10km</td><td>10minutes</td><td>null</td></tr>
    </tbody>
  </table>
</details>

<h3>🗃️ Table 4: pizza_names</h3>
<p> At the moment - Pizza Runner only has 2 pizzas available the Meat Lovers or Vegetarian!</p>

<details>
  <summary><strong>Click to view pizza_names table</strong></summary>
  <br/>
  <table style="border: 0; border-collapse: collapse;">
    <thead style="font-weight: bold;">
      <tr>
        <td>pizza_id</td>
        <td>pizza_name</td>
      </tr>
    </thead>
    <tbody>
      <tr><td>1</td><td>Meat Lovers</td></tr>
      <tr><td>2</td><td>Vegetarian</td></tr>
    </tbody>
  </table>
</details>

<h3>🗃️ Table 5: pizza_recipes</h3>
<p> Each <code> pizza_id</code>  has a standard set of <code> toppings</code>  which are used as part of the pizza recipe.</p>

<details>
  <summary><strong>Click to view pizza_recipes table</strong></summary>
  <br/>
  <table style="border: 0; border-collapse: collapse;">
    <thead style="font-weight: bold;">
      <tr>
        <td>pizza_id</td>
        <td>toppings</td>
      </tr>
    </thead>
    <tbody>
      <tr><td>1</td><td>1, 2, 3, 4, 5, 6, 8, 10</td></tr>
      <tr><td>2</td><td>4, 6, 7, 9, 11, 12</td></tr>
    </tbody>
  </table>
</details>


<h3>🗃️ Table 6: pizza_toppings</h3>
<p>This table contains all of the <code>topping_name</code> values with their corresponding <code> topping_id</code>  value.</p>
<details>
  <summary><strong>Click to view toppings table</strong></summary>
  <br/>
  <table style="border: 0; border-collapse: collapse;">
    <thead style="font-weight: bold;">
      <tr>
        <td>topping_id</td>
        <td>topping_name</td>
      </tr>
    </thead>
    <tbody>
      <tr><td>1</td><td>Bacon</td></tr>
      <tr><td>2</td><td>BBQ Sauce</td></tr>
      <tr><td>3</td><td>Beef</td></tr>
      <tr><td>4</td><td>Cheese</td></tr>
      <tr><td>5</td><td>Chicken</td></tr>
      <tr><td>6</td><td>Mushrooms</td></tr>
      <tr><td>7</td><td>Onions</td></tr>
      <tr><td>8</td><td>Pepperoni</td></tr>
      <tr><td>9</td><td>Peppers</td></tr>
      <tr><td>10</td><td>Salami</td></tr>
      <tr><td>11</td><td>Tomatoes</td></tr>
      <tr><td>12</td><td>Tomato Sauce</td></tr>
    </tbody>
  </table>
</details>

<h2 id="entity-relationship-diagram">🧩 Entity Relationship Diagram</h2>
<img src="https://github.com/sajjad-hos/8-Week-SQL-Challenge/blob/main/Case%20Study%20%232%20-%20Pizza%20Runner/Pizza%20Runner.png">


<h2 id="tech-stack-tools">🛠️ Tech Stack & Tools</h2>
<p style="font-weight: normal;"> I completed the case study questions using <strong>PostgreSQL v17</strong> on <a href="https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/138" target="_blank" rel="noopener noreferrer">DB Fiddle</a>. 
  The queries are ready to run and explore as needed.</p>

<h2 id="data-cleaning">🧼 Data Cleaning</h2>
Some tables contained data quality issues, so I cleaned the data and stored it in temporary tables for reliable analysis.

### Table: `customer_orders`
The `exclusions` and `extras` columns in the customer_orders table have `NULL` values or the text `'null'`. I cleaned these up by replacing them with an `empty string ('')`.
The query below creates a temporary table called `customer_orders_cleaned`. It keeps all the original data but cleans up the `exclusions` and `extras` columns. The table is temporary and will be removed after the session ends.

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
Note: make sure to use the cleaned table (customer_orders_cleaned) with the solutions below. If you use the original table, the results might not be accurate.

### Table: `runner_orders`
There are some inconsistent or invalid entries such as `NULL` values or the `string 'null'` in the `pickup_time`, `distance`, `duration`, and `cancellation` columns.
To fix this, the query below first drops any existing `runner_orders_cleaned` table, then creates a temporary cleaned version of the `runner_orders` table. It addresses issues where columns like `pickup_time`, `distance`, `duration`, and `cancellation` contain `NULL` values or the string 'null', replacing them with `empty strings`. Additionally, it removes non-numeric characters from the `distance` and `duration` columns using `regexp_replace` to retain only the numeric values. After creating the cleaned table, it casts `pickup_time` to `TIMESTAMP`, `distance` to `FLOAT`, and `duration` to `INT`, ensuring that each column has the correct `data type` for further analysis.
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
Note: make sure to use the cleaned table (runner_orders_cleaned) with the solutions below. If you use the original table, the results might not be accurate.

<h2 id="questions-and-solutions">📌Questions & Solutions</h2>

#### A. Pizza Metrics

---
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

<h2 id="final-notes">📝 Final Notes</h2>
Thank you for reviewing my solution! If you spot any errors or have suggestions on how I can improve, please feel free to reach out to me on <a href="https://www.linkedin.com/in/sajjad-hos" target="_blank" rel="noopener noreferrer">LinkedIn</a>. I appreciate your feedback!
