## Case Study #7 - Balanced Tree Clothing Co. | A. High Level Sales Analysis | Questions | Solutions | Results

#### Q1: What was the total quantity sold for all products?
#### 🧠 My Approach & Solution:

- Added the qty column across all sales records to get the total quantity sold for all products.
  
````sql
SELECT SUM(qty) AS "Total Quantity Sold"
FROM balanced_tree.sales;
  ````

#### 📊 Query Result & Insights:
| Total Quantity Sold |
| ------------------- |
| 45216               |

- The total quantity sold for all products was 45216.
---

#### Q2: What is the total generated revenue for all products before discounts?
#### 🧠 My Approach & Solution:

- Multiplied qty by price for each sale to get the revenue per sale, then summed all these values to calculate the total revenue before discount.
  
````sql
SELECT SUM(qty * price) AS "Total Revenue Before Discount"
FROM balanced_tree.sales
  ````

#### 📊 Query Result & Insights:
| Total Revenue Before Discount |
| ----------------------------- |
| 1289453                       |

- The total generated revenue for all products before discounts was 1289453.
---

#### Q3: What was the total discount amount for all products?
#### 🧠 My Approach & Solution:

- Added the (qty * price * discount) / 100 across every row in the sales table
  
````sql
SELECT ROUND(SUM(qty * price * discount / 100.0), 2) AS "Total Discount Amount"
FROM balanced_tree.sales;
  ````

#### 📊 Query Result & Insights:
| Total Discount Amount |
| --------------------- |
| 156229.14              |

- The total discount amount for all products was 156229.1
---


