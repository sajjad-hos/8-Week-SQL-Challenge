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
      <li><a href="#questions-and-solutions">📌Questions & Solutions</a></li>
      <li><a href="#final-notes">📝 Final Notes</a></li>
    </ul>
  </td>
</tr></table>


<h2 id="introduction-and-problem-statement">🔰 Introduction and Problem Statement</h2>

| Introduction | Problem Statement | Official Source|
|------------|--------------|-------------------|
| **Danny** opened a small Japanese restaurant in early 2021, serving his favorite foods:**sushi**, **curry**, and **ramen**. He’s collected some basic customer data but needs help analyzing it to make better business decisions.| Danny wants to understand his customers better by finding out **how often they visit**, **how much they spend**, and **which menu items they prefer**. He hopes these insights will help him create a more personalized experience and decide whether to expand his customer loyalty program. Additionally, he needs help generating easy-to-use data sets so his team can explore the data without needing to write SQL.| [Link](https://8weeksqlchallenge.com/case-study-1/) |

<h2 id="dataset">🛢️ Dataset</h2>
<p>Danny has shared three key datasets for this case study. These are given below:</p>

<h3>🗃️ Table 1: sales </h3>
<p> The <code>sales</code> table logs all customer purchase events, including the <code>customer_id</code>, <code>order_date</code>, and the <code>product_id</code> of each item ordered. </p>

<details>
  <summary> <strong>Click to view sales table</strong></summary>
  <br/>
  <table style="border: 0; border-collapse: collapse;">
    <thead style="font-weight: bold;">
      <tr>
        <td>customer_id</td>
        <td>order_date</td>
        <td>product_id</td>
      </tr>
    </thead>
    <tbody>
      <tr><td>A</td><td>2021-01-01</td><td>1</td></tr>
      <tr><td>A</td><td>2021-01-01</td><td>2</td></tr>
      <tr><td>A</td><td>2021-01-07</td><td>2</td></tr>
      <tr><td>A</td><td>2021-01-10</td><td>3</td></tr>
      <tr><td>A</td><td>2021-01-11</td><td>3</td></tr>
      <tr><td>A</td><td>2021-01-11</td><td>3</td></tr>
      <tr><td>B</td><td>2021-01-01</td><td>2</td></tr>
      <tr><td>B</td><td>2021-01-02</td><td>2</td></tr>
      <tr><td>B</td><td>2021-01-04</td><td>1</td></tr>
      <tr><td>B</td><td>2021-01-11</td><td>1</td></tr>
      <tr><td>B</td><td>2021-01-16</td><td>3</td></tr>
      <tr><td>B</td><td>2021-02-01</td><td>3</td></tr>
      <tr><td>C</td><td>2021-01-01</td><td>3</td></tr>
      <tr><td>C</td><td>2021-01-01</td><td>3</td></tr>
      <tr><td>C</td><td>2021-01-07</td><td>3</td></tr>
    </tbody>
  </table>
</details>

<h3>🗃️ Table 2: menu </h3>
<p> The <code>menu</code> table maps each <code>product_id</code> to its associated <code>product_name</code> and <code>price</code>.</p>

<details>
  <summary> <strong>Click to view menu table</strong></summary>
  <br/>
  <table style="border: 0; border-collapse: collapse;">
    <thead style="font-weight: bold;">
      <tr>
        <td>product_id</td>
        <td>product_name</td>
        <td>price</td>
      </tr>
    </thead>
    <tbody>
      <tr><td>1</td><td>sushi</td><td>10</td></tr>
      <tr><td>2</td><td>curry</td><td>15</td></tr>
      <tr><td>3</td><td>ramen</td><td>12</td></tr>
    </tbody>
  </table>
</details>

<h3>🗃️ Table 3: members</h3>
<p> The <code>members</code> table captures the <code>join_date</code> when a <code>customer_id</code> joined Danny’s Diner loyalty program.</p>

<details>
  <summary><strong>Click to view members table</strong></summary>
  <br/>
  <table style="border: 0; border-collapse: collapse;">
    <thead style="font-weight: bold;">
      <tr>
        <td>customer_id</td>
        <td>join_date</td>
      </tr>
    </thead>
    <tbody>
      <tr><td>A</td><td>2021-01-07</td></tr>
      <tr><td>B</td><td>2021-01-09</td></tr>
    </tbody>
  </table>
</details>


<h2 id="entity-relationship-diagram">🧩 Entity Relationship Diagram</h2>
<img src="https://github.com/sajjad-hos/8-Week-SQL-Challenge/blob/main/Case%20Study%20%231%20-%20Danny's%20Diner/Danny's%20Diner.png">


<h2 id="tech-stack-tools">🛠️ Tech Stack & Tools</h2>
<p style="font-weight: normal;"> I completed the case study questions using <strong>PostgreSQL v17</strong> on <a href="https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/138" target="_blank" rel="noopener noreferrer">DB Fiddle</a>. 
  The queries are ready to run and explore as needed.</p>

<h2 id="questions-and-solutions">📌Questions & Solutions</h2>

#### 1. What is the total amount each customer spent at the restaurant?
#### 🧠 My Solution:

````sql
SELECT s.customer_id AS customer,
       CONCAT('$', SUM(m.price)) AS total_spent
FROM   sales s
INNER JOIN   menu m  ON s.product_id = m.product_id
GROUP BY s.customer_id
ORDER BY s.customer_id;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>
I combined data from the <code>sales</code> and <code>menu</code> tables using <code>product_id</code>. Then, I followed these simple steps to get the results:
  <ul>
    <li> After joining the <code>sales</code> and <code>menu</code> tables using <code>product_id</code> to match purchases with their prices, and sum up the prices for each customer to get their total spend.</li>
    <li> Group everything by customer so we can add up their orders.h</li>
    <li> Show the total with a dollar sign to make it clear and sort the list by <code>customer_id</code> so it’s easy to follow.</li>
  </ul> </details>

#### 📊 Query Result:
| customer | total_spent |
| -------- | ----------- |
| A        | $76         |
| B        | $74         |
| C        | $36         |

####  ⚡Insights:
- Customer A leads the spend list with a total of $76.
- Customer B is the second with $74, just $2 less than Customer A.
- Customer C has spent $36, which is roughly half of what Customers A and B have spent.

---


#### 2. How many days has each customer visited the restaurant?
#### 🧠 My Solution:

````sql
SELECT
      customer_id AS customer,
      COUNT(DISTINCT order_date) AS total_visit_days
FROM  sales
GROUP BY  customer_id
ORDER BY  customer_id;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>
The <code>sales</code> table has all the data I need to solve this problem. I followed these simple steps to get the results:
  <ul>
    <li>Count the number of unique days they placed orders (using COUNT(DISTINCT..) function and <code>order_date</code>) column.</li>
    <li>Group the results by customer to get their total visit days.</li>
    <li>Sort the list by <code>customer_id</code> so it’s easy to read.</li>
  </ul> </details>

#### 📊 Query Result:
| customer | total_visit_days |
| -------- | ---------------- |
| A        | 4                |
| B        | 6                |
| C        | 2                |

#### ⚡Insights:
- Customer B is the star guest with 6 visits, a true fan of the restaurant!
- Customer A is also a regular, visits 4 times, which is wonderful to see.
- Customer C has visited only twice! So some extra care could help encourage more visits.

---

#### 3. What was the first item from the menu purchased by each customer?
#### 🧠 My Solution:

````sql
SELECT DISTINCT ON (s.customer_id)
      s.customer_id AS customer,
      m.product_name AS first_purchased_item,
      s.order_date        
FROM  sales s
INNER JOIN  menu m ON s.product_id = m.product_id
ORDER BY s.customer_id, s.order_date;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>
I combined data from the <code>sales</code> and <code>menu</code> tables using <code>product_id</code> to link purchases with <code>product_name</code>. Then, I followed these simple steps to get the results:
  <ul>
    <li>Used <code>INNER JOIN</code> to connect the two tables using <code>product_id</code>, so I could match each purchase with its product name.</li>
    <li>Used <code>DISTINCT ON (customer_id)</code> to pick just the first purchase for each customer.</li>
    <li>Ordered the data by <code>customer_id</code> and <code>order_date</code> to make sure the earliest purchase appears first.</li>
    <li>Selected <code>customer_id</code>, <code>product_name</code>, and <code>order_date</code> to show who bought what and when.</li>
  </ul> </details>

#### 📊 Query Result:
| customer | first_purchased_item | order_date |
| -------- | -------------------- | ---------- |
| A        | curry                | 2021-01-01 |
| B        | curry                | 2021-01-01 |
| C        | ramen                | 2021-01-01 |

#### ⚡Insights:
- Customers A and B kicked off with curry, showing their love for it right away.
- Customer C started with ramen, picking a different favorite from the star.

---

#### 4. What is the most purchased item on the menu and how many times was it purchased by all customers?
#### 🧠 My Solution:

````sql
SELECT
      m.product_name,
      COUNT(*) AS purchase_count
FROM sales s
INNER JOIN menu m ON s.product_id = m.product_id
GROUP BY m.product_name
ORDER BY purchase_count DESC
LIMIT 1;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>
To figure out which menu item was the most popular overall, I used data from both the <code>sales</code> and <code>menu</code> tables. Here'r the steps I followed:
  <ul>
    <li>Joined the two tables using <code>product_id</code> so I could match each sale with its product name.</li>
    <li>Counted how many times each item was ordered across all customers using <code>COUNT(*)</code>.</li>
    <li>Grouped the results by <code>product_name</code> to get totals for each dish.</li>
    <li>Sorted the list in descending order to put the most ordered item at the top.</li>
    <li>Used <code>LIMIT 1</code> to show just the most purchased item and its count.</li>
  </ul> </details>

#### 📊 Query Result:
| product_name | purchase_count |
| ------------ | -------------- |
| ramen        | 8              |

#### ⚡Insights:
- Ramen is the most popular dish, with customers ordering it 8 times. Looks like everyone loves it!
  
---

#### 5. Which item was the most popular for each customer?
#### 🧠 My Solution:

````sql
WITH item_counts AS (
    SELECT
        s.customer_id,
        m.product_name,
        COUNT(*) AS purchase_count,
        RANK() OVER (PARTITION BY s.customer_id ORDER BY COUNT(*) DESC) AS rank
    FROM sales s
    INNER JOIN menu m ON s.product_id = m.product_id
    GROUP BY s.customer_id, m.product_name
)
SELECT
    customer_id AS customer,
    product_name,
    purchase_count
FROM item_counts
WHERE rank = 1
ORDER BY customer_id, product_name;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>
To find each customer’s favorite item, I worked with the <code>sales</code> and <code>menu</code> tables. Here’s what I did:
  <ul>
    <li>Joined the two tables using <code>product_id</code> to match purchases with product names.</li>
    <li>Used CTE to first count how many times each customer ordered each item.</li>
    <li>Within the CTE, applied the <code>RANK()</code> function to rank items for each customer. The most purchased items got rank 1.</li>
    <li>Picked only the top-ranked items for each customer (their most ordered dish).</li>
    <li>Sorted the final result by customer and item name to keep things neat.</li>
  </ul></details>

#### 📊 Query Result:
| customer | product_name | purchase_count |
| -------- | ------------ | -------------- |
| A        | ramen        | 3              |
| B        | curry        | 2              |
| B        | ramen        | 2              |
| B        | sushi        | 2              |
| C        | ramen        | 3              |

#### ⚡Insights:
- Customers A and C love ramen, each ordered it 3 times. It’s their favorite dish!
- Customer B enjoys a mix of flavors and has ordered curry, ramen, and sushi 2 times each.
  
---

#### 6. Which item was purchased first by the customer after they became a member?
#### 🧠 My Solution:

````sql
SELECT DISTINCT ON (s.customer_id)
      s.customer_id AS customer,
      m.product_name,
      s.order_date
FROM sales s
INNER JOIN members memb ON s.customer_id = memb.customer_id
INNER JOIN menu m ON s.product_id = m.product_id
WHERE s.order_date >= memb.join_date
ORDER BY s.customer_id, s.order_date;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>
To find each customer’s first purchased item after they become a member, I need <strong>sales</strong>, <strong>members</strong>, and <strong>menu</strong> tables by following these steps:
<ul>
  <li>Joined the <strong>sales</strong> table with the <strong>members</strong> table using <code>customer_id</code> to know when each customer joined.</li>
  <li>Joined the <strong>menu</strong> table to get the <code>product_name</code> for each purchase.</li>
  <li>Filtered purchases to only include those made on or after the customer’s membership start date.</li>
  <li>Used <code>DISTINCT ON (customer_id)</code> with ordering by <code>order_date</code> to select the earliest purchase after joining.</li>
</ul> </details>

#### 📊 Query Result:
| customer | product_name | order_date |
| -------- | ------------ | ---------- |
| A        | curry        | 2021-01-07 |
| B        | sushi        | 2021-01-11 |

#### ⚡Insights:
- After becoming a member, Customer A’s first purchase was curry, and Customer B’s was sushi.
  
---

#### 7. Which item was purchased just before the customer became a member?
#### 🧠 My Solution:

````sql
WITH last_before_membership AS (
        SELECT
            s.customer_id,
            MAX(s.order_date) AS last_order_date
        FROM sales s
        INNER JOIN members memb ON s.customer_id = memb.customer_id
        WHERE s.order_date < memb.join_date
        GROUP BY s.customer_id
)

SELECT
      s.customer_id AS customer,
      m.product_name,
      s.order_date
FROM sales s
INNER JOIN members memb ON s.customer_id = memb.customer_id
INNER JOIN menu m ON s.product_id = m.product_id
INNER JOIN last_before_membership lbm ON s.customer_id = lbm.customer_id AND s.order_date = lbm.last_order_date
WHERE s.order_date < memb.join_date
ORDER BY s.customer_id, m.product_name;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>
To find each customer’s last purchased item before they become a member, I need <strong>sales</strong>, <strong>members</strong>, and <strong>menu</strong> tables by following these steps:</p>
  <ul>
    <li>Created a CTE to get each customer’s last order date before their membership start.</li>
    <li>Joined <code>sales</code>, <code>members</code>, and <code>menu</code> tables to get full purchase details.</li>
    <li>Joined the CTE on <code>customer_id</code> and <code>order_date</code> to match the last pre-member purchase.</li>
    <li>Filtered again to ensure only purchases before the join date are included.</li>
    <li>Sorted the final results by customer and product name.</li>
  </ul> </details>

#### 📊 Query Result:
| customer | product_name | order_date |
| -------- | ------------ | ---------- |
| A        | curry        | 2021-01-01 |
| A        | sushi        | 2021-01-01 |
| B        | sushi        | 2021-01-04 |

#### ⚡Insights:
- Customer A’s last purchases before joining were curry and sushi.
- Customer B last bought sushi before becoming a member.

---

#### 8. What is the total items and amount spent for each member before they became a member?
#### 🧠 My Solution:

````sql
SELECT
    s.customer_id AS customer,
    COUNT(*) AS total_items_before_membership,
    SUM(m.price) AS total_amount_before_membership
FROM sales s
INNER JOIN members memb ON s.customer_id = memb.customer_id
INNER JOIN menu m ON s.product_id = m.product_id
WHERE s.order_date < memb.join_date
GROUP BY s.customer_id
ORDER BY s.customer_id;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>
To solve this, I need <strong>sales</strong>, <strong>members</strong>, and <strong>menu</strong> tables by following these steps:
  <ul>
    <li>Joined <code>sales</code> with <code>members</code> and <code>menu</code> to get each customer's membership join date and price of each purchased item.</li>
    <li>Counted the total items bought before becoming a member.</li>
    <li>Summed the prices to get the total amount spent before membership.</li>
    <li>Filtered only the purchases made before the membership join date.</li>
    <li>Grouped the results by customer to show individual totals.</li>
  </ul> </details>

#### 📊 Query Result:
| customer | total_items_before_membership | total_amount_before_membership |
| -------- | ----------------------------- | ------------------------------ |
| A        | 2                             | 25                             |
| B        | 3                             | 40                             |

#### ⚡Insights:
- Customer A bought 2 items and spent a total of $25 before joining the loyalty program.</li>
- Customer B made 3 purchases totaling $40 before becoming a member.
---

#### 9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
#### 🧠 My Solution:

````sql
SELECT
      s.customer_id AS customer,
      SUM(CASE WHEN m.product_name = 'sushi' THEN m.price * 10 * 2 ELSE m.price * 10 END) AS total_points
FROM sales s
JOIN menu m ON s.product_id = m.product_id
GROUP BY s.customer_id
ORDER BY s.customer_id;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>
To solve this, I need <strong>sales</strong> and <strong>menu</strong> tables and following steps:
  <ul>
    <li>Joined <code>sales</code> with <code>menu</code> to access item names and prices.</li>
    <li>Used a <code>CASE</code> statement to calculate points: price × 10 for all items.</li>
    <li>Applied a 2x multiplier for <code>sushi</code>, giving price × 10 × 2.</li>
    <li>Summed the total points earned by each customer.</li>
    <li>Grouped by customer and sorted for a clear result.</li>
  </ul> </details>

#### 📊 Query Result:
| customer | total_points |
| -------- | ------------ |
| A        | 860          |
| B        | 940          |
| C        | 360          |

#### ⚡Insights:
- Customer B is shining bright with 940 points! Thanks for the regular visits and enjoying sushi.
- Customer A is close behind with 860 points, clearly loving their sushi moments.
- Customer C has 360 points, enjoying the menu a bit more quietly with fewer sushi bites.

---

#### 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
#### 🧠 My Solution:

````sql
SELECT
    s.customer_id AS customer,
    SUM(CASE
          WHEN s.order_date BETWEEN memb.join_date AND memb.join_date + INTERVAL '6 days' THEN m.price * 10 * 2
          WHEN m.product_name = 'sushi' THEN m.price * 10 * 2
          ELSE m.price * 10
        END) AS total_points
FROM sales s
INNER JOIN members memb ON s.customer_id = memb.customer_id
INNER JOIN menu m ON s.product_id = m.product_id
WHERE s.customer_id IN ('A', 'B') AND s.order_date <= '2023-01-31'
GROUP BY s.customer_id
ORDER BY s.customer_id;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>
To solve this, I need <strong>sales</strong>, <strong>members</strong> and <strong>menu</strong> tables and following steps:
  <ul>
    <li>Joined <code>sales</code>, <code>members</code>, and <code>menu</code> tables to access customer join dates and item prices.</li>
    <li>Used a <code>CASE</code> statement to apply different point rules.</li>
    <li>Gave 2x points on all items if the purchase was made within 7 days (including join date) of membership.</li>
    <li>Applied 2x points only for <code>sushi</code> if purchased after the first week.</li>
    <li>Applied normal points (price × 10) for all other cases.</li>
    <li>Filtered data for customers A and B and orders made before or on January 31, 2023.</li>
    <li>Grouped the results by customer and summed total points.</li>
  </ul> </details>

#### 📊 Result:
| customer | total_points |
| -------- | ------------ |
| A        | 1370         |
| B        | 940          |

#### ⚡Insights:
- Customer A earned 1,370 points by getting double points on everything during their first week as a member.
- Customer B earned 940 points, also enjoying double points on all their purchases in their first week.
  
---

#### ➕ Bonus Question:

---
#### 1. Join All The Things: Danny and his team want to create a basic summary table that combines key information from multiple sources, so they can quickly derive insights without writing complex SQL joins. The task is now, recreate the following table that includes customer_id, order_date, product_name, price and membership status by using the available data:
| customer_id | order_date | product_name | price | member |
| ----------- | ---------- | ------------ | ----- | ------ |
| A           | 2021-01-01 | curry        | 15    | N      |
| A           | 2021-01-01 | sushi        | 10    | N      |
| A           | 2021-01-07 | curry        | 15    | Y      |
| A           | 2021-01-10 | ramen        | 12    | Y      |
| A           | 2021-01-11 | ramen        | 12    | Y      |
| A           | 2021-01-11 | ramen        | 12    | Y      |
| B           | 2021-01-01 | curry        | 15    | N      |
| B           | 2021-01-02 | curry        | 15    | N      |
| B           | 2021-01-04 | sushi        | 10    | N      |
| B           | 2021-01-11 | sushi        | 10    | Y      |
| B           | 2021-01-16 | ramen        | 12    | Y      |
| B           | 2021-02-01 | ramen        | 12    | Y      |
| C           | 2021-01-01 | ramen        | 12    | N      |
| C           | 2021-01-01 | ramen        | 12    | N      |
| C           | 2021-01-07 | ramen        | 12    | N      |

#### 🧠 My Solution:

````sql
SELECT 
    s.customer_id,
    s.order_date,
    m.product_name,
    m.price,
    CASE WHEN s.order_date >= memb.join_date THEN 'Y' ELSE 'N' END AS member
FROM sales s
LEFT JOIN members memb ON s.customer_id = memb.customer_id
JOIN menu m ON s.product_id = m.product_id
ORDER BY s.customer_id, s.order_date, m.product_name;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>
To solve this, I need <strong>sales</strong>, <strong>members</strong> and <strong>menu</strong> tables and following steps:
  <ul>
    <li>Start with the sales table to get all purchases.</li>
    <li>Use a LEFT JOIN to include membership info, so customers without membership still show.</li>
    <li>Join with the menu to get product names and prices.</li>
    <li>Add a column to mark if the purchase was made after the customer joined the membership.</li>
    <li>Order the results by customer, date, and product for easy reading.</li>
  </ul> </details>

#### 📊 Query Result:
| customer_id | order_date | product_name | price | member |
| ----------- | ---------- | ------------ | ----- | ------ |
| A           | 2021-01-01 | curry        | 15    | N      |
| A           | 2021-01-01 | sushi        | 10    | N      |
| A           | 2021-01-07 | curry        | 15    | Y      |
| A           | 2021-01-10 | ramen        | 12    | Y      |
| A           | 2021-01-11 | ramen        | 12    | Y      |
| A           | 2021-01-11 | ramen        | 12    | Y      |
| B           | 2021-01-01 | curry        | 15    | N      |
| B           | 2021-01-02 | curry        | 15    | N      |
| B           | 2021-01-04 | sushi        | 10    | N      |
| B           | 2021-01-11 | sushi        | 10    | Y      |
| B           | 2021-01-16 | ramen        | 12    | Y      |
| B           | 2021-02-01 | ramen        | 12    | Y      |
| C           | 2021-01-01 | ramen        | 12    | N      |
| C           | 2021-01-01 | ramen        | 12    | N      |
| C           | 2021-01-07 | ramen        | 12    | N      |

#### ⚡Insights:
- The output table indicates whether the purchase was made before or after they joined the membership program.
- Customer A shopped as a non-member until joining the loyalty program on 2021-01-07.
- Similarly, Customer B has purchases both before and after their membership start date. 
- Customer C hasn’t joined the membership program yet, so all their purchases are marked as non-member.

---

#### 2. Rank All The Things: create a query or table that shows, for each customer’s product purchase, a ranking of their products only if they were members at the time of purchase. For non-member purchases, the ranking value should be null.
#### 🧠 My Solution:

````sql
WITH customers_data AS (
  SELECT 
    s.customer_id, 
    s.order_date,  
    m.product_name, 
    m.price,
    CASE WHEN members.join_date IS NOT NULL AND members.join_date <= s.order_date THEN 'Y' ELSE 'N' END AS member_status
  FROM dannys_diner.sales s
  LEFT JOIN dannys_diner.members members ON s.customer_id = members.customer_id
  INNER JOIN dannys_diner.menu m ON s.product_id = m.product_id
)

SELECT *,
  CASE WHEN member_status = 'Y' THEN RANK() OVER (PARTITION BY customer_id, member_status ORDER BY order_date)
       ELSE NULL
  END AS ranking
FROM customers_data;
  ````
<details> <summary><strong>Solution Approach:</strong></summary>
To solve this, I need <strong>sales</strong>, <strong>members</strong> and <strong>menu</strong> tables and following steps:
  <ul>
    <li>Start by gathering all sales data along with membership info and menu details using JOINs.</li>
    <li>Create a new column to mark whether the customer was a member at the time of each purchase.</li>
    <li>Use a window function (RANK) to assign a purchase rank for each member’s orders based on order date.</li>
    <li>Only rank purchases made after the customer became a member; others get no rank.</li>
    <li>Return all columns including the calculated rank for easy analysis of purchase order.</li>
  </ul> </details>

#### 📊 Query Result:
| customer_id | order_date | product_name | price | member_status | ranking |
| ----------- | ---------- | ------------ | ----- | ------------- | ------- |
| A           | 2021-01-01 | sushi        | 10    | N             |         |
| A           | 2021-01-01 | curry        | 15    | N             |         |
| A           | 2021-01-07 | curry        | 15    | Y             | 1       |
| A           | 2021-01-10 | ramen        | 12    | Y             | 2       |
| A           | 2021-01-11 | ramen        | 12    | Y             | 3       |
| A           | 2021-01-11 | ramen        | 12    | Y             | 3       |
| B           | 2021-01-01 | curry        | 15    | N             |         |
| B           | 2021-01-02 | curry        | 15    | N             |         |
| B           | 2021-01-04 | sushi        | 10    | N             |         |
| B           | 2021-01-11 | sushi        | 10    | Y             | 1       |
| B           | 2021-01-16 | ramen        | 12    | Y             | 2       |
| B           | 2021-02-01 | ramen        | 12    | Y             | 3       |
| C           | 2021-01-01 | ramen        | 12    | N             |         |
| C           | 2021-01-01 | ramen        | 12    | N             |         |
| C           | 2021-01-07 | ramen        | 12    | N             |         |

#### ⚡Insights:
- This detailed report ranks each customer’s purchases after they joined the membership program.
- Customer A, the ranking shows their first purchase as a member was on January 7th, 10th and 11th.
- Customer B also shows a clear progression of member purchases starting January 11th. 
- Customer C hasn’t joined the membership program yet, so none of their purchases are ranked.
- Purchases made before membership are still visible but unranked, helping us see the transition into member behavior.

  ---

<h2 id="final-notes">📝 Final Notes</h2>
Thank you for reviewing my solution! If you spot any errors or have suggestions on how I can improve, please feel free to reach out to me on <a href="https://www.linkedin.com/in/sajjad-hos" target="_blank" rel="noopener noreferrer">LinkedIn</a>. I appreciate your feedback!
