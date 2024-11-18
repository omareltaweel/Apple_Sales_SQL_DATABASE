# Apple_Sales_SQL_DATABASE
Apple Sales Data SQL: Create a database named 'Apple,' design the necessary tables, and import data from a CSV file into these tables. Additionally, provide answers to 20 business-related questions through SQL queries.

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



