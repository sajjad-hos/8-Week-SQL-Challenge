## Case Study #7 - Balanced Tree Clothing Co. | E. Bonus Challenge | Questions | Solutions | Results
#### Use a single SQL query to transform the `product_hierarchy` and `product_prices` datasets to the `product_details` table.

#### 🧠 My Approach & Solution:

````sql
WITH style_hierarchy AS (
    SELECT 
        style.id AS style_id, 
        style.level_text AS style_name, 
        segment.id AS segment_id, 
        segment.level_text AS segment_name,
        category.id AS category_id,
        category.level_text AS category_name
    FROM balanced_tree.product_hierarchy style
    LEFT JOIN balanced_tree.product_hierarchy segment ON style.parent_id = segment.id
    LEFT JOIN balanced_tree.product_hierarchy category ON segment.parent_id = category.id
    WHERE style.id BETWEEN 7 AND 18
)
SELECT 
    pp.product_id,
    pp.price,
    CONCAT(sh.style_name, ' ', sh.segment_name, ' - ', sh.category_name) AS product_full_name,
    sh.category_id,
    sh.segment_id,
    sh.style_id,
    sh.category_name,
    sh.segment_name,
    sh.style_name
FROM balanced_tree.product_prices pp
LEFT JOIN style_hierarchy sh ON pp.id = sh.style_id;
  ````

#### 📊 Query Result & Insights:
| product_id | price | product_full_name                | category_id | segment_id | style_id | category_name | segment_name | style_name          |
| ---------- | ----- | -------------------------------- | ----------- | ---------- | -------- | ------------- | ------------ | ------------------- |
| c4a632     | 13    | Navy Oversized Jeans - Womens    | 1           | 3          | 7        | Womens        | Jeans        | Navy Oversized      |
| e83aa3     | 32    | Black Straight Jeans - Womens    | 1           | 3          | 8        | Womens        | Jeans        | Black Straight      |
| e31d39     | 10    | Cream Relaxed Jeans - Womens     | 1           | 3          | 9        | Womens        | Jeans        | Cream Relaxed       |
| d5e9a6     | 23    | Khaki Suit Jacket - Womens       | 1           | 4          | 10       | Womens        | Jacket       | Khaki Suit          |
| 72f5d4     | 19    | Indigo Rain Jacket - Womens      | 1           | 4          | 11       | Womens        | Jacket       | Indigo Rain         |
| 9ec847     | 54    | Grey Fashion Jacket - Womens     | 1           | 4          | 12       | Womens        | Jacket       | Grey Fashion        |
| 5d267b     | 40    | White Tee Shirt - Mens           | 2           | 5          | 13       | Mens          | Shirt        | White Tee           |
| c8d436     | 10    | Teal Button Up Shirt - Mens      | 2           | 5          | 14       | Mens          | Shirt        | Teal Button Up      |
| 2a2353     | 57    | Blue Polo Shirt - Mens           | 2           | 5          | 15       | Mens          | Shirt        | Blue Polo           |
| f084eb     | 36    | Navy Solid Socks - Mens          | 2           | 6          | 16       | Mens          | Socks        | Navy Solid          |
| b9a74d     | 17    | White Striped Socks - Mens       | 2           | 6          | 17       | Mens          | Socks        | White Striped       |
| 2feb6b     | 29    | Pink Fluro Polkadot Socks - Mens | 2           | 6          | 18       | Mens          | Socks        | Pink Fluro Polkadot |
