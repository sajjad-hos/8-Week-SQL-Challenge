## C. Segment Analysis | Questions | Solutions | Results

#### Q1: Using our filtered dataset by removing the interests with less than 6 months worth of data, which are the top 10 and bottom 10 interests which have the largest composition values in any `month_year`? Only use the maximum composition value for each interest but you must keep the corresponding `month_year`
#### Q2: Which 5 interests had the lowest average `ranking` value?
#### 🧠 My Approach & Solution:

````sql
SELECT
  ima.interest_name,
  ROUND(AVG(ime.ranking), 1) AS avg_ranking
FROM fresh_segments.interest_metrics ime
INNER JOIN fresh_segments.interest_map ima ON ime.interest_id::INT = ima.id
GROUP BY ima.interest_name
ORDER BY avg_ranking ASC
LIMIT 5;
  ````

#### 📊 Query Result & Insights:
| interest_name                  | avg_ranking |
| ------------------------------ | ----------- |
| Winter Apparel Shoppers        | 1.0         |
| Fitness Activity Tracker Users | 4.1         |
| Mens Shoe Shoppers             | 5.9         |
| Elite Cycling Gear Shoppers    | 7.8         |
| Shoe Shoppers                  | 9.4         |

---
#### Q3: Which 5 interests had the largest standard deviation in their `percentile_ranking` value?
#### Q4: For the 5 interests found in the previous question - what was minimum and maximum `percentile_ranking` values for each interest and its corresponding `year_month` value? Can you describe what is happening for these 5 interests?
#### Q5: How would you describe our customers in this segment based off their composition and ranking values? What sort of products or services should we show to these customers and what should we avoid?
