---
title: 'SQL Essentials: Master Database Queries'
description: 'Learn SQL from basics to advanced queries - the essential skill for working with databases'
pubDate: 'Dec 14 2025'
heroImage: '/blog-placeholder-1.jpg'
---

SQL (Structured Query Language) is the standard language for working with relational databases. Whether you're building web apps, analyzing data, or managing databases, SQL is essential. Let's master the fundamentals.

## Basic SELECT Queries

\`\`\`sql
-- Get all columns
SELECT * FROM users;

-- Get specific columns
SELECT name, email FROM users;

-- With alias
SELECT name AS full_name, email AS contact_email
FROM users;

-- Limit results
SELECT * FROM users LIMIT 10;

-- Remove duplicates
SELECT DISTINCT city FROM users;
\`\`\`

## WHERE Clause - Filtering

\`\`\`sql
-- Exact match
SELECT * FROM users WHERE age = 25;

-- Comparison operators
SELECT * FROM products WHERE price > 100;
SELECT * FROM products WHERE price <= 50;
SELECT * FROM users WHERE age BETWEEN 18 AND 65;

-- String matching
SELECT * FROM users WHERE name = 'John';
SELECT * FROM users WHERE email LIKE '%@gmail.com';
SELECT * FROM users WHERE name LIKE 'J%'; -- Starts with J

-- Multiple conditions
SELECT * FROM users
WHERE age >= 18 AND city = 'New York';

SELECT * FROM products
WHERE category = 'Electronics' OR category = 'Books';

-- IN operator
SELECT * FROM users
WHERE city IN ('New York', 'Los Angeles', 'Chicago');

-- NULL checks
SELECT * FROM users WHERE phone IS NULL;
SELECT * FROM users WHERE phone IS NOT NULL;
\`\`\`

## ORDER BY - Sorting

\`\`\`sql
-- Ascending (default)
SELECT * FROM products ORDER BY price;
SELECT * FROM products ORDER BY price ASC;

-- Descending
SELECT * FROM products ORDER BY price DESC;

-- Multiple columns
SELECT * FROM users
ORDER BY city ASC, age DESC;
\`\`\`

## INSERT - Adding Data

\`\`\`sql
-- Insert single row
INSERT INTO users (name, email, age)
VALUES ('John Doe', 'john@example.com', 30);

-- Insert multiple rows
INSERT INTO users (name, email, age) VALUES
  ('Alice Smith', 'alice@example.com', 25),
  ('Bob Johnson', 'bob@example.com', 35);
\`\`\`

## UPDATE - Modifying Data

\`\`\`sql
-- Update specific rows
UPDATE users
SET age = 31, city = 'Boston'
WHERE email = 'john@example.com';

-- Update multiple rows
UPDATE products
SET price = price * 0.9  -- 10% discount
WHERE category = 'Electronics';

-- ⚠️ Be careful! Without WHERE, updates ALL rows
UPDATE users SET active = true;  -- Updates everyone!
\`\`\`

## DELETE - Removing Data

\`\`\`sql
-- Delete specific rows
DELETE FROM users WHERE id = 5;

-- Delete based on conditions
DELETE FROM users WHERE created_at < '2020-01-01';

-- ⚠️ Without WHERE, deletes ALL rows
DELETE FROM users;  -- Deletes everyone!
\`\`\`

## Aggregate Functions

\`\`\`sql
-- Count rows
SELECT COUNT(*) FROM users;
SELECT COUNT(DISTINCT city) FROM users;

-- Sum
SELECT SUM(price) FROM products;

-- Average
SELECT AVG(age) FROM users;

-- Min/Max
SELECT MIN(price) FROM products;
SELECT MAX(age) FROM users;
\`\`\`

## GROUP BY - Aggregating Data

\`\`\`sql
-- Count users per city
SELECT city, COUNT(*) as user_count
FROM users
GROUP BY city;

-- Average price per category
SELECT category, AVG(price) as avg_price
FROM products
GROUP BY category;

-- Multiple columns
SELECT city, age, COUNT(*) as count
FROM users
GROUP BY city, age;

-- With HAVING (filter after grouping)
SELECT category, AVG(price) as avg_price
FROM products
GROUP BY category
HAVING AVG(price) > 100;
\`\`\`

## JOINs - Combining Tables

### INNER JOIN

\`\`\`sql
-- Get users and their orders
SELECT users.name, orders.total
FROM users
INNER JOIN orders ON users.id = orders.user_id;

-- With alias
SELECT u.name, o.total, o.created_at
FROM users u
INNER JOIN orders o ON u.id = o.user_id;
\`\`\`

### LEFT JOIN

\`\`\`sql
-- Get all users, including those without orders
SELECT u.name, o.total
FROM users u
LEFT JOIN orders o ON u.id = o.user_id;
\`\`\`

### Multiple JOINs

\`\`\`sql
SELECT
  u.name,
  o.total,
  p.name as product_name
FROM users u
INNER JOIN orders o ON u.id = o.user_id
INNER JOIN products p ON o.product_id = p.id;
\`\`\`

## Subqueries

\`\`\`sql
-- Find users with above-average age
SELECT name, age
FROM users
WHERE age > (SELECT AVG(age) FROM users);

-- IN subquery
SELECT name
FROM products
WHERE category_id IN (
  SELECT id FROM categories WHERE active = true
);

-- Correlated subquery
SELECT u.name,
  (SELECT COUNT(*) FROM orders WHERE user_id = u.id) as order_count
FROM users u;
\`\`\`

## Dates and Times

\`\`\`sql
-- Current date/time
SELECT NOW();
SELECT CURRENT_DATE;

-- Date comparisons
SELECT * FROM orders WHERE created_at >= '2025-01-01';

-- Date functions
SELECT DATE(created_at) FROM orders;
SELECT YEAR(created_at), MONTH(created_at) FROM orders;

-- Date arithmetic
SELECT * FROM orders
WHERE created_at > NOW() - INTERVAL 30 DAY;
\`\`\`

## String Functions

\`\`\`sql
-- Concatenation
SELECT CONCAT(first_name, ' ', last_name) as full_name FROM users;

-- Upper/Lower case
SELECT UPPER(name), LOWER(email) FROM users;

-- Length
SELECT name, LENGTH(name) as name_length FROM users;

-- Substring
SELECT SUBSTRING(email, 1, 5) FROM users;

-- Replace
SELECT REPLACE(phone, '-', '') FROM users;
\`\`\`

## Indexes - Performance

\`\`\`sql
-- Create index for faster queries
CREATE INDEX idx_email ON users(email);
CREATE INDEX idx_city_age ON users(city, age);

-- Unique index (enforces uniqueness)
CREATE UNIQUE INDEX idx_username ON users(username);

-- View indexes
SHOW INDEXES FROM users;

-- Drop index
DROP INDEX idx_email ON users;
\`\`\`

## Transactions

\`\`\`sql
-- Start transaction
START TRANSACTION;

-- Make changes
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;

-- Commit if successful
COMMIT;

-- Or rollback if error
ROLLBACK;
\`\`\`

## Creating Tables

\`\`\`sql
CREATE TABLE users (
  id INT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(100) NOT NULL,
  email VARCHAR(255) UNIQUE NOT NULL,
  age INT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

-- With foreign key
CREATE TABLE orders (
  id INT PRIMARY KEY AUTO_INCREMENT,
  user_id INT NOT NULL,
  total DECIMAL(10, 2) NOT NULL,
  status VARCHAR(20) DEFAULT 'pending',
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (user_id) REFERENCES users(id)
);
\`\`\`

## Altering Tables

\`\`\`sql
-- Add column
ALTER TABLE users ADD COLUMN phone VARCHAR(20);

-- Modify column
ALTER TABLE users MODIFY COLUMN age INT DEFAULT 0;

-- Rename column
ALTER TABLE users RENAME COLUMN phone TO mobile;

-- Drop column
ALTER TABLE users DROP COLUMN mobile;

-- Add index
ALTER TABLE users ADD INDEX idx_email (email);
\`\`\`

## Common Patterns

### Pagination

\`\`\`sql
-- Page 1 (items 1-10)
SELECT * FROM products ORDER BY id LIMIT 10 OFFSET 0;

-- Page 2 (items 11-20)
SELECT * FROM products ORDER BY id LIMIT 10 OFFSET 10;
\`\`\`

### Find Duplicates

\`\`\`sql
SELECT email, COUNT(*) as count
FROM users
GROUP BY email
HAVING COUNT(*) > 1;
\`\`\`

### Ranking

\`\`\`sql
SELECT
  name,
  score,
  RANK() OVER (ORDER BY score DESC) as rank
FROM students;
\`\`\`

### Running Totals

\`\`\`sql
SELECT
  date,
  revenue,
  SUM(revenue) OVER (ORDER BY date) as running_total
FROM daily_sales;
\`\`\`

## Best Practices

1. **Always use WHERE with UPDATE/DELETE** - Prevent accidental mass changes
2. **Index frequently queried columns** - Especially foreign keys
3. **Use transactions for related changes** - Maintain data integrity
4. **Avoid SELECT *** - Specify needed columns
5. **Use meaningful aliases** - Make queries readable
6. **Test with LIMIT first** - Before running large updates

## Practice Resources

- [SQL Fiddle](http://sqlfiddle.com/) - Online SQL playground
- [LeetCode SQL](https://leetcode.com/problemset/database/) - Practice problems
- [Mode SQL Tutorial](https://mode.com/sql-tutorial/) - Interactive lessons

Master SQL and unlock the power of data!
