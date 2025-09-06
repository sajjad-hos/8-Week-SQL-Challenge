## Case Study #8 - Fresh Segments | B. Interest Analysis | Questions | Solutions | Results

#### Q1: Which interests have been present in all `month_year` dates in our dataset?
#### 🧠 My Approach & Solution:
- First, I created the total_months CTE, which counts how many unique months exist in the entire table.
- Counted how many unique months each interest_id appears in using COUNT(DISTINCT im.month_year).
- After the CROSS JOIN, for each interest_id row, the total_months is attached.
- Then, grouped by interest_id and total months.
- Finally, HAVING filters only interest_ids where the number of months they appear in equals the total number of months in the dataset.
  
````sql
WITH total_months AS (
    SELECT COUNT(DISTINCT month_year) AS total
    FROM fresh_segments.interest_metrics
)
SELECT 
    im.interest_id,
    COUNT(DISTINCT im.month_year) AS present_in_all_month_year
FROM fresh_segments.interest_metrics im
CROSS JOIN total_months tm
GROUP BY im.interest_id, tm.total
HAVING COUNT(DISTINCT im.month_year) = tm.total
Limit 10;
  ````

#### 📊 Query Result & Insights:
I am showing only 10 rows out of 480 here.
| interest_id | present_in_all_month_year |
| ----------- | ------------------------- |
| 100         | 14                        |
| 10008       | 14                        |
| 10009       | 14                        |
| 10010       | 14                        |
| 101         | 14                        |
| 102         | 14                        |
| 10249       | 14                        |
| 10250       | 14                        |
| 10251       | 14                        |
| 10284       | 14                        |

- A total of 1202 interests are present in all month_year out of 480 interest_id.

---
#### Q2: Using this same `total_months` measure - calculate the cumulative percentage of all records starting at 14 months - which total_months value passes the 90% cumulative percentage value?
#### 🧠 My Approach & Solution:
#### Calculate the cumulative percentage of all records starting at 14 months:
- CTE 1 (interest_months_summary): Counted the number of distinct months each interest_id appeared in (total_months).
- CTE 2 (interest_month_distribution): Counted interest_ids sharing the same total_months to generate a frequency distribution.
- Main Query: Calculated the cumulative percentage of interest_ids in descending order of total_months using a window function.

````sql
-- CTE1: Count the number of distinct months for each interest_id
WITH interest_months_summary AS (
    SELECT interest_id, COUNT(DISTINCT month_year) AS total_months
    FROM fresh_segments.interest_metrics
    WHERE interest_id IS NOT NULL
    GROUP BY interest_id
),
-- CTE 2: Count how many interest_ids have the same number of months
interest_month_distribution AS (
    SELECT total_months, COUNT(interest_id) AS interests
    FROM interest_months_summary
    GROUP BY total_months
)
-- Main query calculates the cumulative percentage of all records starting at 14 months.
SELECT
      total_months, interests,
      CAST(100.0 * SUM(interests) OVER(ORDER BY total_months DESC) / SUM(interests) OVER() AS decimal(10, 2)) AS cumulative_pct
FROM interest_month_distribution
ORDER BY total_months DESC;
  ````

#### 📊 Query Result & Insights:
| total_months | interests | cumulative_pct |
| ------------ | --------- | -------------- |
| 14           | 480       | 39.93          |
| 13           | 82        | 46.76          |
| 12           | 65        | 52.16          |
| 11           | 94        | 59.98          |
| 10           | 86        | 67.14          |
| 9            | 95        | 75.04          |
| 8            | 67        | 80.62          |
| 7            | 90        | 88.10          |
| 6            | 33        | 90.85          |
| 5            | 38        | 94.01          |
| 4            | 32        | 96.67          |
| 3            | 15        | 97.92          |
| 2            | 12        | 98.92          |
| 1            | 13        | 100.00         |

#### total_months value passes the 90% cumulative percentage value:
````sql
-- CTE1: Count the number of distinct months for each interest_id
WITH interest_months_summary AS (
    SELECT interest_id, COUNT(DISTINCT month_year) AS total_months
    FROM fresh_segments.interest_metrics
    WHERE interest_id IS NOT NULL
    GROUP BY interest_id
),
-- CTE 2: Count how many interest_ids have the same number of months
interest_month_distribution AS (
    SELECT total_months, COUNT(interest_id) AS interests
    FROM interest_months_summary
    GROUP BY total_months
),
-- CTE 3 calculate cumulative percentage in descending order of total_months.
total_months_interests_and_cumulative_pct AS (
    SELECT 
        total_months,
        interests,
        CAST(100.0 * SUM(interests) OVER(ORDER BY total_months DESC) / SUM(interests) OVER() AS decimal(10, 2)) AS cumulative_pct
    FROM interest_month_distribution
    ORDER BY total_months DESC
)
-- Main query shows which total_months value passes the 90% cumulative percentage value?
SELECT total_months, interests, cumulative_pct
FROM total_months_interests_and_cumulative_pct
WHERE cumulative_pct >= 90
ORDER BY total_months DESC;
  ````

#### 📊 Query Result & Insights:
| total_months | interests | cumulative_pct |
| ------------ | --------- | -------------- |
| 6            | 33        | 90.85          |
| 5            | 38        | 94.01          |
| 4            | 32        | 96.67          |
| 3            | 15        | 97.92          |
| 2            | 12        | 98.92          |
| 1            | 13        | 100.00         |

- 6 total_months value passes the 90% cumulative percentage value.
  
#### Q3: If we were to remove all `interest_id` values which are lower than the `total_months` value we found in the previous question - how many total data points would we be removing?
#### 🧠 My Approach & Solution:
- The CTE interest_months_summary calculates the number of distinct months each interest_id appears in.
- Identified interest_ids that appear in fewer than 6 months.
- All rows in the original table corresponding to these interest_ids are counted, giving the total rows that would be removed.

````sql
WITH interest_months_summary AS (
    SELECT interest_id, COUNT(DISTINCT month_year) AS total_months
    FROM fresh_segments.interest_metrics
    WHERE interest_id IS NOT NULL
    GROUP BY interest_id
)
SELECT COUNT(interest_id) AS rows_would_be_removed
FROM fresh_segments.interest_metrics
WHERE interest_id IN (
  SELECT interest_id 
  FROM interest_months_summary
  WHERE total_months < 6);
  ````

#### 📊 Query Result & Insights:
| rows_would_be_removed |
| --------------------- |
| 400                   |

- A total of 400 data points would be removed!

#### Q4: Does this decision make sense to remove these data points from a business perspective? Use an example where there are all 14 months present to a removed `interest` example for your arguments - think about what it means to have less months present from a segment perspective.
#### Q5: After removing these interests - how many unique interests are there for each month?
