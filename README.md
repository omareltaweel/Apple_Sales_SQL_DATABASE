# Apple_Sales_SQL_DATABASE
Apple Sales Data SQL: Create a database named 'Apple,' design the necessary tables, and import data from a CSV file into these tables. Additionally, provide answers to 20 business-related questions through SQL queries.

-- ============================================
-- SECTION 1: Database and Table Creation
-- ============================================

-- Create the Apple database
CREATE DATABASE Apple;
USE Apple;

-- Create 'stores' table
CREATE TABLE stores (
    store_id VARCHAR(5) PRIMARY KEY,
    store_name VARCHAR(30),
    city VARCHAR(25),
    country VARCHAR(25)
);

-- Create 'category' table
CREATE TABLE category (
    category_id VARCHAR(10) PRIMARY KEY,
    category_name VARCHAR(20)
);

-- Create 'products' table
CREATE TABLE products (
    product_id VARCHAR(10) PRIMARY KEY,
    product_name VARCHAR(35),
    category_id VARCHAR(10),
    launch_date DATE,
    price FLOAT,
    CONSTRAINT FK_category FOREIGN KEY (category_id) REFERENCES category (category_id)
);

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

-- Create 'warranty' table
CREATE TABLE warranty (
    claim_id VARCHAR(10) PRIMARY KEY,
    claim_date DATE,
    sale_id VARCHAR(15),
    repair_status VARCHAR(15),
    CONSTRAINT FK_orders FOREIGN KEY (sale_id) REFERENCES sales (sale_id)
);
