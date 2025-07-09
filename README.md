# 🏦 Fake Banking Database (SQL Project)

This project demonstrates SQL database design and querying skills through a simulated banking environment. It includes schema creation with constraints, data relationships, and advanced query logic involving subqueries, grouping, and filtering.

# 🔹 Database Overview

Database Name: banking

Tables:

branch - Bank branches with city/location constraints

customer - Customer personal information

loan - Loans issued by branches

borrower - Relationship between customers and their loans

account - Bank accounts and balances

depositor - Relationship between customers and their deposit accounts

All tables include relevant primary keys, foreign keys, check constraints, and ON DELETE/UPDATE CASCADE behaviors.

# 🔧 Schema Example (DDL)
```bash
CREATE TABLE branch(
	branch_name VARCHAR(40) PRIMARY KEY,
	branch_city VARCHAR(40) NOT NULL CHECK (branch_city IN ('Brooklyn', 'Bronx', 'Manhattan', 'Yonkers')),
	assets MONEY NOT NULL CHECK (assets >= '0.00')
);

CREATE TABLE customer(
	cust_ID VARCHAR(40) PRIMARY KEY,
	customer_name VARCHAR(40) NOT NULL,
	customer_street VARCHAR(40) NOT NULL,
	customer_city VARCHAR(40) NOT NULL
);
```
# 📊 Key SQL Queries

1. Customers with both loans and deposit accounts:
```bash
SELECT c.cust_id, b.loan_number, d.account_number
FROM customer c
JOIN borrower b ON c.cust_id = b.cust_id
JOIN depositor d ON c.cust_id = d.cust_id
GROUP BY c.cust_id, b.loan_number, d.account_number;
```
2. Customers with accounts in their home city branches
```bash
SELECT c.cust_id, c.cust_city, b.branch_city, a.branch_name, d.account_number
FROM customer c
JOIN depositor d ON c.cust_id = d.cust_id
JOIN account a ON d.account_number = a.account_number
JOIN branch b ON a.branch_name = b.branch_name
WHERE c.cust_city = b.branch_city;
```
3. Customers who only borrowed but never deposited
```bash
SELECT c.cust_id, c.cust_name FROM customer c
WHERE c.cust_id IN (SELECT b.cust_id FROM borrower b)
EXCEPT SELECT d.cust_id, c.cust_name FROM depositor d
JOIN customer c ON d.cust_id = c.cust_id; 
```
4.Branches serving customers from 'Harrison'
```bash
SELECT DISTINCT a.branch_name FROM customer c
JOIN depositor d ON c.cust_id = d.cust_id
JOIN account a ON d.account_number = a.account_number
WHERE c.cust_city = 'Harrison'; 
```
5.Customers with the same address as ID 12345 (excluding 12345)
```bash
SELECT c.cust_id, c.cust_name FROM customer c
WHERE (c.cust_street, c.cust_city) IN (SELECT cust_street, 
cust_city FROM customer WHERE cust_id = '12345'
)
EXCEPT SELECT c.cust_id, c.cust_name FROM customer c
WHERE c.cust_id = '12345'; 
```
6.Customers with accounts in every Brooklyn branch
```bash
SELECT c.cust_id, c.cust_name FROM customer c
WHERE c.cust_id IN (
SELECT d.cust_id FROM depositor d
JOIN account a ON d.account_number = a.account_number
JOIN branch b ON a.branch_name = b.branch_name
WHERE b.branch_city = 'Brooklyn' GROUP BY d.cust_id
HAVING COUNT(DISTINCT b.branch_name) = (SELECT COUNT (*)
FROM branch WHERE branch_city = 'Brooklyn'
)); 
```
7. Loans from 'Yonkahs Bankahs' exceeding branch average
```bash
SELECT l.loan_number, c.cust_name, l.branch_name FROM loan l
JOIN borrower b ON l.loan_number = b.loan_number
JOIN customer c ON b.cust_id = c.cust_id
WHERE l.branch_name = 'Yonkahs Bankahs'
AND CAST(l.amount AS DECIMAL) > ( SELECT CAST(AVG(CAST(amount AS DECIMAL))AS DECIMAL)
FROM loan WHERE branch_name = 'Yonkahs Bankahs'
);
```
Each query showcases different SQL logic: JOIN, GROUP BY, HAVING, EXCEPT, and subqueries.

# 🛠 Skills Demonstrated

Relational schema design with constraints

Foreign key and cascading rules

Advanced querying with JOIN, EXCEPT, and HAVING

Data filtering with subqueries

Query logic for real-world banking scenarios


# 📂 Files Included

banking_ddl.sql — Schema and table creation

banking_queries.sql — All queries listed above

README.md — This project documentation

# 👩‍💼 Author

Dana Brooks
Executive Director of Admissions
📧 danatallent@yahoo.com
🔗 LinkedIn
