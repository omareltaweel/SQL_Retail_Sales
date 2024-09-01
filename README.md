# SQL_Retail_Sales-
-- SQL Retail Sales Analysis - P1
CREATE DATABASE sql_project_p2;


-- Create TABLE
DROP TABLE IF EXISTS retail_sales;
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

SELECT * FROM [sql_project_p2].[dbo].[Retail_Sales _Analysis]



    

SELECT 
    COUNT(*) 
FROM  [sql_project_p2].[dbo].[Retail_Sales _Analysis]

-- Data Cleaning
SELECT * FROM [sql_project_p2].[dbo].[Retail_Sales _Analysis]
WHERE transactions_id IS NULL

SELECT * FROM [sql_project_p2].[dbo].[Retail_Sales _Analysis]
WHERE sale_date IS NULL

SELECT * FROM [sql_project_p2].[dbo].[Retail_Sales _Analysis]
WHERE sale_time IS NULL

SELECT * FROM [sql_project_p2].[dbo].[Retail_Sales _Analysis]
WHERE 
    transactions_id IS NULL
    OR
    sale_date IS NULL
    OR 
    sale_time IS NULL
    OR
    gender IS NULL
    OR
    category IS NULL
    OR
    quantiy IS NULL
    OR
    cogs IS NULL
    OR
    total_sale IS NULL;
    
-- 
DELETE FROM [sql_project_p2].[dbo].[Retail_Sales _Analysis]
WHERE 
    transactions_id IS NULL
    OR
    sale_date IS NULL
    OR 
    sale_time IS NULL
    OR
    gender IS NULL
    OR
    category IS NULL
    OR
    quantiy IS NULL
    OR
    cogs IS NULL
    OR
    total_sale IS NULL;
    
-- Data Exploration

-- How many sales we have?
SELECT COUNT(*) as total_sale FROM [sql_project_p2].[dbo].[Retail_Sales _Analysis]

-- How many uniuque customers we have ?

SELECT COUNT(DISTINCT customer_id) as total_sale FROM [sql_project_p2].[dbo].[Retail_Sales _Analysis]



SELECT DISTINCT category FROM [sql_project_p2].[dbo].[Retail_Sales _Analysis]


-- Data Analysis & Business Key Problems & Answers

-- My Analysis & Findings
-- Q.1 Write a SQL query to retrieve all columns for sales made on '2022-11-05
-- Q.2 Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 10 in the month of Nov-2022
-- Q.3 Write a SQL query to calculate the total sales (total_sale) for each category.
-- Q.4 Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.
-- Q.5 Write a SQL query to find all transactions where the total_sale is greater than 1000.
-- Q.6 Write a SQL query to find the total number of transactions (transaction_id) made by each gender in each category.
-- Q.7 Write a SQL query to calculate the average sale for each month. Find out best selling month in each year
-- Q.8 Write a SQL query to find the top 5 customers based on the highest total sales 
-- Q.9 Write a SQL query to find the number of unique customers who purchased items from each category.
-- Q.10 Write a SQL query to create each shift and number of orders (Example Morning <=12, Afternoon Between 12 & 17, Evening >17)



 -- Q.1 Write a SQL query to retrieve all columns for sales made on '2022-11-05

SELECT *
FROM [sql_project_p2].[dbo].[Retail_Sales _Analysis]
WHERE sale_date = '2022-11-05';


-- Q.2 Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 4 in the month of Nov-2022

SELECT 
  *
FROM [sql_project_p2].[dbo].[Retail_Sales _Analysis]
WHERE 
    category = 'Clothing'
    AND 
	FORMAT(sale_date, 'yyyy-MM') = '2022-11'
	AND
    quantiy >= 4


-- Q.3 Write a SQL query to calculate the total sales (total_sale) for each category.

SELECT 
    category,
    SUM(total_sale) as net_sale,
    COUNT(*) as total_orders
FROM [sql_project_p2].[dbo].[Retail_Sales _Analysis]
GROUP BY category
order by category desc 

-- Q.4 Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.

SELECT
    ROUND(AVG(age), 2) as avg_age
FROM [sql_project_p2].[dbo].[Retail_Sales _Analysis]
WHERE category = 'Beauty'


-- Q.5 Write a SQL query to find all transactions where the total_sale is greater than 1000.

SELECT * FROM  [sql_project_p2].[dbo].[Retail_Sales _Analysis]
WHERE total_sale > 1000


-- Q.6 Write a SQL query to find the total number of transactions (transaction_id) made by each gender in each category.

SELECT 
    category,
    gender,
    COUNT(*) as total_trans
FROM  [sql_project_p2].[dbo].[Retail_Sales _Analysis]
GROUP 
    BY 
    category,
    gender
ORDER BY category, gender


-- Q.7 Write a SQL query to calculate the average sale for each month. Find out best selling month in each year

SELECT 
    year,
    month,
    avg_sale
FROM 
(    
    SELECT 
        YEAR(sale_date) AS year,
        MONTH(sale_date) AS month,
        AVG(total_sale) AS avg_sale,
        RANK() OVER(PARTITION BY YEAR(sale_date) ORDER BY AVG(total_sale) DESC) AS rank
    FROM [sql_project_p2].[dbo].[Retail_Sales _Analysis]
    GROUP BY YEAR(sale_date ), MONTH(sale_date) 
) AS t1
WHERE rank = 1
ORDER BY year, month;

    
-- ORDER BY 1, 3 DESC

-- Q.8 Write a SQL query to find the top 5 customers based on the highest total sales 

SELECT 
    customer_id,
    SUM(total_sale) as total_sales
FROM [sql_project_p2].[dbo].[Retail_Sales _Analysis]
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5


SELECT 
    TOP 5 
    customer_id, 
    SUM(total_sale) AS total_sales
FROM 
    [sql_project_p2].[dbo].[Retail_Sales _Analysis]
GROUP BY 
    customer_id
ORDER BY 
    total_sales DESC , customer_id ;

-- Q.9 Write a SQL query to find the number of unique customers who purchased items from each category.


SELECT 
    category,    
    COUNT(DISTINCT customer_id) as cnt_unique_cs
FROM [sql_project_p2].[dbo].[Retail_Sales _Analysis]
GROUP BY category



-- Q.10 Write a SQL query to create each shift and number of orders (Example Morning <12, Afternoon Between 12 & 17, Evening >17)

WITH hourly_sale AS
(
    SELECT *,
        CASE
            WHEN DATEPART(HOUR, sale_time) < 12 THEN 'Morning'
            WHEN DATEPART(HOUR, sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
            ELSE 'Evening'
        END AS shift
    FROM [sql_project_p2].[dbo].[Retail_Sales _Analysis]
)
SELECT 
    shift,
    COUNT(*) AS total_orders    
FROM hourly_sale
GROUP BY shift;


-- End of project

