Project Title: Retail Sales Analysis

Database:sql_project_p2

This project is designed to demonstrate SQL skills and techniques typically used by data analysts to explore, clean, and analyze retail sales data. The project involves setting up a retail sales database, Performing Exploratory Data analysis (EDA), and answering specific business questions through SQL queries.To complement the SQL analysis, an interactive Tableau dashboard was created to visualize key insights from the retail sales data.

This unguided project demonstrates my ability to clean, manipulate, and analyze data using SQL, and to present key business insights through interactive dashboards in Tableau. It simulates the kind of data analysis a retail business would use to understand performance, optimize sales, and improve customer targeting.

Objectives

Set up a retail sales database: Create and populate a retail sales database with the provided sales data.
Data Cleaning: 
Identify and remove any records with missing or null values.
Exploratory Data Analysis (EDA): Perform basic exploratory data analysis to understand the dataset.
Business Analysis: Use SQL to answer specific business questions and derive insights from the sales data.

Project Structure
Database SetupDatabase Creation: The project starts by creating a database named sql_project_p2.
Table Creation: 
A table named retail_sales_data is created to store the sales data. The table structure includes columns for transaction ID, sale date, sale time, customer ID, gender, age, product category, quantity sold, price per unit, cost of goods sold (COGS), and total sale amount.


CREATE DATABASE sql_project_p2;


-- Create TABLE

CREATE TABLE retail_sales_data
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
Data Exploration & Cleaning

Record Count: Determine the total number of records in the dataset.
Customer Count: Find out how many unique customers are in the dataset.
Category Count: Identify all unique product categories in the dataset.
Null Value Check: Check for any null values in the dataset and delete records with missing data.            

SELECT * FROM retail_sales_data LIMIT 10;
SELECT COUNT(*) FROM retail_sales_data;

-- Data Cleaning

SELECT * FROM retail_sales_data
WHERE sale_date IS NULL
SELECT * FROM retail_sales_data
WHERE sale_time IS NULL
SELECT * FROM retail_sales_data
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
    
-- 
DELETE FROM retail_sales_data
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
    
-- Data Exploration

-- How many sales we have?
SELECT COUNT(*) as total_sale FROM retail_sales_data ;

-- How many uniuque customers we have ?

SELECT COUNT(DISTINCT customer_id) as total_sale FROM retail_sales_data;

SELECT DISTINCT category FROM retail_sales_data;


-- Data Analysis & Business Key Problems & Answers

 To retrieve all columns for sales made on '2022-11-05

SELECT *
FROM retail_sales_data
WHERE sale_date = '2022-11-05';

To retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 4 in the month of Nov-2022

SELECT * 
FROM retail_sales_data
WHERE category = 'Clothing' 
  AND DATE_FORMAT(sale_date, '%Y-%m') = '2022-11' 
  AND quantity >= 4 
LIMIT 0, 1000;

To calculate the total sales (total_sale) for each category.

SELECT 
    category,
    SUM(total_sale) as net_sale,
    COUNT(*) as total_orders
FROM retail_sales_data
GROUP BY 1

The average age of customers who purchased items from the 'Beauty' category.

SELECT
    ROUND(AVG(age), 2) as avg_age
FROM retail_sales_data
WHERE category = 'Beauty';


To find all transactions where the total_sale is greater than 1000.

SELECT * FROM retail_sales_data
WHERE total_sale > 1000


The total number of transactions (transaction_id) made by each gender in each category.

SELECT 
    category,
    gender,
    COUNT(*) as total_trans
FROM retail_sales_data
GROUP 
    BY 
    category,
    gender
ORDER BY 1

To calculate the average sale for each month. Find out best selling month in each year

SELECT 
       year,
       month,
    avg_sale
FROM 
(    
SELECT 
    EXTRACT(YEAR FROM sale_date) as year,
    EXTRACT(MONTH FROM sale_date) as month,
    AVG(total_sale) as avg_sale,
    RANK() OVER(PARTITION BY EXTRACT(YEAR FROM sale_date) ORDER BY AVG(total_sale) DESC) as rank
FROM retail_sales_data
GROUP BY 1, 2
) as t1
WHERE rank = 1
    
-- ORDER BY 1, 3 DESC

To find the top 5 customers based on the highest total sales 

SELECT 
    customer_id,
    SUM(total_sale) as total_sales
FROM retail_sales_data
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5

To find the number of unique customers who purchased items from each category.


SELECT 
    category,    
    COUNT(DISTINCT customer_id) as cnt_unique_cs
FROM retail_sales_data
GROUP BY category



create each shift and number of orders (Example Morning <12, Afternoon Between 12 & 17, Evening >17)

WITH hourly_sale
AS
(
SELECT *,
    CASE
        WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
        WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
        ELSE 'Evening'
    END as shift
FROM retail_sales_data
)
SELECT 
    shift,
    COUNT(*) as total_orders    
FROM hourly_sale
GROUP BY shift
--Total Sales Per Year
SELECT 
    YEAR(STR_TO_DATE(sale_date, '%m/%d/%Y')) AS sale_year,
    COUNT(*) AS total_transactions,
    SUM(total_sale) AS total_sales_amount
FROM 
    retail_sales_data
GROUP BY 
    YEAR(STR_TO_DATE(sale_date, '%m/%d/%Y'))
ORDER BY 
    sale_year;
    
    -- Total Sales Per Month and Year
    SELECT 
    YEAR(STR_TO_DATE(sale_date, '%m/%d/%Y')) AS sale_year,
    MONTH(STR_TO_DATE(sale_date, '%m/%d/%Y')) AS sale_month,
    COUNT(*) AS total_transactions,
    SUM(total_sale) AS total_sales_amount
FROM 
    retail_sales_data
GROUP BY 
    YEAR(STR_TO_DATE(sale_date, '%m/%d/%Y')), 
    MONTH(STR_TO_DATE(sale_date, '%m/%d/%Y'))
ORDER BY 
    sale_year, sale_month;

SELECT *
FROM retail_sales_data
WHERE YEAR(STR_TO_DATE(sale_date, '%m/%d/%Y')) = 2022;
-- SALES PER YEAR
SELECT 
    *, 
    YEAR(STR_TO_DATE(TRIM(sale_date), '%m/%d/%Y')) AS sale_year
FROM 
    retail_sales_data;
    
    SELECT 
    *, 
    YEAR(sale_date) AS sale_year
FROM 
    retail_sales_data;
    
Findings:-

Customer Demographics: The dataset includes customers from various age groups, with sales distributed across different categories such as Clothing and Beauty.
High-Value Transactions: Several transactions had a total sale amount greater than 1000, indicating premium purchases.
Sales Trends: Monthly analysis shows variations in sales, helping identify peak seasons.
Customer Insights: The analysis identifies the top-spending customers and the most popular product categories.

For visulaze the more clear insight of sales analysis same dataset used in Tableau Dashboard.
🔗 **[View the Dashboard on Tableau Public](https://public.tableau.com/views/Book1_17484418371370/Dashboard1?:language=en-US&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link)

Sales Overview in Dashboard : Monthly Sales Trend, Catogory Price Per Unit,Sales by Category.

The dashboard was created using the `.twbx` file available in this repository:

Reports

Sales Summary: A detailed report summarizing total sales, customer demographics, and category performance.
Trend Analysis: Insights into sales trends across different months and shifts.
Customer Insights: Reports on top customers and unique customer counts per category.

Conclusion

This project serves as a comprehensive introduction to SQL for data analysts, covering database setup, data cleaning, exploratory data analysis, and business-driven SQL queries. The findings from this project can help drive business decisions by understanding sales patterns, customer behavior, and product performance.

How to Use
Clone the Repository: Clone this project repository from GitHub.
Set Up the Database: Run the SQL scripts provided in the database_setup.sql file to create and populate the database.
Run the Queries: Use the SQL queries provided in the analysis_queries.sql file to perform your analysis.


This project is part of my portfolio, showcasing the SQL skills.

