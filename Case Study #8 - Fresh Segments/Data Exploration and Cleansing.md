## Data Exploration and Cleansing | Questions | Solutions | Results

#### Q1: Update the `fresh_segments.interest_metrics` table by modifying the `month_year` column to be a date data type with the start of the month
#### Q2: What is count of records in the `fresh_segments.interest_metrics` for each `month_year` value sorted in chronological order (earliest to latest) with the null values appearing first?
#### Q3: What do you think we should do with these null values in the `fresh_segments.interest_metrics`
#### Q3: What do you think we should do with these null values in the `fresh_segments.interest_metrics`
#### Q4: How many `interest_id` values exist in the `fresh_segments.interest_metrics` table but not in the `fresh_segments.interest_map` table? What about the other way around?
#### Q5: Summarise the `id` values in the `fresh_segments.interest_map` by its total record count in this table
#### Q6: What sort of table join should we perform for our analysis and why? Check your logic by checking the rows where `interest_id = 21246` in your joined output and include all columns from `fresh_segments.interest_metrics` and all columns from `fresh_segments.interest_map except` from the `id` column.
#### Q7: Are there any records in your joined table where the `month_year` value is before the `created_at` value from the `fresh_segments.interest_map` table? Do you think these values are valid and why?
