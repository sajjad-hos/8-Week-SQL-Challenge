## 🥑 Case Study #3 - Foodie-Fi – B. Data Analysis Questions | Solutions | Result

#### Q1: How many customers has Foodie-Fi ever had?
#### 🧠 My Approach & Solution:
````sql
SELECT COUNT(DISTINCT customer_id) AS total_unique_customers
FROM subscriptions;
  ````

#### 📊 Query Result & Insights:
| total_unique_customers |
| ---------------------- |
| 1000                   |

- Foodie-Fi has had 1000 unique customers in total.
---


