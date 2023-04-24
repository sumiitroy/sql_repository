# Recursive Common Table Expression

Created By: [Sumit Roy](https://www.linkedin.com/in/sumiitroy/)

######  A Recursive CTE is a CTE that references itself. The CTE repeatedly executes, returns subsets of data, until it returns the complete result set.

Sample Data for Sales Table:

product_id | period_start | period_end | average_daily_sales
--------------|---------------------|-----------------|----------------------------
1 | 2019-01-25 | 2019-02-28 | 100
2 | 2018-12-01 | 2020-01-01 | 10
3 | 2019-12-01 | 2020-01-31 | 1




Result Set Required:

Year  |    Product_id  |   average_daily_sales
-------|----------------------|----------------------------
2018| 2 | 10
2019 | 1 |100
2019 | 2 | 10
2019 | 3 | 1
2020 | 2 | 10
2020 | 3 |1



```sql
create table sales (
product_id int,
period_start date,
period_end date,
average_daily_sales int
);

insert into sales values(1,'2019-01-25','2019-02-28',100),
(2,'2018-12-01','2020-01-01',10),
(3,'2019-12-01','2020-01-31',1);

with cte as
(
SELECT MIN(period_start) as min_date, MAX(period_end) as max_date
FROM dbo.sales -- Anchor Member
UNION ALL
SELECT DATEADD(DAY, 1, min_date) as dates, max_date FROM cte -- Recursive Member
WHERE DATEADD(DAY, 1, min_date) < max_date -- Terminate Condition
)

SELECT YEAR(min_date) as year_sale, product_id, average_daily_sales FROM cte
INNER JOIN dbo.sales
ON min_date between period_start and period_end
GROUP BY YEAR(min_date), product_id, average_daily_sales
OPTION(maxrecursion 1000)
/* default maximum number of recursion allowed for CTE is 100 */
```
