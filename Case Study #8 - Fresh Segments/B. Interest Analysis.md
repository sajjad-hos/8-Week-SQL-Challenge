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
#### Q3: If we were to remove all `interest_id` values which are lower than the `total_months` value we found in the previous question - how many total data points would we be removing?
#### Q4: Does this decision make sense to remove these data points from a business perspective? Use an example where there are all 14 months present to a removed `interest` example for your arguments - think about what it means to have less months present from a segment perspective.
#### Q5: After removing these interests - how many unique interests are there for each month?
