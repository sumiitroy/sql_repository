# Created By

[Sumit Roy](https://www.linkedin.com/in/sumiitroy/)


# Pareto Analysis on Superstore Dataset using SQL


The 80-20 rule, also known as the Pareto Principle, 
is a familiar saying that asserts that 80% of outcomes (or outputs) 
result from 20% of all causes (or inputs) for any given event.

# How we will apply it to the Superstore Dataset?

We will check the 20% of the products which make the 80% of the total sales.

## SQL Functions Used

1. Common Table Expressions
2. Window Function (SUM)
3. Aggregation (SUM)

# SQL Query

```sql
with total_sales as -- Calculating the total sales for each product
(
    SELECT product_id,
           SUM(sales) as total_sales
    FROM dbo.superstore
    GROUP BY product_id
),
pareto_sales as
(
    SELECT product_id,
           total_sales,
           SUM(total_sales)OVER(ORDER BY total_sales DESC) as running_sales_sum, -- provides the running sum for each product
           0.80*SUM(total_sales)OVER() as percent_sales -- Provides the 80% of the total sales column
        FROM total_sales
    GROUP BY product_id, total_sales
),
product_list AS
(
SELECT * FROM pareto_sales
WHERE percent_sales >= running_sales_sum -- This will filter all the records where 80% of the sales value is greater than the running sum
)
SELECT round((SELECT COUNT(product_id) FROM product_list)*100.0/(
        SELECT COUNT(DISTINCT product_id)FROM dbo.Superstore),2) -- 22.40% of the products make the 80% of the overall sales 
```
