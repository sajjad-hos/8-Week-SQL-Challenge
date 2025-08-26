## Data Exploration and Cleansing | Questions | Solutions | Results

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
#### Q3: What do you think we should do with these null values in the `fresh_segments.interest_metrics`
#### Q3: What do you think we should do with these null values in the `fresh_segments.interest_metrics`
#### Q4: How many `interest_id` values exist in the `fresh_segments.interest_metrics` table but not in the `fresh_segments.interest_map` table? What about the other way around?
#### Q5: Summarise the `id` values in the `fresh_segments.interest_map` by its total record count in this table
#### Q6: What sort of table join should we perform for our analysis and why? Check your logic by checking the rows where `interest_id = 21246` in your joined output and include all columns from `fresh_segments.interest_metrics` and all columns from `fresh_segments.interest_map except` from the `id` column.
#### Q7: Are there any records in your joined table where the `month_year` value is before the `created_at` value from the `fresh_segments.interest_map` table? Do you think these values are valid and why?
