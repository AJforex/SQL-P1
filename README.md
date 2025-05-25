# SQL-P1
-- SQL Retail Sales Analysis
```Create database sql_project_p1;
```
```drop table if exists retail_sales;
```
```create table retail_sales (
	transactions_id	int primary key,
	sale_date date,
	sale_time time,	
	customer_id	int,
	gender varchar(15),
	age	int,
	category varchar(15),
	quantity int,
	price_per_unit float,
	cogs float,
	total_sale float
);
```
```select * from retail_sales;
```
```select count(*) from retail_sales;
```select customer_id, count(customer_id) from retail_sales group by customer_id;

-- Data Cleaning
select * from retail_sales where transactions_id is null or
sale_date is null or sale_time is null or customer_id	is null or
gender is null or age is null or category is null or quantity is null or
price_per_unit is null or cogs is null or total_sale is null;

delete from retail_sales where transactions_id is null or
sale_date is null or sale_time is null or customer_id	is null or
gender is null or age is null or category is null or quantity is null or
price_per_unit is null or cogs is null or total_sale is null;

--Data Exploration
--How many customers we have
select count(distinct customer_id) as tot_cus from retail_sales;

--Data Analysis business key problems and answers
**--1) write a sql query to retrieve all colummns for sales made on "2022-11-05"**
```sql
select * from retail_sales where sale_date = '2022-11-05';
```
/* 2) write a sql query to retrieve all transactions where d category is 'clothing' and d 
quantity sold is more than 3 in the month of nov 2022 */
```select  *
from retail_sales where category = 'Clothing' 
-- grouping by 1 means the first select option is group together
and TO_CHAR(sale_date, 'yyyy-mm')= '2022-11'
and quantity > 3;
```
-- 4) to find avg age of customers who pruchased items frm d 'beauty' category;
select category, round(avg(age), 2) from retail_sales 
where category = 'Beauty' group by 1;


6) --to find d tot number of trans made by each gender in each category
select category, gender, sum(total_sale) from retail_sales 
group by 1, 2 order by 1, 2 desc;

--7) cal avg sale for each month. find d best selling month;
--since d column is date, extract month and year from sale date
--using rank to find d best sellng month in each year
--then use a sub query to nest the intial query
select 
year, month, "avg sale in each month"
from (
select 
extract(year from sale_date) as year,
extract(month from sale_date) as month,
avg(total_sale) as "avg sale in each month",
rank() over(partition by extract(year from sale_date) order by avg(total_sale) desc) as rank
from retail_sales
group by 1, 2
) as "Top list" where rank = 1;

-- 8) find d top5 customers based on d highest total sales
select customer_id, sum(total_sale) as "total sales of each customer" from retail_sales
group by customer_id order by 2 desc limit 5;

-- 9) find d number of unique customers who purchased items from each category;
select category, count(distinct customer_id) as "num of cus" from retail_sales
group by category order by 2 desc;

-- 10) create shift and number of orders(example morning < 12, afternoon 12 & 17, evening >17);
-- using CASE statement will create new column "shift" to meet the condtions required
--we need to extract hour from the time given
--using CTE(common table expression) WITH, to reference the query so as to group by
With hourly_sale
As (
select *,
	CASE 
		when extract(hour from sale_time) < 12 then 'Morning'
		when extract(hour from sale_time) between 12 and 17 then 'Afternoon'
		Else 'Evening'
	End as Shift --name d case column as shift
from retail_sales
)
select shift, count(*) as "total orders in each shift" from hourly_sale
group by shift order by 2 desc;

--End of sql-project_p1




