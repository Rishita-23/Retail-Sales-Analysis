# Retail Sales Analysis SQL Project

## Project Overview

**Project Title**: Retail Sales Analysis  
**Database**: `p1_retail_db`

This project is designed to demonstrate SQL skills and techniques typically used by data analysts to explore, clean, and analyze retail sales data. The project involves setting up a retail sales database, performing exploratory data analysis (EDA), and answering specific business questions through SQL queries. This project is ideal for those who are starting their journey in data analysis and want to build a solid foundation in SQL.

## Objectives

- ✨ **Set up a retail sales database**: Create and populate a retail sales database with the provided sales data.
- ✉️ **Data Cleaning**: Identify and remove any records with missing or null values.
- 🔢 **Exploratory Data Analysis (EDA)**: Perform basic exploratory data analysis to understand the dataset.
- 📊 **Business Analysis**: Use SQL to answer specific business questions and derive insights from the sales data.

## Project Structure

### 1. Database Setup

```sql
CREATE DATABASE p1_retail_db;

CREATE TABLE retail_sales
(
    transactions_id INT PRIMARY KEY,
    sale_date DATE,	
    sale_time TIME,
    customer_id INT,	
    gender VARCHAR(10),
    age INT,
    category VARCHAR(35),
    quantity INT,
    price_per_unit FLOAT,	
    cogs FLOAT,
    total_sale FLOAT
);
```

### 2. Data Exploration & Cleaning

```sql
-- Record Count
SELECT COUNT(*) FROM retail_sales;

-- Unique Customers
SELECT COUNT(DISTINCT customer_id) FROM retail_sales;

-- Unique Categories
SELECT DISTINCT category FROM retail_sales;

-- Check Null Values
SELECT * FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;

-- Remove Null Records
DELETE FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;
```

### 3. Data Analysis & Findings

**Q1: Sales made on '2022-11-05'**
```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```

**Q2: Clothing sales > 4 in Nov 2022**
```sql
SELECT *
FROM retail_sales
WHERE category = 'Clothing'
  AND TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
  AND quantity >= 4;
```

**Q3: Total sales by category**
```sql
SELECT category, SUM(total_sale) as net_sale, COUNT(*) as total_orders
FROM retail_sales
GROUP BY category;
```

**Q4: Average age of Beauty buyers**
```sql
SELECT ROUND(AVG(age), 2) as avg_age
FROM retail_sales
WHERE category = 'Beauty';
```

**Q5: Transactions > 1000**
```sql
SELECT *
FROM retail_sales
WHERE total_sale > 1000;
```

**Q6: Transactions by gender & category**
```sql
SELECT category, gender, COUNT(*) as total_trans
FROM retail_sales
GROUP BY category, gender
ORDER BY category;
```

**Q7: Best-selling month per year**
```sql
SELECT year, month, avg_sale
FROM (
  SELECT EXTRACT(YEAR FROM sale_date) as year,
         EXTRACT(MONTH FROM sale_date) as month,
         AVG(total_sale) as avg_sale,
         RANK() OVER(PARTITION BY EXTRACT(YEAR FROM sale_date) 
                    ORDER BY AVG(total_sale) DESC) as rank
  FROM retail_sales
  GROUP BY year, month
) as t1
WHERE rank = 1;
```

**Q8: Top 5 customers by total sales**
```sql
SELECT customer_id, SUM(total_sale) as total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sales DESC
LIMIT 5;
```

**Q9: Unique customers per category**
```sql
SELECT category, COUNT(DISTINCT customer_id) as cnt_unique_cs
FROM retail_sales
GROUP BY category;
```

**Q10: Sales by shift (Morning, Afternoon, Evening)**
```sql
WITH hourly_sale AS (
  SELECT *,
    CASE
      WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
      WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
      ELSE 'Evening'
    END as shift
  FROM retail_sales
)
SELECT shift, COUNT(*) as total_orders
FROM hourly_sale
GROUP BY shift;
```

## Findings

- 🥇 **High-Value Transactions**: Transactions >1000 reflect premium purchases.
- 🌍 **Diverse Demographics**: Customers span various age groups and genders.
- 🔢 **Popular Categories**: Clothing and Beauty lead in quantity and unique customers.
- 📅 **Sales Trends**: Monthly insights help identify peak business periods.

## Reports

- **Sales Summary**: Overview of revenue and product category contributions.
- **Trend Analysis**: Sales performance by month and time shifts.
- **Customer Insights**: High-spenders, loyal customers, and demographics.

## Conclusion

This project showcases SQL’s power in real-world retail data analysis, from setup and cleaning to advanced queries for actionable insights. Perfect for budding analysts!
