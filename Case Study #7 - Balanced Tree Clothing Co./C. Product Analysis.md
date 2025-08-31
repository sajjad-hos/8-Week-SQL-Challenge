## Case Study #7 - Balanced Tree Clothing Co. | C. Product Analysis | Questions | Solutions | Results

#### Q1: What are the top 3 products by total revenue before discount?
#### 🧠 My Approach & Solution:

````sql
WITH product_revenue AS (
    SELECT 
        s.prod_id,
        pd.product_name,
        SUM(s.price * s.qty) AS total_revenue
    FROM balanced_tree.sales s
    INNER JOIN balanced_tree.product_details pd ON s.prod_id = pd.product_id
    GROUP BY s.prod_id, pd.product_name
)
SELECT 
    prod_id,
    product_name,
    total_revenue
FROM product_revenue
ORDER BY total_revenue DESC
LIMIT 3;
  ````

#### 📊 Query Result & Insights:
| prod_id | product_name                 | total_revenue |
| ------- | ---------------------------- | ------------- |
| 2a2353  | Blue Polo Shirt - Mens       | 217683        |
| 9ec847  | Grey Fashion Jacket - Womens | 209304        |
| 5d267b  | White Tee Shirt - Mens       | 152000        |

- The top 3 products are Blue Polo Shirt Men's, Grey Fashion Jacket Women's, and White Tee Shirt Men's.
---
#### Q2: What is the total quantity, revenue, and discount for each segment?  
#### 🧠 My Approach & Solution:
- Joined sales with product_details to get the segment for each product, and then summed up the quantity sold, total revenue, and discount. Finally, grouped by segment_id and segment_name to get one row per segment.

````sql
SELECT 
  pd.segment_id,
  pd.segment_name AS segment, 
  SUM(s.qty) AS total_quantity,
  SUM(s.qty * s.price) AS total_revenue,
  SUM((s.qty * s.price) * s.discount / 100) AS total_discount
FROM balanced_tree.sales s
INNER JOIN balanced_tree.product_details pd ON s.prod_id = pd.product_id
GROUP BY pd.segment_id, pd.segment_name;
  ````

#### 📊 Query Result & Insights:
| segment_id | segment | total_quantity | total_revenue | total_discount |
| ---------- | ------- | -------------- | ------------- | -------------- |
| 4          | Jacket  | 11385          | 366983        | 42451          |
| 6          | Socks   | 11217          | 307977        | 35280          |
| 5          | Shirt   | 11265          | 406143        | 48082          |
| 3          | Jeans   | 11349          | 208350        | 23673          |

- For the segment Jacket, the total units sold were 11385 units, the total revenue generated $366983 with $42451 discount.
- For the segment Socks, the total units sold were 11217 units, the total revenue generated $307977 with $35280 discount.
- For the segment Shirt, the total units sold were 11265 units, the total revenue generated $406143 with $48082 discount.
- For the segment Jeans, the total units sold were 11349 units, the total revenue generated $208350 with $23673 discount.
---
#### Q3: What is the top selling product for each segment?  
#### 🧠 My Approach & Solution:
- I created CTE product_totals and used a ranking function (like RANK()) inside the CTE to rank products by quantity per segment.
- In the main query, I selected only products with rank = 1 to get the top-selling product(s) for each segment.

````sql
WITH product_totals AS (
    SELECT 
        pd.segment_name,
        pd.product_name,
        SUM(s.qty) AS total_quantity,
        RANK() OVER (PARTITION BY pd.segment_name ORDER BY SUM(s.qty) DESC) AS rank_in_segment
    FROM balanced_tree.sales s
    INNER JOIN balanced_tree.product_details pd ON s.prod_id = pd.product_id
    GROUP BY pd.segment_name, pd.product_name
)
SELECT 
    segment_name AS segment,
    product_name AS top_selling_product,
    total_quantity
FROM product_totals
WHERE rank_in_segment = 1;
  ````

#### 📊 Query Result & Insights:
| segment | top_selling_product           | total_quantity |
| ------- | ----------------------------- | -------------- |
| Jacket  | Grey Fashion Jacket - Womens  | 3876           |
| Jeans   | Navy Oversized Jeans - Womens | 3856           |
| Shirt   | Blue Polo Shirt - Mens        | 3819           |
| Socks   | Navy Solid Socks - Mens       | 3792           |

---
#### Q4: What is the total quantity, revenue, and discount for each category?  
#### 🧠 My Approach & Solution:

````sql
SELECT 
  pd.category_name, 
  SUM(s.qty) AS total_quantity,
  SUM(s.qty * s.price) AS total_revenue,
  SUM((s.qty * s.price) * s.discount/100) AS total_discount
FROM balanced_tree.sales s
INNER JOIN balanced_tree.product_details pd ON s.prod_id = pd.product_id
GROUP BY pd.category_name;
  ````

#### 📊 Query Result & Insights:
| category_name | total_quantity | total_revenue | total_discount |
| ------------- | -------------- | ------------- | -------------- |
| Mens          | 22482          | 714120        | 83362          |
| Womens        | 22734          | 575333        | 66124          |

---
#### Q5: What is the top selling product for each category?  
#### 🧠 My Approach & Solution:

````sql
WITH product_revenue AS (
    SELECT 
        pd.category_name,
        s.prod_id,
        pd.product_name,
        SUM(s.qty) AS total_quantity,
        RANK() OVER (PARTITION BY pd.category_name ORDER BY SUM(s.price * s.qty) DESC) AS revenue_rank
    FROM balanced_tree.sales s
    INNER JOIN balanced_tree.product_details pd 
        ON s.prod_id = pd.product_id
    GROUP BY pd.category_name, s.prod_id, pd.product_name
)
SELECT 
    category_name,
    product_name,
    total_quantity
FROM product_revenue
WHERE revenue_rank = 1;
  ````

#### 📊 Query Result & Insights:
| category_name | product_name                 | total_quantity |
| ------------- | ---------------------------- | -------------- |
| Mens          | Blue Polo Shirt - Mens       | 3819           |
| Womens        | Grey Fashion Jacket - Womens | 3876           |

---
#### Q6: What is the percentage split of revenue by product for each segment?  
#### 🧠 My Approach & Solution:

````sql
WITH product_sales AS (
    SELECT 
        pd.segment_name,
        s.prod_id,
        pd.product_name,
        SUM(s.price * s.qty) AS revenue
    FROM balanced_tree.sales s
    INNER JOIN balanced_tree.product_details pd ON s.prod_id = pd.product_id
    GROUP BY pd.segment_name, s.prod_id, pd.product_name
),
segment_totals AS (
    SELECT 
        segment_name,
        SUM(revenue) AS total_revenue
    FROM product_sales
    GROUP BY segment_name
)
SELECT 
    ps.segment_name,
    ps.product_name,
    ps.revenue,
    ROUND((ps.revenue / st.total_revenue) * 100, 1) AS revenue_pct
FROM product_sales ps
INNER JOIN segment_totals st ON ps.segment_name = st.segment_name
ORDER BY ps.segment_name, revenue_pct DESC;
  ````

#### 📊 Query Result & Insights:
| segment_name | product_name                     | revenue | revenue_pct |
| ------------ | -------------------------------- | ------- | ----------- |
| Jacket       | Grey Fashion Jacket - Womens     | 209304  | 57.0        |
| Jacket       | Khaki Suit Jacket - Womens       | 86296   | 23.5        |
| Jacket       | Indigo Rain Jacket - Womens      | 71383   | 19.5        |
| Jeans        | Black Straight Jeans - Womens    | 121152  | 58.1        |
| Jeans        | Navy Oversized Jeans - Womens    | 50128   | 24.1        |
| Jeans        | Cream Relaxed Jeans - Womens     | 37070   | 17.8        |
| Shirt        | Blue Polo Shirt - Mens           | 217683  | 53.6        |
| Shirt        | White Tee Shirt - Mens           | 152000  | 37.4        |
| Shirt        | Teal Button Up Shirt - Mens      | 36460   | 9.0         |
| Socks        | Navy Solid Socks - Mens          | 136512  | 44.3        |
| Socks        | Pink Fluro Polkadot Socks - Mens | 109330  | 35.5        |
| Socks        | White Striped Socks - Mens       | 62135   | 20.2        |

---
#### Q7: What is the percentage split of revenue by segment for each category?  
#### 🧠 My Approach & Solution:

````sql
WITH segment_sales AS (
    SELECT 
        pd.category_name,
        pd.segment_name,
        SUM(s.price * s.qty) AS segment_revenue
    FROM balanced_tree.sales s
    JOIN balanced_tree.product_details pd
        ON s.prod_id = pd.product_id
    GROUP BY pd.category_name, pd.segment_name
),
category_totals AS (
    SELECT 
        category_name,
        SUM(segment_revenue) AS total_category_revenue
    FROM segment_sales
    GROUP BY category_name
)
SELECT 
    ss.category_name,
    ss.segment_name,
    ss.segment_revenue,
    ROUND((ss.segment_revenue / ct.total_category_revenue) * 100, 1) AS revenue_pct
FROM segment_sales ss
INNER JOIN category_totals ct ON ss.category_name = ct.category_name
ORDER BY ss.category_name, revenue_pct DESC;
  ````

#### 📊 Query Result & Insights:
| category_name | segment_name | segment_revenue | revenue_pct |
| ------------- | ------------ | --------------- | ----------- |
| Mens          | Shirt        | 406143          | 56.87       |
| Mens          | Socks        | 307977          | 43.13       |
| Womens        | Jacket       | 366983          | 63.79       |
| Womens        | Jeans        | 208350          | 36.21       |

---
#### Q8: What is the percentage split of total revenue by category?  
#### 🧠 My Approach & Solution:

````sql
WITH category_revenue AS (
    SELECT 
        pd.category_name,
        SUM(s.price * s.qty) AS revenue,
        SUM(SUM(s.price * s.qty)) OVER () AS total_revenue
    FROM balanced_tree.sales s
    INNER JOIN balanced_tree.product_details pd ON s.prod_id = pd.product_id
    GROUP BY pd.category_name
)
SELECT 
    category_name,
    revenue,
    ROUND((revenue / total_revenue) * 100, 1) AS revenue_pct
FROM category_revenue
ORDER BY revenue_pct DESC;
  ````

#### 📊 Query Result & Insights:
| category_name | revenue | revenue_pct |
| ------------- | ------- | ----------- |
| Mens          | 714120  | 55.4        |
| Womens        | 575333  | 44.6        |

---
#### Q9: What is the total transaction “penetration” for each product?
#### 🧠 My Approach & Solution:
- Hint: penetration = number of transactions where at least 1 quantity of a product was purchased divided by total number of transactions.
````sql
WITH product_transactions AS (
  SELECT 
    s.prod_id, 
    pd.product_name,
    COUNT(DISTINCT s.txn_id) AS product_txn,
    (SELECT COUNT(DISTINCT txn_id) FROM balanced_tree.sales) AS total_txn
  FROM balanced_tree.sales s
  INNER JOIN balanced_tree.product_details pd ON s.prod_id = pd.product_id
  GROUP BY s.prod_id, pd.product_name
)
SELECT 
  product_name,
  product_txn,
  total_txn,
  CAST(100.0 * product_txn / total_txn AS NUMERIC(10,1)) AS penetration_pct
FROM product_transactions
ORDER BY penetration_pct DESC;
  ````

#### 📊 Query Result & Insights:
| product_name                     | product_txn | total_txn | penetration_pct |
| -------------------------------- | ----------- | --------- | --------------- |
| Navy Solid Socks - Mens          | 1281        | 2500      | 51.2            |
| Navy Oversized Jeans - Womens    | 1274        | 2500      | 51.0            |
| Grey Fashion Jacket - Womens     | 1275        | 2500      | 51.0            |
| Blue Polo Shirt - Mens           | 1268        | 2500      | 50.7            |
| White Tee Shirt - Mens           | 1268        | 2500      | 50.7            |
| Pink Fluro Polkadot Socks - Mens | 1258        | 2500      | 50.3            |
| Indigo Rain Jacket - Womens      | 1250        | 2500      | 50.0            |
| Khaki Suit Jacket - Womens       | 1247        | 2500      | 49.9            |
| Black Straight Jeans - Womens    | 1246        | 2500      | 49.8            |
| Teal Button Up Shirt - Mens      | 1242        | 2500      | 49.7            |
| Cream Relaxed Jeans - Womens     | 1243        | 2500      | 49.7            |
| White Striped Socks - Mens       | 1243        | 2500      | 49.7            |

---
#### Q10: What is the most common combination of at least 1 quantity of any 3 products in a single transaction?  
#### 🧠 My Approach & Solution:
- To be added!

---
