# Project 1: Foundational Data Analysis in Retail for Portfolio Development

## Project Overview

**Project Title**: Retail Sales Analysis  

In this first project, the main objective is to conduct foundational data analysis using SQL to investigate and derive insights from retail sales data. This project serves as an entry point in a planned three-part series, where each project will build on the last to deepen skills in People Analytics and data management.

The upcoming projects will:

Expand to Project 2 (using MySQL) for more complex query operations in employee and retail analytics.
Conclude with Project 3 (Google BigQuery), where ETL, data synchronization, and integration from external sources will be applied, further preparing the dataset for visualization and advanced analysis with tools like Tableau and Looker Studio.
This approach provides a comprehensive journey from core SQL skills to high-level analytics, covering end-to-end data processes in retail.


## Objectives

1. **Database Setup**: Design and set up essential tables for a retail sales environment, including data on products, transactions, customers, and inventory management.

2. **Data Structuring and Relationships**: Develop relationships across tables to represent a realistic retail database structure that supports various types of queries and business logic.

3. **SQL Querying and Analysis**: Use SQL to answer common retail analytics questions, such as tracking best-sellers, customer purchase behavior, inventory status, and supplier relationships.

4. **Data Optimization and Indexing**: Implement indexing to improve query performance for high-demand information, such as customer purchases and sales trends.

5. **Portfolio-Ready Presentation**: Ensure the project is structured for a professional portfolio, demonstrating proficiency in SQL and data handling within a retail context.

## Project Structure

### 1. Database Setup

- **Database Creation**: The project starts by creating a database named `retail_db`.
- **Table Creation**: the database structure is established by creating the necessary tables to hold all retail-related data. This step involves defining five essential tables: category, product, transaction, employee, and organizational_structure.

Each table is designed with specific columns and data types to meet the needs of the project:

- The `category` table stores information about product categories, including category ID and category name.
- The `product` table contains product details, including product ID, category ID (linked to category), product name, price, and the date the product was added.
- The `transaction` table tracks all sales, recording transaction ID, product ID (linked to product), quantity sold, payment method, and the total sale amount.
- The `employee` table stores employee details, such as employee ID, first and last names, position, and hire date.
- The `organizational_structure` table represents the hierarchical structure of the company, specifying employee IDs and their corresponding managers.

```sql
-- 1. Create the category table
CREATE TABLE category (
    category_id INT PRIMARY KEY,
    category_name VARCHAR(50)
);

-- 2. Create the product table
CREATE TABLE product (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(100),
    category_id INT,
    price DECIMAL(10, 2),
    date_added DATE,
    FOREIGN KEY (category_id) REFERENCES category(category_id)
);

-- 3. Create the transaction table
CREATE TABLE transaction (
    transaction_id INT PRIMARY KEY,
    product_id INT,
    quantity INT,
    total_price DECIMAL(10, 2),
    transaction_date DATE,
    payment_method VARCHAR(20),
    FOREIGN KEY (product_id) REFERENCES product(product_id)
);

-- 4. Create the employee table
CREATE TABLE employee (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    position VARCHAR(50),
    hire_date DATE
);

-- 5. Create the organizational_structure table
CREATE TABLE organizational_structure (
    employee_id INT,
    manager_id INT,
    FOREIGN KEY (employee_id) REFERENCES employee(employee_id),
    FOREIGN KEY (manager_id) REFERENCES employee(employee_id)
);
```
- **Insert Data into Tables**: Insert the data into each table, following the order of dependencies (e.g., insert data into the category table before product, because product references category).

```sql
-- Insert data into the category table
INSERT INTO category (category_id, category_name)
VALUES
(1, 'Beverages'),
(2, 'Snacks'),
(3, 'Household Items');

-- Insert data into the product table
INSERT INTO product (product_id, product_name, category_id, price, date_added)
VALUES
(1, 'Coca-Cola 500ml', 1, 15000, '2024-01-01'),
(2, 'Indomie Goreng', 2, 3500, '2024-02-01'),
(3, 'Toilet Paper Roll', 3, 5000, '2024-03-01'),
(4, 'KitKat 45g', 2, 10000, '2024-04-01'),
(5, 'Pepsi 500ml', 1, 15000, '2024-05-01'),
(6, 'UHT Milk', 1, 7000, '2024-06-01');

-- Insert data into the transaction table
INSERT INTO transaction (transaction_id, product_id, quantity, total_price, transaction_date, payment_method)
VALUES
(1, 1, 3, 45000, '2024-11-01', 'Cash'),
(2, 2, 2, 7000, '2024-11-01', 'Credit Card'),
(3, 3, 1, 5000, '2024-11-01', 'E-wallet'),
(4, 4, 5, 50000, '2024-11-02', 'Cash'),
(5, 5, 2, 30000, '2024-11-02', 'Credit Card'),
(6, 6, 3, 21000, '2024-11-02', 'E-wallet');

-- Insert data into the employee table
INSERT INTO employee (employee_id, first_name, last_name, position, hire_date)
VALUES
(1, 'Andi', 'Budi', 'Manager', '2020-01-01'),
(2, 'Siti', 'Aminah', 'Cashier', '2021-02-15'),
(3, 'Budi', 'Santoso', 'Warehouse Staff', '2019-03-10'),
(4, 'Joko', 'Wahyu', 'Cashier', '2022-07-05'),
(5, 'Dewi', 'Rahmawati', 'Manager', '2020-11-20'),
(6, 'Sandi', 'Hadi', 'Warehouse Staff', '2018-05-30');

-- Insert data into the organizational_structure table
INSERT INTO organizational_structure (position, employee_id, manager_id)
VALUES
(1, NULL),
(2, 1),
(3, 1),
(4, 1),
(5, NULL),
(6, 5);
```
### 2. Data Join and How to Save It

To make it easier for me to process the data, I usually join tables that have relationships between tables considering that our database consists of more than one table. We can use the JOIN command for this. To avoid duplicate columns, I prefer to use USING instead of ON to connect the tables.
As a note, to save the result of this query, we will use the CREATE TABLE command so that we can call the result table easily in the future.

```sql
CREATE TABLE retail AS
SELECT *
FROM product
JOIN transaction_data USING (product_id)
JOIN category USING (category_id);

CREATE TABLE structure AS
SELECT * 
FROM employee
JOIN organizational_structure USING (employee_id);
```
### 3. Data Exploration & Cleaning

- **Null Value Check**: Check for any null values in the dataset and delete records with missing data.
- **Table Join**: Show relational table join in dataset.
- **Record Count**: Determine the total number of records in the dataset.
- **Product Count**: Find out how many unique products are in the dataset.
- **Category Count**: Identify all unique product categories in the dataset.
- **Transaction Count**: Find out how many unique transactions in the dataset.

```sql
SELECT * FROM retail
WHERE category_id IS NULL OR product_id IS NULL OR	product_name IS NULL OR	price IS NULL OR date_added IS NULL OR	
transaction_id IS NULL OR	quantity IS NULL OR	total_price IS NULL OR	transaction_date IS NULL OR	
payment_method IS NULL OR	category_name IS NULL;

-- if any data appears, then continue with the DELETE command

DELETE FROM retail
WHERE category_id IS NULL OR product_id IS NULL OR	product_name IS NULL OR	price IS NULL OR date_added IS NULL OR	
transaction_id IS NULL OR	quantity IS NULL OR	total_price IS NULL OR	transaction_date IS NULL OR	
payment_method IS NULL OR	category_name IS NULL;

SELECT * FROM retail
ORDER BY 2;

SELECT COUNT(*) AS total_records FROM retail;
SELECT COUNT(DISTINCT product_id) AS total_product FROM retail;
SELECT DISTINCT category_name FROM retail;
SELECT COUNT(DISTINCT transaction_id) AS total_transactions FROM retail;
```
### 4. Data Analysis & Findings

The following SQL queries were developed to answer specific business questions:

Basic Querying and Filtering
- Display products in a specific category (e.g., food, drink, etc.).
- Show products added after a certain date, sorted by the date they were added.
  
Aggregation and Grouping
- Count the total number of products in each category.
- Display the number of transactions for each payment type (cash, credit card, e-wallet, etc.).
  
Joining and Relating Tables
- Show product data with transaction data, including both sold and unsold products.
- Join the product table with the category table to show the product name along with its category and price.
  
Aggregation Functions and Advanced Analysis
- Display the top 3 products with the highest sales in each category.
- Calculate the total revenue from transactions in a specific month.
  
Using Aliases and String Functions
- Display the full product name (combination of the brand name and product name) in uppercase.
- Display products with the letter "A" in their name.
- Using Common Table Expressions (CTE) and Subqueries
- Display products with prices above the average price of products in the same category.
- Use a subquery to show products with stock levels above 100 units.
  
Recursive CTE for Hierarchical Structure
- Display the employee hierarchy, from manager down to cashier, using the organizational structure.
  
Using Other Built-in Functions
- Display transaction dates in the format "DD-MM-YYYY".
- Calculate the total store operating hours each day (based on store opening and closing times).
  
Query Optimization and Indexing
- Create an index on the transaction table to improve query performance based on the transaction date.

These questions cover a range of SQL skills, from basic data retrieval to more advanced techniques like aggregation, joining, and hierarchical queries.








