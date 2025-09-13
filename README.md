# Retail Sales Analysis SQL Project

## Project Overview

**Project Title**: Retail Sales Analysis   
**Database**: `sql_project_p1`

This project is designed to demonstrate SQL skills and techniques to explore, clean, and analyze retail sales data. The project involves setting up a retail sales database, performing exploratory data analysis (EDA), and answering specific business questions through SQL queries.

## Objectives

1. **Set up a retail sales database**: Create and populate a retail sales database with the provided sales data.
2. **Data Cleaning**: Identify and remove any records with missing or null values.
3. **Exploratory Data Analysis (EDA)**: Perform basic exploratory data analysis to understand the dataset.
4. **Business Analysis**: Use SQL to answer specific business questions and derive insights from the sales data.

## Project Structure

### 1. Database Setup

- **Database Creation**: The project starts by creating a database named `p1_retail_db`.
- **Table Creation**: A table named `retail_sales` is created to store the sales data. The table structure includes columns for transaction ID, sale date, sale time, customer ID, gender, age, product category, quantity sold, price per unit, cost of goods sold (COGS), and total sale amount.

```sql
CREATE DATABASE sql_project_p1;

CREATE TABLE retail_sales
(
    transactions_id INT PRIMARY KEY,
    sale_date DATE,	
    sale_time TIME,
    customer_id INT,	
    gender VARCHAR(15),
    age INT,
    category VARCHAR(15),
    quantity INT,
    price_per_unit FLOAT,	
    cogs FLOAT,
    total_sale FLOAT
);
```

### 2. Data Exploration & Cleaning

- **Record Count**: Determine the total number of records in the dataset.
- **Customer Count**: Find out how many unique customers are in the dataset.
- **Category Count**: Identify all unique product categories in the dataset.
- **Null Value Check**: Check for any null values in the dataset and delete records with missing data.

```sql
SELECT COUNT(*) FROM retail_sales;
SELECT * FROM retail_sales
LIMIT 10;
SELECT COUNT(DISTINCT customer_id) FROM retail_sales;

SELECT * FROM retail_sales
WHERE
     transaction_id IS NULL OR  
    sale_date IS NULL OR sale_time IS NULL OR 
    gender IS NULL OR category IS NULL OR 
    quantity IS NULL OR cogs IS NULL OR total_sale IS NULL;

DELETE FROM retail_sales
WHERE 
     transaction_id IS NULL OR 
     sale_date IS NULL OR sale_time	IS NULL OR
	 gender IS NULL OR category IS NULL OR
	 quantity IS NULL OR cogs IS NULL OR
	 total_sale IS NULL;
```

### 3. Data Analysis & Findings

The following SQL queries were developed to answer specific business questions:

1. **Write a SQL query to retrieve all columns for sales made on '2022-11-05'**:
```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```
**Output** 
<img width="1565" height="395" alt="1a" src="https://github.com/user-attachments/assets/e9467988-978b-42fb-86c8-b7bd6bce35d9" />


2. **Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 4 in the month of Nov-2022**:
```sql
SELECT *
FROM retail_sales
WHERE category = 'Clothing'
AND
TO_CHAR (sale_date, 'YYYY-MM') = '2022-11'
AND 
quantity >=4;
```
**Output**
<img width="1566" height="361" alt="2" src="https://github.com/user-attachments/assets/a46efdf2-7573-469f-9aad-9dfa61b6fb68" />

3. **Write a SQL query to calculate the total sales (total_sale) for each category.**:
```sql
SELECT 
category,
SUM (total_sale) as net_sale,
COUNT (*) as total_orders
FROM retail_sales
GROUP BY 1;
```
**Output**
<img width="458" height="142" alt="3" src="https://github.com/user-attachments/assets/211efd63-6fc9-4ccc-af42-94f6c3ecd3ec" />  



4. **Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.**:
```sql
SELECT 
ROUND (AVG (age),2) as avg_age
FROM retail_sales
WHERE category = 'Beauty';
```
**Output**
<img width="92" height="82" alt="4" src="https://github.com/user-attachments/assets/c22b2a21-cfce-45c8-a7b0-9f9de4acff04" />


5. **Write a SQL query to find all transactions where the total_sale is greater than 1000.**:
```sql
SELECT *
FROM retail_sales
WHERE total_sale> 1000;
```
**Output**
<img width="1540" height="391" alt="5a" src="https://github.com/user-attachments/assets/567661b0-7d26-4d86-8e01-ed15bb38815e" />


6. **Write a SQL query to find the total number of transactions (transaction_id) made by each gender in each category.**:
```sql
SELECT 
category,
gender,
COUNT (transaction_id) as transactions
FROM retail_sales
GROUP BY 1,2
ORDER BY 1;
```
**Output**
<img width="498" height="233" alt="6" src="https://github.com/user-attachments/assets/7517080b-c624-40b1-b1b5-5cebcdc51deb" />


7. **Write a SQL query to calculate the average sale for each month. Find out best selling month in each year**:
```sql
SELECT 
      year,
      month,
      avg_sale
FROM
(
SELECT 
     EXTRACT (YEAR from sale_date) as year,
	 EXTRACT (MONTH from sale_date ) as month,
	 AVG (total_sale) as avg_sale,
	 RANK() OVER(PARTITION BY EXTRACT(YEAR FROM sale_date) ORDER BY AVG(total_sale) DESC) as rank
FROM retail_sales
GROUP BY 1, 2
) as t1
WHERE rank = 1;
```
**Output**
<img width="351" height="107" alt="7" src="https://github.com/user-attachments/assets/a671cc11-52fd-4707-a0b9-0245a2b5b202" />


8. **Write a SQL query to find the top 5 customers based on the highest total sales.**:
```sql
SELECT 
 customer_id,
 SUM (total_sale) as total_sales
 FROM retail_sales
 GROUP BY 1
 ORDER BY 2 DESC
 LIMIT 5;
```
**Output**
<img width="277" height="207" alt="8" src="https://github.com/user-attachments/assets/8437b0ed-7c45-49b8-b05a-6ebe65a927aa" />


9. **Write a SQL query to find the number of unique customers who purchased items from each category.**:
```sql
SELECT 
category,
COUNT (DISTINCT customer_id) as cnt_unique
FROM retail_sales
GROUP BY 1;
```
**Output**
<img width="302" height="146" alt="9" src="https://github.com/user-attachments/assets/923247ac-42fc-4a04-b630-265e1d1198bf" />


10. **Write a SQL query to create each shift and number of orders (Example Morning <12, Afternoon Between 12 & 17, Evening >17)**:
```sql
WITH hourly_sale
AS
(
SELECT *,
    CASE
        WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
        WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
        ELSE 'Evening'
    END as shift
FROM retail_sales
)
SELECT 
    shift,
    COUNT(*) as total_orders    
FROM hourly_sale
GROUP BY shift;
```
**Output**
<img width="210" height="143" alt="10" src="https://github.com/user-attachments/assets/8316543e-9533-4477-b9c7-b7af826997a3" />


## Findings

- **Customer Demographics**: The dataset includes customers from various age groups, with sales distributed across different categories such as Clothing and Beauty.
- **High-Value Transactions**: Several transactions had a total sale amount greater than 1000, indicating premium purchases.
- **Sales Trends**: Monthly analysis shows variations in sales, helping identify peak seasons.
- **Customer Insights**: The analysis identifies the top-spending customers and the most popular product categories.

## Reports

- **Sales Summary**: A detailed report summarizing total sales, customer demographics, and category performance.
- **Trend Analysis**: Insights into sales trends across different months and shifts.
- **Customer Insights**: Reports on top customers and unique customer counts per category.

## Conclusion

This project includes database setup, data cleaning, exploratory data analysis, and business-driven SQL queries. The findings from this project can help drive business decisions by understanding sales patterns, customer behavior, and product performance.

## Author - Anshika Srivastava


