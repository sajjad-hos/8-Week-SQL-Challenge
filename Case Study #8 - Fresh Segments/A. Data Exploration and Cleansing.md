## A. Data Exploration and Cleansing | Questions | Solutions | Results

#### Q1: Update the `fresh_segments.interest_metrics` table by modifying the `month_year` column to be a date data type with the start of the month
#### 🧠 My Approach & Solution:

- After inspecting the month_year values data type, it's then safely converted to a proper date with the first day of the month.

````sql
ALTER TABLE fresh_segments.interest_metrics
ALTER COLUMN month_year TYPE DATE
USING TO_DATE(month_year, 'MM-YYYY');

-- To verify the answer
SELECT * FROM fresh_segments.interest_metrics LIMIT 10;
  ````

#### 📊 Query Result & Insights:
| _month | _year | month_year | interest_id | composition | index_value | ranking | percentile_ranking |
| ------ | ----- | ---------- | ----------- | ----------- | ----------- | ------- | ------------------ |
| 7      | 2018  | 2018-07-01 | 32486       | 11.89       | 6.19        | 1       | 99.86              |
| 7      | 2018  | 2018-07-01 | 6106        | 9.93        | 5.31        | 2       | 99.73              |
| 7      | 2018  | 2018-07-01 | 18923       | 10.85       | 5.29        | 3       | 99.59              |
| 7      | 2018  | 2018-07-01 | 6344        | 10.32       | 5.1         | 4       | 99.45              |
| 7      | 2018  | 2018-07-01 | 100         | 10.77       | 5.04        | 5       | 99.31              |
| 7      | 2018  | 2018-07-01 | 69          | 10.82       | 5.03        | 6       | 99.18              |
| 7      | 2018  | 2018-07-01 | 79          | 11.21       | 4.97        | 7       | 99.04              |
| 7      | 2018  | 2018-07-01 | 6111        | 10.71       | 4.83        | 8       | 98.9               |
| 7      | 2018  | 2018-07-01 | 6214        | 9.71        | 4.83        | 8       | 98.9               |
| 7      | 2018  | 2018-07-01 | 19422       | 10.11       | 4.81        | 10      | 98.63              |

- The `month_year` column has been updated with a date data type with the start of the month in the `fresh_segments.interest_metrics` table.
---
#### Q2: What is count of records in the `fresh_segments.interest_metrics` for each `month_year` value sorted in chronological order (earliest to latest) with the null values appearing first?
#### 🧠 My Approach & Solution:

- Ordered the results by converting month_year text into a date with TO_DATE(month_year, 'MM-YYYY'), so the months appear in correct chronological order instead of text order.

````sql
SELECT month_year, COUNT(*) AS total_count
FROM fresh_segments.interest_metrics
GROUP BY month_year
ORDER BY TO_DATE(month_year, 'MM-YYYY') NULLS FIRST;
  ````

#### 📊 Query Result & Insights:
| month_year | total_count |
| ---------- | ----------- |
|            | 1194        |
| 07-2018    | 729         |
| 08-2018    | 767         |
| 09-2018    | 780         |
| 10-2018    | 857         |
| 11-2018    | 928         |
| 12-2018    | 995         |
| 01-2019    | 973         |
| 02-2019    | 1121        |
| 03-2019    | 1136        |
| 04-2019    | 1099        |
| 05-2019    | 857         |
| 06-2019    | 824         |
| 07-2019    | 864         |
| 08-2019    | 1149        |

- Each `month_year` value is sorted in chronological order (earliest to latest) with the null values appearing first.
---
#### Q3: What do you think we should do with these null values in the `fresh_segments.interest_metrics`
#### 🧠 My Approach & Solution:

Before I made the final decision, I checked whether the corresponding columns of month_year are NULL or not. Then, I calculated what percentage of the data this represents.

Check 1: whether the corresponding columns of month_year are NULL or not
````sql
SELECT * FROM fresh_segments.interest_metrics
WHERE month_year is NULL ;
  ````
Check 2: What percentage of the values are NULL?
````sql
SELECT 100* COUNT(*) / (SELECT COUNT(*) FROM fresh_segments.interest_metrics ) AS percent_null
FROM fresh_segments.interest_metrics
WHERE month_year is NULL;
  ````

#### 📊 Query Result & Insights:
| percent_null |
| ------------ |
| 8            |

The `_month`, `_year`, and `interested_id` columns are NULL while the `month_year` value is also NULL, making these rows unusable for time-based analyses such as time series, trends, cohorts, seasonality, or forecasting. Since 8% of the data is affected, the best option is to remove them.

To be safe, I stored them in a separate table for future audit/reference.
````sql
 -- A table to store rows with NULLs
CREATE TABLE fresh_segments.interest_metrics_removed AS
SELECT *FROM fresh_segments.interest_metrics
WHERE _month IS NULL OR _year IS NULL OR month_year IS NULL OR interest_id IS NULL;
     
-- See stored data sample with 2 rows
SELECT * FROM fresh_segments.interest_metrics_removed LIMIT 2;
  ````
| _month | _year | month_year | interest_id | composition | index_value | ranking | percentile_ranking |
| ------ | ----- | ---------- | ----------- | ----------- | ----------- | ------- | ------------------ |
|        |       |            |             | 6.12        | 2.85        | 43      | 96.4               |
|        |       |            |             | 7.13        | 2.84        | 45      | 96.23              |
---

#### Q4: How many `interest_id` values exist in the `fresh_segments.interest_metrics` table but not in the `fresh_segments.interest_map` table? What about the other way around?
Total Count of interest_ids that exist in the metrics table but not in the map table.

````sql
 SELECT COUNT(*) AS exist_in_metrics_but_not_in_map
 FROM fresh_segments.interest_metrics im
 LEFT JOIN fresh_segments.interest_map mp ON im.interest_id = mp.id::text
 WHERE im.interest_id IS NOT NULL AND mp.id IS NULL;
  ````

#### 📊 Query Result & Insights:
| exist_in_metrics_but_not_in_map |
| ------------------------------- |
| 0                               |

- A total of 0 interest_ids exist in the metrics table but not in the map table.

Total Count of interest_ids that exist in the map table but not in the metrics table.

````sql
SELECT COUNT(*) AS exist_in_map_but_not_in_metrics
FROM fresh_segments.interest_map mp
LEFT JOIN fresh_segments.interest_metrics m ON mp.id::text = m.interest_id
WHERE mp.id IS NOT NULL AND m.interest_id IS NULL;
  ````

#### 📊 Query Result & Insights:
| exist_in_map_but_not_in_metrics |
| ------------------------------- |
| 7                               |

- A total of 7 interest_ids exist in the map table but not in the metrics table.
---
#### Q5: Summarise the `id` values in the `fresh_segments.interest_map` by its total record count in this table

````sql
SELECT COUNT(*) AS COUNT_ID  
FROM fresh_segments.interest_map;
  ````

#### 📊 Query Result & Insights:
| count_id |
| -------- |
| 1209     |


---

#### Q6: What sort of table join should we perform for our analysis and why? Check your logic by checking the rows where `interest_id = 21246` in your joined output and include all columns from `fresh_segments.interest_metrics` and all columns from `fresh_segments.interest_map except` from the `id` column.

Reason for using LEFT JOIN: because interest_metrics is our primary analysis table, and dropping rows from it would lead to data loss. An INNER JOIN would exclude any interest_id values that don’t exist in interest_map, which could bias results or reduce completeness. By using a LEFT JOIN, we keep every metric record and only add descriptive fields from interest_map where available. Checking with interest_id = 21246 confirms this: all metric rows remain, and map details are included when a match exists.

````sql
SELECT im.*, mp.interest_name, mp.interest_summary, mp.created_at, mp.last_modified
FROM fresh_segments.interest_metrics AS im
LEFT JOIN fresh_segments.interest_map AS mp ON im.interest_id = mp.id::text
WHERE im.interest_id = '21246';
  ````

#### 📊 Query Result & Insights:
| _month | _year | month_year | interest_id | composition | index_value | ranking | percentile_ranking | interest_name                    | interest_summary                                      | created_at          | last_modified       |
| ------ | ----- | ---------- | ----------- | ----------- | ----------- | ------- | ------------------ | -------------------------------- | ----------------------------------------------------- | ------------------- | ------------------- |
| 7      | 2018  | 07-2018    | 21246       | 2.26        | 0.65        | 722     | 0.96               | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11 17:50:04 | 2018-06-11 17:50:04 |
| 8      | 2018  | 08-2018    | 21246       | 2.13        | 0.59        | 765     | 0.26               | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11 17:50:04 | 2018-06-11 17:50:04 |
| 9      | 2018  | 09-2018    | 21246       | 2.06        | 0.61        | 774     | 0.77               | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11 17:50:04 | 2018-06-11 17:50:04 |
| 10     | 2018  | 10-2018    | 21246       | 1.74        | 0.58        | 855     | 0.23               | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11 17:50:04 | 2018-06-11 17:50:04 |
| 11     | 2018  | 11-2018    | 21246       | 2.25        | 0.78        | 908     | 2.16               | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11 17:50:04 | 2018-06-11 17:50:04 |
| 12     | 2018  | 12-2018    | 21246       | 1.97        | 0.7         | 983     | 1.21               | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11 17:50:04 | 2018-06-11 17:50:04 |
| 1      | 2019  | 01-2019    | 21246       | 2.05        | 0.76        | 954     | 1.95               | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11 17:50:04 | 2018-06-11 17:50:04 |
| 2      | 2019  | 02-2019    | 21246       | 1.84        | 0.68        | 1109    | 1.07               | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11 17:50:04 | 2018-06-11 17:50:04 |
| 3      | 2019  | 03-2019    | 21246       | 1.75        | 0.67        | 1123    | 1.14               | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11 17:50:04 | 2018-06-11 17:50:04 |
| 4      | 2019  | 04-2019    | 21246       | 1.58        | 0.63        | 1092    | 0.64               | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11 17:50:04 | 2018-06-11 17:50:04 |
|        |       |            | 21246       | 1.61        | 0.68        | 1191    | 0.25               | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11 17:50:04 | 2018-06-11 17:50:04 |

---

#### Q7: Are there any records in your joined table where the `month_year` value is before the `created_at` value from the `fresh_segments.interest_map` table? Do you think these values are valid and why?

````sql
SELECT  COUNT(*) AS total_invalid_records
FROM fresh_segments.interest_map map
LEFT JOIN fresh_segments.interest_metrics metrics ON map.id::text = metrics.interest_id
WHERE TO_DATE(metrics.month_year, 'MM-YYYY') < map.created_at::DATE;
  ````

#### 📊 Query Result & Insights:
| total_invalid_records |
| --------------------- |
| 188                   |

- There are 188 records where month_year comes before created_at, but this is valid since month_year represents the start of the month as a period label, while created_at captures the exact date the interest was added; the mismatch reflects different time representations, not incorrect data.

---
