## 🥑 Case Study #3 - Foodie-Fi | D. Outside The Box Questions | Solutions | Result

The following are open ended questions which might be asked during a technical interview for this case study - there are no right or wrong answers, but answers that make sense from both a technical and a business perspective make an amazing impression!

#### Q1: How would you calculate the rate of growth for Foodie-Fi?
To calculate Foodie-Fi’s rate of growth, we can track the customer growth rate over time, measuring how the number of active or paying customers changes from one period to the next.

````sql
WITH monthly_customers AS (
    SELECT 
        DATE_TRUNC('month', start_date) AS month_start,
        TO_CHAR(start_date, 'Mon YYYY') AS month_year,
        COUNT(DISTINCT customer_id) AS new_customers
    FROM foodie_fi.subscriptions
    WHERE plan_id <> 4
    GROUP BY 1, 2
)

SELECT
    month_year, new_customers,
    LAG(new_customers) OVER (ORDER BY month_start) AS prev_month_customers,
    ROUND(100.0 * (new_customers - LAG(new_customers) OVER (ORDER BY month_start)) / LAG(new_customers) OVER (ORDER BY month_start), 1) || '%' AS customer_growth_rate
FROM monthly_customers
ORDER BY month_start;
  ````

#### 📊 Query Result & Insights:
| month_year | new_customers | prev_month_customers | customer_growth_rate |
| ---------- | ------------- | -------------------- | -------------------- |
| Jan 2020   | 88            |                      |                      |
| Feb 2020   | 85            | 88                   | -3.4%                |
| Mar 2020   | 114           | 85                   | 34.1%                |
| Apr 2020   | 109           | 114                  | -4.4%                |
| May 2020   | 128           | 109                  | 17.4%                |
| Jun 2020   | 123           | 128                  | -3.9%                |
| Jul 2020   | 132           | 123                  | 7.3%                 |
| Aug 2020   | 152           | 132                  | 15.2%                |
| Sep 2020   | 140           | 152                  | -7.9%                |
| Oct 2020   | 146           | 140                  | 4.3%                 |
| Nov 2020   | 123           | 146                  | -15.8%               |
| Dec 2020   | 133           | 123                  | 8.1%                 |
| Jan 2021   | 58            | 133                  | -56.4%               |
| Feb 2021   | 29            | 58                   | -50.0%               |
| Mar 2021   | 24            | 29                   | -17.2%               |
| Apr 2021   | 20            | 24                   | -16.7%               |

#### Q2: What key metrics would you recommend Foodie-Fi management to track over time to assess the performance of their overall business?
Key metrics I would recommend to Foodie-Fi management:
- Monthly Recurring Revenue (MRR): Measures predictable revenue growth and trends over time.
- Customer Growth / Active Users: Tracks how many new customers join and how many are actively using the service (DAU/MAU).
- Churn Rate: Percentage of customers cancelling subscriptions; helps identify retention issues.
- Trial-to-Paid Conversion Rate: Measures how effectively free trial users become paying customers.
- Customer Lifetime Value (LTV): Shows the total revenue a customer brings over their subscription, helping prioritize high-value customers.

#### Q3: What are some key customer journeys or experiences that you would analyse further to improve customer retention?
Key customer journeys to analyze for retention:
- Cancellations to understand why customers leave.
- Trial-to-paid conversion to see where users drop off before upgrading.
- Feature usage to identify which features drive engagement and retention.
- Engagement patterns to spot inactive users and triggers for churn.
- Support interactions to assess how support impacts customer loyalty.
  
#### Q4: If the Foodie-Fi team were to create an exit survey shown to customers who wish to cancel their subscription, what questions would you include in the survey?
I would include the following questions in the survey:
- Q1. What are the main reasons you’re cancelling your subscription? (Subscription costly / Found an alternative / Missing features / Other)
- Q2. Overall, how satisfied were you with your Foodie-Fi experience? (Very satisfied / Satisfied / Dissatisfied / Very dissatisfied)
- Q3. Which feature(s) did you feel were missing? (More cooking ideas / Better recommendations / Easier navigation / Other)
- Q4. Would you consider returning to Foodie-Fi in the future? (Definitely / Maybe / No)
- Q5. Do you have any additional feedback to help us improve? (Optional) (Please share any thoughts or suggestions: __)

#### Q5: What business levers could the Foodie-Fi team use to reduce the customer churn rate? How would you validate the effectiveness of your ideas?

Business levers to reduce churn:
- Act on feedback by addressing common issues from exit surveys. For example, price concerns can be addressed by offering discounts, extending trials, or adding benefits.
- Besides customer feedback in the exit survey, boost engagement by sending tips, reminders, or promotions and highlighting unused features to keep customers active.
- Also flexible plans & loyalty by offering tiered subscriptions and rewarding long-term customers.

Measuring effectiveness:
- Track churn rate over time.
- Monitor trial-to-paid conversions and plan changes.
- Measure customer satisfaction or NPS.
- Use A/B testing to confirm improvements work.

