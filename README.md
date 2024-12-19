### sql-project
## SQL Retail Sales Analysis
```sql
CREATE DATABASE sql_project;

-- Create TABLE
use sql_project;
CREATE TABLE retail_sales
            (
                transaction_id INT PRIMARY KEY,	
                sale_date DATE,	 
                sale_time TIME,	
                customer_id	INT,
                gender	VARCHAR(15),
                age	INT,
                category VARCHAR(15),	
                quantity	INT,
                price_per_unit FLOAT,	
                cogs	FLOAT,
                total_sale FLOAT
            );

SELECT count(*) FROM retail_sales
LIMIT 10


    

SELECT 
    COUNT(*) 
FROM retail_sales;
```

## Data Cleaning
```sql
SELECT * FROM retail_sales
WHERE transaction_id IS NULL

SELECT * FROM retail_sales
WHERE sale_date IS NULL

SELECT * FROM retail_sales
WHERE sale_time IS NULL

SELECT * FROM retail_sales
WHERE 
    transaction_id IS NULL
    OR
    sale_date IS NULL
    OR 
    sale_time IS NULL
    OR
    gender IS NULL
    OR
    category IS NULL
    OR
    quantity IS NULL
    OR
    cogs IS NULL
    OR
    total_sale IS NULL;
    
DELETE FROM retail_sales
WHERE 
    transaction_id IS NULL
    OR
    sale_date IS NULL
    OR 
    sale_time IS NULL
    OR
    gender IS NULL
    OR
    category IS NULL
    OR
    quantity IS NULL
    OR
    cogs IS NULL
    OR
    total_sale IS NULL;
```
    
## Data Exploration

# How many sales we have?
```sql
SELECT COUNT(*) as total_sale FROM retail_sales
```
# How many uniuque customers we have ?
```sql
SELECT COUNT(DISTINCT customer_id) as total_sale FROM retail_sales;

SELECT DISTINCT category FROM retail_sales
```

## Data Analysis 

# Q.1 Write an SQL query to retrieve all columns for sales made on '2022-11-05
```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```

# Q.2 Write an SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 4 in the month of Nov-2022
```sql
    
SELECT * FROM
(SELECT * , 
	month(sale_date),
    year(sale_date)
FROM retail_sales
WHERE category = 'Clothing') as t1
WHERE month(sale_date)='11'
	AND  year(sale_date)='2022'
```





# Q.3 Write an SQL query to calculate the total sales (total_sale) for each category.
```sql
SELECT 
    category,
    SUM(total_sale) as net_sale,
    COUNT(*) as total_orders
FROM retail_sales
GROUP BY 1
```
#Q.4 Write an SQL query to find the average age of customers who purchased items from the 'Beauty' category.
```sql
SELECT
    ROUND(AVG(age), 2) as avg_age
FROM retail_sales
WHERE category = 'Beauty'
```

# Q.5 Write an SQL query to find all transactions where the total_sale is greater than 1000.
```sql
SELECT * FROM retail_sales
WHERE total_sale > 1000
```

# Q.6 Write an SQL query to find the total number of transactions (transaction_id) made by each gender in each category.
```sql
SELECT 
    category,
    gender,
    COUNT(*) as total_trans
FROM retail_sales
GROUP 
    BY 
    category,
    gender
ORDER BY 1
```

# Q.7 Write an SQL query to calculate the average sale for each month. Find out best selling month in each year
```sql
SELECT 
       year,
       month,
    avg_sale
FROM 
(    
SELECT 
    YEAR(sale_date) as year,
    MONTH(sale_date) as month,
    AVG(total_sale) as avg_sale,
    RANK() OVER(PARTITION BY YEAR(sale_date) ORDER BY AVG(total_sale) DESC) as month_rank
FROM retail_sales
GROUP BY 1, 2
) as t1
WHERE month_rank = 1
```

# Q.8 Write an SQL query to find the top 5 customers based on the highest total sales 
```sql
SELECT 
    customer_id,
    SUM(total_sale) as total_sales
FROM retail_sales
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5
```
# Q.9 Write an SQL query to find the number of unique customers who purchased items from each category.
```sql
SELECT 
    category,    
    COUNT(DISTINCT customer_id) as cnt_unique_cs
FROM retail_sales
GROUP BY category
```


# Q.10 Write an SQL query to create each shift and number of orders (Example Morning <12, Afternoon Between 12 & 17, Evening >17)
```sql
WITH hourly_sale
AS
(
SELECT *,
    CASE
        WHEN HOUR(sale_time) < 12 THEN 'Morning'
        WHEN HOUR(sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
        ELSE 'Evening'
    END as shift
FROM retail_sales
)
SELECT 
    shift,
    COUNT(*) as total_orders    
FROM hourly_sale
GROUP BY shift
```
# Q.11 Write an SQL query to find the youngest and oldest customer (based on age) who purchased items from the Electronics category.
```sql
SELECT 	category,
		MIN(age),
        Max(age)
FROM retail_sales
WHERE category = 'Electronics';
```
# Q.12 Write an SQL query to retrieve all transactions where the price per unit is greater than the average price of all items sold.
```sql	
SELECT *
FROM retail_sales
WHERE price_per_unit >(
		SELECT AVG(price_per_unit)
        FROM retail_sales);
 ```       
        
# Q.13 Write an SQL query to calculate the cumulative total sales by category over time
```sql
SELECT 
    category,
    sale_date,
    SUM(total_sale) OVER (PARTITION BY category ORDER BY sale_date) AS cumulative_total_sales
FROM 
    retail_sales 
ORDER BY 
    category, sale_date;
```    
# Q.14 Write a SQL query to identify customers who made more than 5 transactions in the month of December 2022.
```sql
SELECT 
   customer_id,
   COUNT(customer_id)AS total_transactions
FROM retail_sales
WHERE sale_date BETWEEN '2022-12-01' AND '2022-12-31'    
GROUP BY customer_id
HAVING COUNT(customer_id) > 2
```
# Q.15 Write a SQL query to calculate the total sales contribution for each gender (male and female) as a percentage of the overall sales.
```sql
SELECT 
    gender,
    SUM(total_sale) AS total_sales,
    ROUND((SUM(total_sale) / (SELECT SUM(total_sale) FROM retail_sales)) * 100, 2) AS sales_percentage
FROM 
    retail_sales 
GROUP BY 
    gender;
```    
# Q.16 Write a SQL query to rank each transaction within its category based on total_sale in descending order. Display the top 2 transactions for each category
```
WITH RankedTransactions AS (
    SELECT 
        category,
        transaction_id,
        total_sale,
         DENSE_RANK() OVER (PARTITION BY category ORDER BY total_sale DESC) AS rank_within_category
    FROM 
        retail_sales  
)
SELECT 
    category,
    transaction_id,
    total_sale,
    rank_within_category
FROM 
    RankedTransactions
WHERE 
    rank_within_category <= 2
ORDER BY 
    category, rank_within_category;
```    
    
## End of project

