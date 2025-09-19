## Case Study #7 - Balanced Tree Clothing Co. | D. Reporting Challenge | Questions | Solutions | Results

Write a single SQL script that combines all of the previous questions into a scheduled report that the Balanced Tree team can run at the beginning of each month to calculate the previous month’s values.

Imagine that the Chief Financial Officer (which is also Danny) has asked for all of these questions at the end of every month.

He first wants you to generate the data for January only - but then he also wants you to demonstrate that you can easily run the same analysis for February without many changes (if at all).

Feel free to split up your final outputs into as many tables as you need - but be sure to explicitly reference which table outputs relate to which question for full marks :)  

---

#### 🧠 My Approach & Solution:

#### Balanced Tree Monthly Report – Simple Instructions
- Created a Temporary Table (sales_monthly). This table stores all product-level transactions for the month you want to analyze. For example: January 2021.
- To generate a report for a different month, update the month and year in the WHERE clause.

````sql
CREATE TEMP TABLE sales_monthly AS
(SELECT * FROM balanced_tree.sales
WHERE EXTRACT(MONTH FROM start_txn_time) = 1 AND EXTRACT(YEAR FROM start_txn_time) = 2021);
  ````

#### Full Query
````sql
-- ============================================================================================================================================================================
-- Balanced Tree Monthly Report (Automated)
-- Generates metrics for January 2021.
-- =============================================================================================================================================================================

-- 1. Create a Temporary Table for the Month
CREATE TEMP TABLE sales_monthly AS
SELECT *
FROM balanced_tree.sales
WHERE EXTRACT(MONTH FROM start_txn_time) = 1
  AND EXTRACT(YEAR FROM start_txn_time) = 2021;

-- =============================================================================================================================================================================
-- High-Level Sales Analysis
-- =============================================================================================================================================================================

-- Q1: Total quantity sold for all products
-- Q2: Total revenue before discounts
-- Q3: Total discount amount for all products
-- All the above questions have been answered in the query below as a single table

SELECT
    SUM(qty) AS total_quantity_sold,
    SUM(qty * price) AS total_revenue_before_discount,
    ROUND(SUM(qty * price * discount / 100.0), 2) AS total_discount_amount
FROM sales_monthly;

-- =============================================================================================================================================================================
-- Transaction Analysis
-- =============================================================================================================================================================================

--Q1: How many unique transactions were there?
SELECT COUNT(DISTINCT txn_id) AS total_unique_transactions
FROM sales_monthly;

--Q2: What was the average number of unique products purchased in each transaction?
WITH unique_products_per_transaction AS (
    SELECT 
        txn_id,
        COUNT(DISTINCT prod_id) AS unique_product_count
    FROM sales_monthly
    GROUP BY txn_id
)
SELECT AVG(unique_product_count)::numeric(10,0) AS avg_unique_products_per_transaction
FROM unique_products_per_transaction;

--Q3: What were the 25th, 50th, and 75th percentile values for the revenue per transaction?
WITH transaction_totals AS (
    SELECT txn_id, SUM(price * qty) AS total_amount
    FROM sales_monthly
    GROUP BY txn_id
)
SELECT
    PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY total_amount) AS "Percentile 25th",
    PERCENTILE_CONT(0.5)  WITHIN GROUP (ORDER BY total_amount) AS "Percentile 50th",
    PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY total_amount) AS "Percentile 75th"
FROM transaction_totals;

--Q4: What was the average discount value per transaction?
WITH txn_discounts AS (
    SELECT 
        txn_id, SUM(qty * price * discount / 100) AS total_discount
    FROM sales_monthly
    GROUP BY txn_id
)
SELECT ROUND(AVG(total_discount), 0) AS avg_discount_per_transaction
FROM txn_discounts;

--Q5: What was the percentage split of all transactions for members vs non-members?
WITH txn_member AS (
    SELECT DISTINCT 
        txn_id, CASE WHEN member = 't' THEN 1 ELSE 0 END AS member
    FROM sales_monthly
)
SELECT 
    ROUND(100.0 * SUM(member) / COUNT(txn_id), 0) AS "percentage transactions for members",
    ROUND(100.0 * (COUNT(txn_id) - SUM(member)) / COUNT(txn_id), 0) AS "percentage transactions for non-members"
FROM txn_member;

--Q6: What was the average revenue for member transactions and non-member transactions?
WITH txn_revenue AS (
    SELECT 
        txn_id, CASE WHEN member = 't' THEN 'Member' ELSE 'Non-Member' END AS member_status,
        SUM(price * qty) AS total_revenue
    FROM sales_monthly
    GROUP BY txn_id, member
)
SELECT member_status,ROUND(AVG(total_revenue), 1) AS avg_revenue
FROM txn_revenue
GROUP BY member_status;

-- =============================================================================================================================================================================
-- Product Analysis
-- =============================================================================================================================================================================

--Q1: What are the top 3 products by total revenue before discount?
WITH product_revenue AS (
    SELECT 
        s.prod_id,
        pd.product_name,
        SUM(s.price * s.qty) AS total_revenue
    FROM sales_monthly s
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
 
--Q2: What is the total quantity, revenue, and discount for each segment?  
SELECT 
  pd.segment_id,
  pd.segment_name AS segment, 
  SUM(s.qty) AS total_quantity,
  SUM(s.qty * s.price) AS total_revenue,
  SUM((s.qty * s.price) * s.discount / 100) AS total_discount
FROM sales_monthly s
INNER JOIN balanced_tree.product_details pd ON s.prod_id = pd.product_id
GROUP BY pd.segment_id, pd.segment_name;
 
--Q3: What is the top selling product for each segment?  
WITH product_totals AS (
    SELECT 
        pd.segment_name,
        pd.product_name,
        SUM(s.qty) AS total_quantity,
        RANK() OVER (PARTITION BY pd.segment_name ORDER BY SUM(s.qty) DESC) AS rank_in_segment
    FROM sales_monthly s
    INNER JOIN balanced_tree.product_details pd ON s.prod_id = pd.product_id
    GROUP BY pd.segment_name, pd.product_name
)
SELECT 
    segment_name AS segment,
    product_name AS top_selling_product,
    total_quantity
FROM product_totals
WHERE rank_in_segment = 1;
  
--Q4: What is the total quantity, revenue, and discount for each category?  
SELECT 
  pd.category_name, 
  SUM(s.qty) AS total_quantity,
  SUM(s.qty * s.price) AS total_revenue,
  SUM((s.qty * s.price) * s.discount/100) AS total_discount
FROM sales_monthly s
INNER JOIN balanced_tree.product_details pd ON s.prod_id = pd.product_id
GROUP BY pd.category_name;

--Q5: What is the top selling product for each category?  
WITH product_revenue AS (
    SELECT 
        pd.category_name,
        s.prod_id,
        pd.product_name,
        SUM(s.qty) AS total_quantity,
        RANK() OVER (PARTITION BY pd.category_name ORDER BY SUM(s.price * s.qty) DESC) AS revenue_rank
    FROM sales_monthly s
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
  
--Q6: What is the percentage split of revenue by product for each segment?  
WITH product_sales AS (
    SELECT 
        pd.segment_name,
        s.prod_id,
        pd.product_name,
        SUM(s.price * s.qty) AS revenue
    FROM sales_monthly s
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

--Q7: What is the percentage split of revenue by segment for each category?  
WITH segment_sales AS (
    SELECT 
        pd.category_name,
        pd.segment_name,
        SUM(s.price * s.qty) AS segment_revenue
    FROM sales_monthly s
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

--Q8: What is the percentage split of total revenue by category?  
WITH category_revenue AS (
    SELECT 
        pd.category_name,
        SUM(s.price * s.qty) AS revenue,
        SUM(SUM(s.price * s.qty)) OVER () AS total_revenue
    FROM sales_monthly s
    INNER JOIN balanced_tree.product_details pd ON s.prod_id = pd.product_id
    GROUP BY pd.category_name
)
SELECT 
    category_name,
    revenue,
    ROUND((revenue / total_revenue) * 100, 1) AS revenue_pct
FROM category_revenue
ORDER BY revenue_pct DESC;

--Q9: What is the total transaction “penetration” for each product?
WITH product_transactions AS (
  SELECT 
    s.prod_id, 
    pd.product_name,
    COUNT(DISTINCT s.txn_id) AS product_txn,
    (SELECT COUNT(DISTINCT txn_id) FROM sales_monthly) AS total_txn
  FROM sales_monthly s
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

--Q10: What is the most common combination of at least 1 quantity of any 3 products in a single transaction?  
WITH txn_products AS (
    SELECT 
        s.txn_id AS transaction_id, 
        p.product_name AS product_name
    FROM sales_monthly s
    LEFT JOIN balanced_tree.product_details p ON s.prod_id = p.product_id
)
SELECT 
    tp1.product_name AS product_first,
    tp2.product_name AS product_second,
    tp3.product_name AS product_third,
    COUNT(*) AS transaction_count
FROM txn_products tp1
LEFT JOIN txn_products tp2 ON tp1.transaction_id = tp2.transaction_id
	AND tp1.product_name < tp2.product_name
LEFT JOIN txn_products tp3 ON tp1.transaction_id = tp3.transaction_id 
    AND tp1.product_name < tp3.product_name 
    AND tp2.product_name < tp3.product_name
WHERE tp1.product_name IS NOT NULL
  AND tp2.product_name IS NOT NULL
  AND tp3.product_name IS NOT NULL
GROUP BY tp1.product_name, tp2.product_name, tp3.product_name
ORDER BY transaction_count DESC
LIMIT 1;
  ````
