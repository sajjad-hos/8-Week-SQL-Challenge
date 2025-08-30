## Case Study #4 - A. Customer Nodes Exploration | Solutions | Result

#### Q1: How many unique nodes are there on the Data Bank system?
#### 🧠 My Approach & Solution:
- I used `COUNT(DISTINCT node_id)` on the customer_nodes table to calculate the total number of unique node IDs present in the system.

````sql
SELECT COUNT(DISTINCT node_id) AS customer_unique_nodes
FROM data_bank.customer_nodes;
  ````

#### 📊 Query Result & Insights:
| customer_unique_nodes |
| --------------------- |
| 5                     |

- The Data Bank system operates using a total of 5 unique nodes across its entire network.
---
#### Q2: What is the number of nodes per region?
#### 🧠 My Approach & Solution:
- I joined the `customer_nodes` and `regions` tables to connect each node to its region and then used `COUNT(DISTINCT cn.node_id)` to find the unique count of nodes per region.

````sql
SELECT 
	r.region_name, 
    COUNT(DISTINCT cn.node_id) AS number_of_nodes
FROM customer_nodes cn
INNER JOIN regions r ON cn.region_id = r.region_id
GROUP BY r.region_name;
  ````

#### 📊 Query Result & Insights:
| region_name | number_of_nodes |
| ----------- | --------------- |
| Africa      | 5               |
| America     | 5               |
| Asia        | 5               |
| Australia   | 5               |
| Europe      | 5               |

- All the regions operate with 5 nodes per region.
---
#### Q3: How many customers are allocated to each region?
#### 🧠 My Approach & Solution:
- I joined the `customer nodes` and `regions` tables to count all node assignments, grouping the results by each region's name.
- Then I counted and showed the region with the most allocations at the top.

````sql
SELECT 
  r.region_name,
  COUNT(cn.node_id) AS nodes
FROM customer_nodes cn
INNER JOIN regions r ON cn.region_id = r.region_id
GROUP BY r.region_name
ORDER BY nodes DESC;
  ````

#### 📊 Query Result & Insights:
| region_name | nodes |
| ----------- | ----- |
| Australia   | 770   |
| America     | 735   |
| Africa      | 714   |
| Asia        | 665   |
| Europe      | 616   |
- Australia has the highest number of node allocations with 770, while Europe has the lowest with 616. This indicates an uneven distribution of customer nodes across regions.
---
#### Q4: How many days on average are customers reallocated to a different node?
#### 🧠 My Approach & Solution:
- I found the first time each customer was assigned to each node using `MIN()` and `GROUP BY`.
- I then calculated the days between moves for each customer using the `LEAD()` window function to get the next node's start date.
- Finally, I averaged these day intervals, excluding null values from final nodes where no next move existed.

````sql
WITH allocation_start AS (
  SELECT 
    customer_id,
    region_id,
    node_id,
    MIN(start_date) AS allocation_date
  FROM customer_nodes
  GROUP BY customer_id, region_id, node_id
),
allocation_diff AS (
  SELECT
    customer_id,
    node_id,
    region_id,
    allocation_date,
    LEAD(allocation_date) OVER (PARTITION BY customer_id ORDER BY allocation_date) - allocation_date AS days_until_move
  FROM allocation_start
)
SELECT 
  ROUND(AVG(days_until_move), 1) AS avg_moving_days
FROM allocation_diff
WHERE days_until_move IS NOT NULL;
  ````

#### 📊 Query Result & Insights:
| avg_moving_days |
| --------------- |
| 23.7            |

- On average, a customer is reallocated to a new node every 24 days.
---
#### Q5: What is the median, 80th and 95th percentile for this same reallocation days metric for each region?
#### 🧠 My Approach & Solution:
- I used a CTE with `MIN(start_date)` and `GROUP BY` to isolate the first occurrence of every customer-node assignment. It is crucial to accurately calculate the start of each allocation period and avoid duplicate records.
- I then employed the `LEAD()` window function, partitioned by customer_id and ordered by first_date, to create a calculated column (moving_days) that  measures the time a customer spent on each node.
- Finally, I used the `PERCENTILE_CONT()` function within a `GROUP BY` region_name to compute the `median`, `80th`, and `95th percentiles`, which provide a statistical summary of the reallocation day distributions across all regions.

````sql
WITH node_start_dates AS (
  SELECT 
    customer_id,
    region_id,
    node_id,
    MIN(start_date) AS first_date
  FROM customer_nodes
  GROUP BY customer_id, region_id, node_id
),
reallocation_intervals AS (
  SELECT
    customer_id,
    region_id,
    node_id,
    first_date,
    LEAD(first_date) OVER(PARTITION BY customer_id ORDER BY first_date) - first_date AS moving_days
  FROM node_start_dates
)

SELECT 
  rg.region_name,
  ROUND(PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY ri.moving_days)::numeric, 1) AS median,
  ROUND(PERCENTILE_CONT(0.8) WITHIN GROUP (ORDER BY ri.moving_days)::numeric, 1) AS "80th_percentile",
  ROUND(PERCENTILE_CONT(0.95) WITHIN GROUP (ORDER BY ri.moving_days)::numeric, 1) AS "95th_percentile"
FROM reallocation_intervals ri
INNER JOIN regions rg ON ri.region_id = rg.region_id
WHERE ri.moving_days IS NOT NULL
GROUP BY rg.region_name
ORDER BY rg.region_name;
  ````

#### 📊 Query Result & Insights:
| region_name | median | 80th_percentile | 95th_percentile |
| ----------- | ------ | --------------- | --------------- |
| Africa      | 21.0   | 33.2            | 58.8            |
| America     | 21.0   | 33.2            | 57.0            |
| Asia        | 22.0   | 32.4            | 49.9            |
| Australia   | 22.0   | 31.0            | 54.0            |
| Europe      | 22.0   | 31.0            | 54.3            |

- The typical move (median) takes about 3 weeks (21-22 days) in every region. This is very consistent.
- Most moves (80th percentile) are done in about a month (31-33 days).
- The slowest moves (95th percentile) show some differences: Asia is the fastest, with 95% of moves done in under 50 days. Africa is the slowest, with almost 59 days.
---

