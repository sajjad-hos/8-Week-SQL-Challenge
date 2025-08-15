## Index Analysis | Questions | Solutions | Results

The `index_value` is a measure which can be used to reverse calculate the average composition for Fresh Segments’ clients.

Average composition can be calculated by dividing the `composition` column by the `index_value` column rounded to 2 decimal places.

1. What is the top 10 interests by the average composition for each month?
2. For all of these top 10 interests - which interest appears the most often?
3. What is the average of the average composition for the top 10 interests for each month?
4. What is the 3 month rolling average of the max average composition value from September 2018 to August 2019 and include the previous top ranking interests in the same output shown below.
5. Provide a possible reason why the max average composition might change from month to month? Could it signal something is not quite right with the overall business model for Fresh Segments?

Required output for question 4:

| Month Year | Interest Name                   | Max Index Composition | 3-Month Moving Avg | 1 Month Ago                                     | 2 Months Ago                                    |
|------------|---------------------------------|-----------------------|--------------------|-------------------------------------------------|-------------------------------------------------|
| 2018-09-01 | Work Comes First Travelers      | 8.26                  | 7.61               | Las Vegas Trip Planners: 7.21                   | Las Vegas Trip Planners: 7.36                   |
| 2018-10-01 | Work Comes First Travelers      | 9.14                  | 8.20               | Work Comes First Travelers: 8.26                | Las Vegas Trip Planners: 7.21                   |
| 2018-11-01 | Work Comes First Travelers      | 8.28                  | 8.56               | Work Comes First Travelers: 9.14                | Work Comes First Travelers: 8.26                |
| 2018-12-01 | Work Comes First Travelers      | 8.31                  | 8.58               | Work Comes First Travelers: 8.28                | Work Comes First Travelers: 9.14                |
| 2019-01-01 | Work Comes First Travelers      | 7.66                  | 8.08               | Work Comes First Travelers: 8.31                | Work Comes First Travelers: 8.28                |
| 2019-02-01 | Work Comes First Travelers      | 7.66                  | 7.88               | Work Comes First Travelers: 7.66                | Work Comes First Travelers: 8.31                |
| 2019-03-01 | Alabama Trip Planners           | 6.54                  | 7.29               | Work Comes First Travelers: 7.66                | Work Comes First Travelers: 7.66                |
| 2019-04-01 | Solar Energy Researchers        | 6.28                  | 6.83               | Alabama Trip Planners: 6.54                     | Work Comes First Travelers: 7.66                |
| 2019-05-01 | Readers of Honduran Content     | 4.41                  | 5.74               | Solar Energy Researchers: 6.28                  | Alabama Trip Planners: 6.54                     |
| 2019-06-01 | Las Vegas Trip Planners         | 2.77                  | 4.49               | Readers of Honduran Content: 4.41                | Solar Energy Researchers: 6.28                  |
| 2019-07-01 | Las Vegas Trip Planners         | 2.82                  | 3.33               | Las Vegas Trip Planners: 2.77                   | Readers of Honduran Content: 4.41               |
| 2019-08-01 | Cosmetics and Beauty Shoppers   | 2.73                  | 2.77               | Las Vegas Trip Planners: 2.82                   | Las Vegas Trip Planners: 2.77                   |

