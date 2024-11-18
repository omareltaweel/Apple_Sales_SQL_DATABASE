# Apple_Sales_SQL_DATABASE
"Apple Sales Data Analysis with SQL: Build a database named 'Apple,' create and structure the required tables, and load data from CSV files into these tables. Utilize SQL queries to address and answer 20 business-focused questions for comprehensive data analysis."

-- ============================================
## SECTION 1: Database and Table Creation
-- ============================================

```sql
Create the Apple database
CREATE DATABASE Apple;
USE Apple;
```
```sql
Create 'stores' table
CREATE TABLE stores (
    store_id VARCHAR(5) PRIMARY KEY,
    store_name VARCHAR(30),
    city VARCHAR(25),
    country VARCHAR(25)
    );
```
```sql
 Create 'category' table
CREATE TABLE category (
    category_id VARCHAR(10) PRIMARY KEY,
    category_name VARCHAR(20)
);
```
```sql
-- Create 'products' table
CREATE TABLE products (
    product_id VARCHAR(10) PRIMARY KEY,
    product_name VARCHAR(35),
    category_id VARCHAR(10),
    launch_date DATE,
    price FLOAT,
    CONSTRAINT FK_category FOREIGN KEY (category_id) REFERENCES category (category_id)
);
```

```sql

-- Create 'sales' table
CREATE TABLE sales (
    sale_id VARCHAR(15) PRIMARY KEY,
    sale_date DATE,
    store_id VARCHAR(5),
    product_id VARCHAR(10),
    quantity INT,
    CONSTRAINT FK_store FOREIGN KEY (store_id) REFERENCES stores (store_id),
    CONSTRAINT FK_product FOREIGN KEY (product_id) REFERENCES products (product_id)
);
```
```sql
-- Create 'warranty' table
CREATE TABLE warranty (
    claim_id VARCHAR(10) PRIMARY KEY,
    claim_date DATE,
    sale_id VARCHAR(15),
    repair_status VARCHAR(15),
    CONSTRAINT FK_orders FOREIGN KEY (sale_id) REFERENCES sales (sale_id)
);
```
-- ============================================
## SECTION 2: Data Import
-- ============================================
```sql
-- Bulk insert data into 'stores' table
BULK INSERT stores
FROM 'C:\Users\user\Desktop\DATA\03.Projects\#21Apple\apple_datasets\stores.csv'
WITH (
    FORMAT = 'CSV',
    FIRSTROW = 2,
    FIELDTERMINATOR = ',',
    ROWTERMINATOR = '\n',
    TABLOCK
);
```

```sql
-- Bulk insert data into 'category' table
BULK INSERT category
FROM 'C:\Users\user\Desktop\DATA\03.Projects\#21Apple\apple_datasets\category.csv'
WITH (
    FORMAT = 'CSV',
    FIRSTROW = 2,
    FIELDTERMINATOR = ',',
    ROWTERMINATOR = '\n',
    TABLOCK
);

```
```sql
-- Bulk insert data into 'products' table
BULK INSERT products
FROM 'C:\Users\user\Desktop\DATA\03.Projects\#21Apple\apple_datasets\products.csv'
WITH (
    FORMAT = 'CSV',
    FIRSTROW = 2,
    FIELDTERMINATOR = ',',
    ROWTERMINATOR = '\n',
    TABLOCK
);
```

```sql
-- Bulk insert data into 'sales' table
BULK INSERT sales
FROM 'C:\Users\user\Desktop\DATA\03.Projects\#21Apple\apple_datasets\sales.csv'
WITH (
    FORMAT = 'CSV',
    FIRSTROW = 2,
    FIELDTERMINATOR = ',',
    ROWTERMINATOR = '\n',
    TABLOCK
);
```
```sql
-- Bulk insert data into 'warranty' table
BULK INSERT warranty
FROM 'C:\Users\user\Desktop\DATA\03.Projects\#21Apple\apple_datasets\warranty.csv'
WITH (
    FORMAT = 'CSV',
    FIRSTROW = 2,
    FIELDTERMINATOR = ',',
    ROWTERMINATOR = '\n',
    TABLOCK
);

```

-- ============================================
## SECTION 3: ER Diagram
-- ============================================
![ER Digram ](https://github.com/user-attachments/assets/ee9e8a7a-f782-4e10-8df2-3dc79793b166)

-- ============================================
## SECTION 4: Analytical Queries
-- ============================================

write all for readme file and rewrite as frist one  

```sql
-- 1. Find the number of stores in each country.

SELECT country, COUNT(store_id) as total_stores
FROM stores
GROUP BY country
ORDER BY total_stores DESC

```
```sql
-- Q.2 Calculate the total number of units sold by each store.
SELECT count(product_id) as number_of_units , s.store_id ,st.store_name
FROM sales as s
join stores as st 
on s.store_id = st.store_id 
group by s.store_id ,st.store_name 
order by s.store_id ;
```

```sql
-- Q.3 Identify how many sales occurred in December 2023.
SELECT count(sale_id) as Sales2023
FROM sales
where format(sale_date,'MM-yyyy') = '12-2023';
```

```sql
-- Q.4 Determine how many stores have never had a warranty claim filed.
SELECT COUNT(*) 
FROM stores
WHERE store_id NOT IN (SELECT DISTINCT store_id 
FROM sales as s
Right JOIN warranty as w
ON s.sale_id = w.sale_id);
```

```sql
-- Q.5 Calculate the percentage of warranty claims marked as "Warranty Void".
SELECT 
ROUND(CAST(COUNT(claim_id) AS FLOAT) / (SELECT COUNT(*) FROM warranty) * 100, 2 ) AS warranty_void_percentage
FROM warranty
WHERE repair_status = 'Warranty Void';
```

```sql
-- Q.6 Identify which store had the highest total units sold in the last year.
SELECT  TOP 1 s.store_id, sum(s.quantity),st.store_name 
FROM sales s
left join stores st
on s.store_id = st.store_id
WHERE sale_date >= DATEADD(year, -1, GETDATE())
group by s.store_id , st.store_name 
order by s.store_id desc 
```
```sql
-- Q.7 Count the number of unique products sold in the last year.
SELECT 
COUNT(DISTINCT product_id)
FROM sales
WHERE sale_date >=  DATEADD(year, -1, GETDATE())
```
```sql
-- Q.8 Find the average price of products in each category.
SELECT 
    p.category_id,
    c.category_name,
    AVG(p.price) AS avg_price
FROM products AS p
JOIN category AS c
ON p.category_id = c.category_id
GROUP BY p.category_id, c.category_name
ORDER BY avg_price DESC;
```

```sql
-- Q.9 How many warranty claims were filed in 2020?
SELECT COUNT(claim_id) as warranty_claims_filed_2020
FROM warranty
WHERE YEAR(claim_date) = 2020;
```
```sql
-- Q.10 For each store, identify the best-selling day based on highest quantity sold.

SELECT *
FROM (SELECT store_id, DATENAME(WEEKDAY, sale_date) AS day_name, sale_date , SUM(quantity) AS total_unit_sold,
        RANK() OVER (PARTITION BY store_id ORDER BY SUM(quantity) DESC) AS rank
    FROM sales
    GROUP BY store_id, DATENAME(WEEKDAY, sale_date),sale_date
) AS t1
WHERE rank = 1;
```
```sql
-- Q.11 Identify the least selling product in each country for each year based on total units sold.
WITH product_rank AS(
SELECT st.country , p.product_name , SUM(s.quantity) as total_qty_sold,RANK() OVER(PARTITION BY st.country ORDER BY SUM(s.quantity)) as rank
FROM sales as s
JOIN 
stores as st
ON s.store_id = st.store_id
JOIN
products as p
ON s.product_id = p.product_id
GROUP BY st.country, p.product_name
)
SELECT * 
FROM product_rank
WHERE rank = 1
```

```sql
Q.12 Calculate how many warranty claims were filed within 180 days of a product sale.
SELECT 
    COUNT(*)
FROM warranty w
LEFT JOIN sales s
    ON s.sale_id = w.sale_id
WHERE 
    DATEDIFF(DAY, s.sale_date, w.claim_date) <= 180;
```
```sql
--Q.13  Determine how many warranty claims were filed for products launched in the last two years.
SELECT 
	p.product_name,
	COUNT(w.claim_id) as no_claim,
	COUNT(s.sale_id)
FROM warranty as w
RIGHT JOIN
sales as s 
ON s.sale_id = w.sale_id
JOIN products as p
ON p.product_id = s.product_id
WHERE  p.launch_date >= DATEADD(YEAR, -2, GETDATE())
GROUP BY p.product_name
HAVING COUNT(w.claim_id) > 0
```

```sql
-- Q.14 List the months in the last three years where sales exceeded 5,000 units in the USA.
SELECT 
    FORMAT(s.sale_date, 'MM-yyyy') AS month,
    SUM(s.quantity) AS total_unit_sold
FROM sales AS s
JOIN stores AS st ON s.store_id = st.store_id
WHERE 
    st.country = 'USA'
    AND s.sale_date >= DATEADD(YEAR, -3, GETDATE())
GROUP BY FORMAT(s.sale_date, 'MM-yyyy')
HAVING SUM(s.quantity) > 5000;
```
```sql
-- Q.15 Identify the product category with the most warranty claims filed in the last two years.

SELECT 
	c.category_name,
	COUNT(w.claim_id) as total_claims
FROM warranty as w
LEFT JOIN sales as s
ON w.sale_id = s.sale_id
JOIN products as p
ON p.product_id = s.product_id
JOIN category as c
ON c.category_id = p.category_id
WHERE w.claim_date >= DATEADD(YEAR, -2, GETDATE())
GROUP BY c.category_name
```


```sql
Q.16 Determine the percentage chance of receiving warranty claims after each purchase for each country!
SELECT 
	country,
	total_unit_sold,
	total_claim,
	COALESCE(CAST(total_claim AS FLOAT) / CAST(total_unit_sold AS FLOAT) * 100, 0) AS risk

FROM(SELECT 
	st.country,
	SUM(s.quantity) as total_unit_sold,
	COUNT(w.claim_id) as total_claim
FROM sales as s
JOIN stores as st
ON s.store_id = st.store_id
LEFT JOIN 
warranty as w
ON w.sale_id = s.sale_id
GROUP BY st.country ) t1
ORDER BY country, total_unit_sold, total_claim DESC
```

```sql
Q.17 Analyze the year-by-year growth ratio for each store.
-- each store and their yearly sale 
WITH yearly_sales AS (
    SELECT s.store_id, st.store_name, YEAR(s.sale_date) AS year, SUM(s.quantity * p.price) AS total_sale
    FROM sales AS s
    JOIN products AS p ON s.product_id = p.product_id
    JOIN stores AS st ON st.store_id = s.store_id
    GROUP BY s.store_id, st.store_name, YEAR(s.sale_date)
),growth_ratio AS (
    SELECT 
        store_name,
        year,
       LAG(total_sale, 1) OVER(PARTITION BY store_name ORDER BY year) AS last_year_sale,
        total_sale AS current_year_sale
    FROM yearly_sales)
SELECT 
    store_name,
    year,
    last_year_sale,
    current_year_sale,
    CASE 
        WHEN last_year_sale > 0 THEN (current_year_sale - last_year_sale) / last_year_sale * 100
        ELSE 0
    END AS growth_percentage
FROM growth_ratio
ORDER BY store_name, year;
```


```sql
Q.18 Calculate the correlation between product price and warranty claims for 
-- products sold in the last five years, segmented by price range.

SELECT 
	CASE
		WHEN p.price < 500 THEN 'Less Expenses Product'
		WHEN p.price BETWEEN 500 AND 1000 THEN 'Mid Range Product'
		ELSE 'Expensive Product'
	END as price_segment,
	COUNT(w.claim_id) as total_Claim
FROM warranty as w
LEFT JOIN
sales as s
ON w.sale_id = s.sale_id
JOIN 
products as p
ON p.product_id = s.product_id
WHERE claim_date >= DATEADD(YEAR, -2, GETDATE())
GROUP BY CASE
		WHEN p.price < 500 THEN 'Less Expenses Product'
		WHEN p.price BETWEEN 500 AND 1000 THEN 'Mid Range Product'
		ELSE 'Expensive Product'
	END;
```

```sql
-- Q.19 Identify the store with the highest percentage of "Paid Repaired" claims relative to total claims filed
WITH paid_repair AS 
(SELECT s.store_id, COUNT(w.claim_id) as paid_repaired
FROM sales as s
RIGHT JOIN warranty as w
ON w.sale_id = s.sale_id
WHERE w.repair_status = 'Paid Repaired'
GROUP BY s.store_id)
,total_repaired AS
(SELECT s.store_id,COUNT(w.claim_id) as total_repaired
FROM sales as s
RIGHT JOIN warranty as w
ON w.sale_id = s.sale_id
GROUP BY s.store_id)
SELECT tr.store_id,st.store_name,pr.paid_repaired,tr.total_repaired,
	ROUND(CAST(pr.paid_repaired AS FLOAT) / CAST(tr.total_repaired AS FLOAT) * 100, 2) AS percentage_paid_repaired
FROM paid_repair as pr
JOIN total_repaired as tr
ON pr.store_id = tr.store_id
JOIN stores as st
ON tr.store_id = st.store_id
```


```sql
-- Q.20 Write a query to calculate the monthly running total of sales for each store

WITH monthly_sales AS
(SELECT 
        store_id,YEAR(sale_date) AS year,MONTH(sale_date) AS month,SUM(p.price * s.quantity) AS total_revenue
    FROM sales AS s
    JOIN products AS p ON s.product_id = p.product_id
    GROUP BY store_id,YEAR(sale_date), MONTH(sale_date)
)
SELECT 
    store_id,year,month, total_revenue,SUM(total_revenue) OVER(PARTITION BY store_id ORDER BY year, month) AS running_total
FROM monthly_sales
Group by store_id,year,month, total_revenue
Order by store_id,year,month
```


