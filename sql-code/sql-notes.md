# 🗄️ SQL — Zero to Advanced: Complete Notes
### *Hindi-Hinglish Explanations + English Model Answers + Interview Q&A + Hands-On*
> **For:** 3 Years Experience | **Level:** Foundational → Expert | **Style:** Eureka 💡 Moments

---

## 📚 TABLE OF CONTENTS

1. [What is SQL & Why It Matters](#1-what-is-sql--why-it-matters)
2. [Database Types & Technologies Map](#2-database-types--technologies-map)
3. [DDL — Data Definition Language](#3-ddl--data-definition-language)
4. [DML — Data Manipulation Language](#4-dml--data-manipulation-language)
5. [DQL — SELECT Deep Dive](#5-dql--select-deep-dive)
6. [Clauses: WHERE, ORDER BY, GROUP BY, HAVING](#6-clauses-where-order-by-group-by-having)
7. [JOINs — The Heart of SQL](#7-joins--the-heart-of-sql)
8. [Subqueries & CTEs](#8-subqueries--ctes)
9. [Window Functions](#9-window-functions)
10. [Indexes & Performance](#10-indexes--performance)
11. [Transactions & ACID](#11-transactions--acid)
12. [Stored Procedures, Functions & Triggers](#12-stored-procedures-functions--triggers)
13. [Views & Materialized Views](#13-views--materialized-views)
14. [Normalization](#14-normalization)
15. [Advanced Topics: Partitioning, Sharding, Replication](#15-advanced-topics-partitioning-sharding-replication)
16. [SQL in Different Technologies](#16-sql-in-different-technologies)
17. [Interview Q&A — 50+ Questions](#17-interview-qa--50-questions)

---

## 1. What is SQL & Why It Matters

### 💡 Eureka Moment
> *"SQL ek aisi language hai jo database se baat karne ke liye use hoti hai — jaise tum kisi librarian se kehte ho: 'Mujhe 2020 ke baad ki sab science books do, price ke order mein.' Wahi kaam SQL karta hai data ke saath."*

**SQL** = **Structured Query Language**

- Relational databases ke saath kaam karta hai
- Tables mein data store hoti hai (rows + columns)
- ANSI standard hai — MySQL, PostgreSQL, Oracle sab ka base ek hi hai

### The "Eureka" Mental Model

```
REAL WORLD          DATABASE ANALOGY
-----------         ----------------
Excel File      →   Database
Sheet Tab       →   Table
Column Header   →   Field / Column
One Row         →   Record / Row / Tuple
```

### Hands-On: Your First SQL

```sql
-- Ek simple "Hello World" of SQL
SELECT 'Hello, SQL World!' AS message;

-- Current date nikalo
SELECT CURRENT_DATE AS today, CURRENT_TIME AS now;
```

---

## 2. Database Types & Technologies Map

### 💡 Eureka Moment
> *"SQL sirf ek tool nahi hai — yeh 20+ technologies mein use hota hai. Ek baar seekh lo, sab jagah kaam aayega!"*

### 🌐 Technologies Where SQL Runs

| Technology | SQL Flavor | Use Case |
|---|---|---|
| **MySQL** | MySQL SQL | Web apps, WordPress, LAMP stack |
| **PostgreSQL** | PL/pgSQL | Analytics, GIS, complex apps |
| **Oracle DB** | PL/SQL | Enterprise, banking |
| **SQL Server** | T-SQL | Microsoft ecosystem, .NET apps |
| **SQLite** | Standard SQL | Mobile apps, Android, iOS |
| **BigQuery** | BigQuery SQL | Google Cloud analytics |
| **Redshift** | Redshift SQL | AWS data warehouse |
| **Snowflake** | Snowflake SQL | Cloud data warehouse |
| **Spark SQL** | Spark SQL | Big data processing |
| **Hive** | HiveQL | Hadoop ecosystem |
| **Presto/Trino** | Presto SQL | Federated queries |
| **Databricks** | Delta SQL | ML + analytics |
| **Azure Synapse** | T-SQL variant | Azure analytics |
| **DuckDB** | DuckDB SQL | In-process analytics |
| **CockroachDB** | PostgreSQL-compatible | Distributed SQL |
| **PlanetScale** | MySQL-compatible | Serverless MySQL |
| **Supabase** | PostgreSQL | Open-source Firebase |
| **Neon** | PostgreSQL | Serverless Postgres |
| **TiDB** | MySQL-compatible | HTAP workloads |
| **ClickHouse** | ClickHouse SQL | Real-time analytics |

### SQL in Programming Languages

```python
# Python mein SQL — 3 tarikon se
# 1. Direct (sqlite3)
import sqlite3
conn = sqlite3.connect('mydb.db')
cursor = conn.cursor()
cursor.execute("SELECT * FROM users WHERE age > 25")

# 2. ORM (SQLAlchemy)
from sqlalchemy import text
result = session.execute(text("SELECT * FROM users"))

# 3. Pandas
import pandas as pd
df = pd.read_sql("SELECT * FROM orders", conn)
```

```javascript
// Node.js mein SQL
const mysql = require('mysql2');
const connection = mysql.createConnection({host: 'localhost', user: 'root', database: 'mydb'});
connection.query('SELECT * FROM users', (err, results) => console.log(results));
```

```java
// Java JDBC
PreparedStatement stmt = conn.prepareStatement("SELECT * FROM users WHERE id = ?");
stmt.setInt(1, userId);
ResultSet rs = stmt.executeQuery();
```

---

## 3. DDL — Data Definition Language

### 💡 Eureka Moment
> *"DDL matlab database ka blueprint banana — jaise engineer pehle building ka design karta hai, DDL se table ka structure define hota hai. Actual data baad mein aata hai."*

**DDL Commands:** `CREATE`, `ALTER`, `DROP`, `TRUNCATE`, `RENAME`

### 3.1 CREATE TABLE

```sql
-- Basic table creation
CREATE TABLE employees (
    emp_id      INT             PRIMARY KEY AUTO_INCREMENT,
    first_name  VARCHAR(50)     NOT NULL,
    last_name   VARCHAR(50)     NOT NULL,
    email       VARCHAR(100)    UNIQUE NOT NULL,
    phone       VARCHAR(15),
    hire_date   DATE            NOT NULL,
    salary      DECIMAL(10, 2)  DEFAULT 0.00,
    dept_id     INT,
    is_active   BOOLEAN         DEFAULT TRUE,
    created_at  TIMESTAMP       DEFAULT CURRENT_TIMESTAMP,
    updated_at  TIMESTAMP       DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (dept_id) REFERENCES departments(dept_id) ON DELETE SET NULL
);

-- Department table
CREATE TABLE departments (
    dept_id     INT          PRIMARY KEY AUTO_INCREMENT,
    dept_name   VARCHAR(100) NOT NULL UNIQUE,
    manager_id  INT,
    location    VARCHAR(100)
);
```

### 3.2 Data Types Cheat Sheet

```sql
-- Numeric
INT / INTEGER          -- -2B to +2B
BIGINT                 -- Very large numbers
SMALLINT               -- Small numbers
DECIMAL(p, s)          -- Exact precision (money ke liye)
FLOAT / DOUBLE         -- Approximate (scientific)

-- String
VARCHAR(n)             -- Variable length (max n chars)
CHAR(n)                -- Fixed length (padded with spaces)
TEXT                   -- Large text
ENUM('val1', 'val2')   -- Predefined values only

-- Date/Time
DATE                   -- YYYY-MM-DD
TIME                   -- HH:MM:SS
DATETIME               -- YYYY-MM-DD HH:MM:SS
TIMESTAMP              -- Same, auto-updates possible
YEAR                   -- Just the year

-- Boolean
BOOLEAN / BOOL         -- TRUE / FALSE (stored as 1/0)

-- Others
JSON                   -- JSON data (MySQL 5.7+, PostgreSQL)
UUID                   -- Universally unique ID (PostgreSQL)
ARRAY                  -- Arrays (PostgreSQL only)
```

### 3.3 Constraints

```sql
CREATE TABLE products (
    product_id   INT          PRIMARY KEY,                     -- Unique + NOT NULL
    product_name VARCHAR(100) NOT NULL,                        -- Cannot be empty
    sku          VARCHAR(50)  UNIQUE,                          -- No duplicates
    price        DECIMAL(8,2) CHECK (price > 0),               -- Custom rule
    category_id  INT          REFERENCES categories(id),       -- FK shorthand
    stock        INT          DEFAULT 0,                        -- Default value
    
    -- Table-level constraints
    CONSTRAINT chk_price_range CHECK (price BETWEEN 0.01 AND 99999.99),
    CONSTRAINT uq_name_category UNIQUE (product_name, category_id)
);
```

### 3.4 ALTER TABLE

```sql
-- Column add karo
ALTER TABLE employees ADD COLUMN middle_name VARCHAR(50) AFTER first_name;

-- Column ka type change karo
ALTER TABLE employees MODIFY COLUMN phone VARCHAR(20);

-- Column rename karo
ALTER TABLE employees RENAME COLUMN phone TO mobile_number;

-- Column delete karo
ALTER TABLE employees DROP COLUMN middle_name;

-- Constraint add karo
ALTER TABLE employees ADD CONSTRAINT chk_salary CHECK (salary >= 0);

-- Index add karo
ALTER TABLE employees ADD INDEX idx_email (email);

-- Foreign key add karo
ALTER TABLE employees ADD FOREIGN KEY (dept_id) REFERENCES departments(dept_id);
```

### 3.5 DROP vs TRUNCATE vs DELETE

```sql
-- DROP: Poori table hi delete ho jaati hai (structure bhi)
DROP TABLE IF EXISTS temp_data;

-- TRUNCATE: Saara data delete, structure rehta hai (faster than DELETE)
TRUNCATE TABLE audit_logs;

-- DELETE: Specific rows delete, can use WHERE, can ROLLBACK
DELETE FROM audit_logs WHERE created_at < '2020-01-01';
```

| Feature | DELETE | TRUNCATE | DROP |
|---|---|---|---|
| Removes Data | ✅ | ✅ | ✅ |
| Removes Structure | ❌ | ❌ | ✅ |
| WHERE clause | ✅ | ❌ | ❌ |
| Can ROLLBACK | ✅ | ❌ (DDL) | ❌ |
| Triggers Fire | ✅ | ❌ | ❌ |
| Speed | Slow | Fast | Fast |

---

## 4. DML — Data Manipulation Language

### 💡 Eureka Moment
> *"DDL se ghar banaya, DML se ghar ke andar saman rakha, hataya, aur badla. DDL = construction, DML = interior decoration."*

**DML Commands:** `INSERT`, `UPDATE`, `DELETE`, `MERGE`

### 4.1 INSERT

```sql
-- Single row insert
INSERT INTO employees (first_name, last_name, email, hire_date, salary, dept_id)
VALUES ('Rahul', 'Sharma', 'rahul.sharma@company.com', '2021-03-15', 75000.00, 1);

-- Multiple rows (efficient!)
INSERT INTO employees (first_name, last_name, email, hire_date, salary, dept_id)
VALUES
    ('Priya', 'Patel',   'priya.patel@company.com',   '2020-06-01', 80000.00, 2),
    ('Arjun', 'Singh',   'arjun.singh@company.com',   '2019-11-20', 90000.00, 1),
    ('Neha',  'Gupta',   'neha.gupta@company.com',    '2022-01-10', 65000.00, 3);

-- INSERT from SELECT (table se table mein data copy)
INSERT INTO employees_archive
SELECT * FROM employees WHERE hire_date < '2018-01-01';

-- INSERT OR IGNORE (MySQL: duplicate pe skip karo)
INSERT IGNORE INTO employees (email, first_name) VALUES ('rahul@company.com', 'Rahul');

-- UPSERT: Insert karo, already exist kare to update karo
INSERT INTO employees (emp_id, first_name, salary)
VALUES (1, 'Rahul', 85000)
ON DUPLICATE KEY UPDATE salary = 85000;    -- MySQL

-- PostgreSQL version
INSERT INTO employees (emp_id, first_name, salary)
VALUES (1, 'Rahul', 85000)
ON CONFLICT (emp_id) DO UPDATE SET salary = EXCLUDED.salary;
```

### 4.2 UPDATE

```sql
-- Basic update
UPDATE employees
SET salary = salary * 1.10      -- 10% raise
WHERE dept_id = 1;

-- Multiple columns update
UPDATE employees
SET
    salary    = 95000,
    dept_id   = 2,
    updated_at = CURRENT_TIMESTAMP
WHERE emp_id = 5;

-- UPDATE with JOIN (ek table ki values se doosri update karo)
UPDATE employees e
JOIN departments d ON e.dept_id = d.dept_id
SET e.salary = e.salary * 1.15
WHERE d.dept_name = 'Engineering';

-- ⚠️ DANGER: WHERE bhulo mat!
UPDATE employees SET salary = 0;  -- Sabka salary zero ho jaayega!
```

### 4.3 DELETE

```sql
-- Safe delete with WHERE
DELETE FROM employees WHERE emp_id = 10;

-- Delete with condition
DELETE FROM orders
WHERE order_date < '2020-01-01' AND status = 'cancelled';

-- Delete with subquery
DELETE FROM employees
WHERE dept_id IN (
    SELECT dept_id FROM departments WHERE location = 'Closed'
);

-- DELETE with JOIN (MySQL specific)
DELETE e
FROM employees e
JOIN departments d ON e.dept_id = d.dept_id
WHERE d.dept_name = 'Temporary';
```

### 4.4 MERGE (UPSERT — Advanced)

```sql
-- SQL Server / Oracle syntax
MERGE INTO employees AS target
USING new_employee_data AS source
    ON target.emp_id = source.emp_id
WHEN MATCHED THEN
    UPDATE SET target.salary = source.salary
WHEN NOT MATCHED BY TARGET THEN
    INSERT (emp_id, first_name, salary)
    VALUES (source.emp_id, source.first_name, source.salary)
WHEN NOT MATCHED BY SOURCE THEN
    DELETE;
```

---

## 5. DQL — SELECT Deep Dive

### 💡 Eureka Moment
> *"SELECT SQL ka 90% kaam karta hai. Iska order yaad rakho: SELECT → FROM → JOIN → WHERE → GROUP BY → HAVING → ORDER BY → LIMIT. Yeh order likha hota hai alag, chalta alag order mein hai."*

### Execution Order (Very Important!)

```
Likhne ka order:        Chalane ka order:
1. SELECT           →   1. FROM
2. FROM             →   2. JOIN
3. JOIN             →   3. WHERE
4. WHERE            →   4. GROUP BY
5. GROUP BY         →   5. HAVING
6. HAVING           →   6. SELECT
7. ORDER BY         →   7. DISTINCT
8. LIMIT            →   8. ORDER BY
                    →   9. LIMIT
```

### 5.1 SELECT Basics

```sql
-- Sab columns
SELECT * FROM employees;

-- Specific columns
SELECT first_name, last_name, salary FROM employees;

-- Alias (column/table ko nickname do)
SELECT
    first_name AS "First Name",
    last_name  AS "Last Name",
    salary * 12 AS annual_salary
FROM employees e;  -- 'e' is table alias

-- Distinct values (duplicates hatao)
SELECT DISTINCT dept_id FROM employees;
SELECT DISTINCT dept_id, is_active FROM employees;

-- Calculated columns
SELECT
    first_name,
    salary,
    salary * 0.20 AS bonus,
    salary + (salary * 0.20) AS total_compensation
FROM employees;
```

### 5.2 String Functions

```sql
SELECT
    CONCAT(first_name, ' ', last_name) AS full_name,
    UPPER(email)                        AS email_upper,
    LOWER(first_name)                   AS fname_lower,
    LENGTH(first_name)                  AS name_length,
    SUBSTRING(email, 1, 10)            AS email_short,
    TRIM('  hello  ')                   AS trimmed,
    REPLACE(phone, '-', '')             AS clean_phone,
    LEFT(first_name, 3)                 AS name_prefix,
    RIGHT(email, 10)                    AS email_suffix,
    LPAD(emp_id, 6, '0')               AS emp_code    -- '000042'
FROM employees;
```

### 5.3 Numeric Functions

```sql
SELECT
    ROUND(salary / 12, 2)    AS monthly_salary,
    CEIL(3.2)                AS rounded_up,       -- 4
    FLOOR(3.8)               AS rounded_down,     -- 3
    ABS(-500)                AS absolute_val,     -- 500
    MOD(17, 5)               AS remainder,        -- 2
    POWER(2, 10)             AS power_of_2,       -- 1024
    SQRT(144)                AS sq_root           -- 12
FROM employees;
```

### 5.4 Date Functions

```sql
SELECT
    NOW()                            AS current_datetime,
    CURDATE()                        AS today,
    YEAR(hire_date)                  AS hire_year,
    MONTH(hire_date)                 AS hire_month,
    DAY(hire_date)                   AS hire_day,
    DATEDIFF(NOW(), hire_date)       AS days_employed,
    DATE_ADD(hire_date, INTERVAL 1 YEAR) AS first_anniversary,
    DATE_FORMAT(hire_date, '%d-%M-%Y')   AS formatted_date,
    DAYNAME(hire_date)               AS day_of_week,
    QUARTER(hire_date)               AS hire_quarter
FROM employees;
```

### 5.5 Conditional Logic

```sql
-- CASE WHEN (SQL ka if-else)
SELECT
    first_name,
    salary,
    CASE
        WHEN salary >= 100000 THEN 'Senior'
        WHEN salary >= 75000  THEN 'Mid-Level'
        WHEN salary >= 50000  THEN 'Junior'
        ELSE                       'Intern'
    END AS salary_band,

    -- Simple CASE
    CASE dept_id
        WHEN 1 THEN 'Engineering'
        WHEN 2 THEN 'Marketing'
        WHEN 3 THEN 'HR'
        ELSE       'Other'
    END AS department

FROM employees;

-- IF function (MySQL)
SELECT
    first_name,
    IF(salary > 80000, 'High', 'Standard') AS salary_type

FROM employees;

-- IFNULL / COALESCE (NULL handle karo)
SELECT
    first_name,
    IFNULL(phone, 'No Phone')                AS contact,
    COALESCE(phone, email, 'No Contact')     AS best_contact
FROM employees;

-- NULLIF (division by zero safe)
SELECT total_revenue / NULLIF(total_orders, 0) AS avg_order_value
FROM sales_summary;
```

---

## 6. Clauses: WHERE, ORDER BY, GROUP BY, HAVING

### 💡 Eureka Moment
> *"WHERE individual rows filter karta hai. HAVING groups filter karta hai. Iska confusion sabse zyada hota hai interview mein — yaad rakho: GROUP BY se pehle WHERE, GROUP BY ke baad HAVING."*

### 6.1 WHERE Clause

```sql
-- Comparison operators
SELECT * FROM employees WHERE salary > 70000;
SELECT * FROM employees WHERE salary != 50000;
SELECT * FROM employees WHERE hire_date >= '2020-01-01';

-- Logical operators
SELECT * FROM employees
WHERE dept_id = 1 AND salary > 80000;

SELECT * FROM employees
WHERE dept_id = 1 OR dept_id = 2;

SELECT * FROM employees
WHERE NOT dept_id = 3;

-- IN operator (OR ka shortcut)
SELECT * FROM employees
WHERE dept_id IN (1, 2, 4);

-- BETWEEN (inclusive both ends)
SELECT * FROM employees
WHERE salary BETWEEN 60000 AND 90000;

SELECT * FROM employees
WHERE hire_date BETWEEN '2020-01-01' AND '2022-12-31';

-- LIKE (pattern matching)
SELECT * FROM employees WHERE first_name LIKE 'R%';        -- R se start
SELECT * FROM employees WHERE email LIKE '%@gmail.com';    -- gmail users
SELECT * FROM employees WHERE first_name LIKE '_a%';       -- 2nd char 'a'
SELECT * FROM employees WHERE first_name LIKE '%raj%';     -- 'raj' anywhere

-- IS NULL / IS NOT NULL
SELECT * FROM employees WHERE phone IS NULL;
SELECT * FROM employees WHERE dept_id IS NOT NULL;
```

### 6.2 ORDER BY

```sql
-- Ascending (default)
SELECT * FROM employees ORDER BY salary;
SELECT * FROM employees ORDER BY salary ASC;

-- Descending
SELECT * FROM employees ORDER BY salary DESC;

-- Multiple columns
SELECT * FROM employees
ORDER BY dept_id ASC, salary DESC;

-- By column position (useful in complex queries)
SELECT first_name, salary FROM employees
ORDER BY 2 DESC;  -- 2nd column (salary) descending

-- NULLs handling
SELECT * FROM employees ORDER BY phone NULLS LAST;    -- PostgreSQL
SELECT * FROM employees ORDER BY phone IS NULL, phone; -- MySQL trick
```

### 6.3 GROUP BY + Aggregate Functions

```sql
-- Aggregate functions
SELECT
    COUNT(*)           AS total_employees,     -- Saare rows count
    COUNT(phone)       AS employees_with_phone, -- NULL skip karta hai
    SUM(salary)        AS total_payroll,
    AVG(salary)        AS avg_salary,
    MIN(salary)        AS lowest_salary,
    MAX(salary)        AS highest_salary,
    MIN(hire_date)     AS earliest_hire,
    GROUP_CONCAT(first_name ORDER BY first_name SEPARATOR ', ')  -- MySQL
FROM employees;

-- GROUP BY
SELECT
    dept_id,
    COUNT(*)       AS headcount,
    AVG(salary)    AS avg_salary,
    MAX(salary)    AS max_salary,
    MIN(hire_date) AS oldest_hire
FROM employees
GROUP BY dept_id;

-- GROUP BY with JOIN
SELECT
    d.dept_name,
    COUNT(e.emp_id)    AS headcount,
    AVG(e.salary)      AS avg_salary
FROM departments d
LEFT JOIN employees e ON d.dept_id = e.dept_id
GROUP BY d.dept_id, d.dept_name
ORDER BY headcount DESC;
```

### 6.4 HAVING

```sql
-- Departments where avg salary > 75000
SELECT
    dept_id,
    AVG(salary) AS avg_salary
FROM employees
GROUP BY dept_id
HAVING AVG(salary) > 75000;

-- WHERE vs HAVING
SELECT
    dept_id,
    AVG(salary) AS avg_salary
FROM employees
WHERE is_active = TRUE          -- Row-level filter (pehle chalega)
GROUP BY dept_id
HAVING COUNT(*) >= 5            -- Group-level filter (baad mein chalega)
  AND AVG(salary) > 60000;

-- HAVING without GROUP BY (rare but valid)
SELECT COUNT(*) AS total
FROM employees
HAVING COUNT(*) > 100;
```

---

## 7. JOINs — The Heart of SQL

### 💡 Eureka Moment
> *"JOIN matlab do tables ko ek common column pe milana — jaise do alag registers mein ek hi student ka data hai, roll number se match karke ek combined view banao."*

### JOIN Types Visual

```
Table A:  1, 2, 3, 4      Table B:  3, 4, 5, 6

INNER JOIN:     3, 4              (common only)
LEFT JOIN:  1, 2, 3, 4            (all of A + matched B)
RIGHT JOIN:         3, 4, 5, 6   (matched A + all of B)
FULL OUTER: 1, 2, 3, 4, 5, 6    (everything)
CROSS JOIN: every row of A × every row of B
```

### Sample Data Setup

```sql
-- Sample tables for all JOIN examples
CREATE TABLE departments (
    dept_id INT PRIMARY KEY, dept_name VARCHAR(50), budget DECIMAL(12,2)
);
INSERT INTO departments VALUES (1,'Engineering',500000),(2,'Marketing',300000),(3,'HR',200000),(4,'Finance',250000);

CREATE TABLE employees (
    emp_id INT PRIMARY KEY, name VARCHAR(50), dept_id INT, salary DECIMAL(8,2)
);
INSERT INTO employees VALUES
(1,'Rahul',1,90000),(2,'Priya',2,75000),(3,'Arjun',1,85000),
(4,'Neha',5,70000),(5,'Vikram',NULL,65000);
```

### 7.1 INNER JOIN

```sql
-- Sirf woh rows jahan dono tables mein match ho
SELECT
    e.name,
    e.salary,
    d.dept_name
FROM employees e
INNER JOIN departments d ON e.dept_id = d.dept_id;
-- Result: Rahul, Priya, Arjun milenge (Neha dept_id=5 exist nahi karta, Vikram NULL hai)

-- Same as INNER JOIN (JOIN keyword alone = INNER)
SELECT e.name, d.dept_name
FROM employees e
JOIN departments d ON e.dept_id = d.dept_id;
```

### 7.2 LEFT JOIN (LEFT OUTER JOIN)

```sql
-- A ke saare rows + B ke matching rows (no match = NULL)
SELECT
    e.name,
    e.salary,
    d.dept_name         -- NULL aayega Neha aur Vikram ke liye
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.dept_id;

-- 💡 Trick: LEFT JOIN with IS NULL = rows in A but NOT in B
SELECT e.name
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.dept_id
WHERE d.dept_id IS NULL;    -- Employees without valid department
```

### 7.3 RIGHT JOIN

```sql
-- B ke saare rows + A ke matching rows
SELECT
    e.name,
    d.dept_name
FROM employees e
RIGHT JOIN departments d ON e.dept_id = d.dept_id;
-- Finance department dikhega even if no employees

-- 💡 Tip: RIGHT JOIN avoid karo — same result LEFT JOIN se
-- Swap table order instead: more readable
SELECT e.name, d.dept_name
FROM departments d
LEFT JOIN employees e ON d.dept_id = e.dept_id;
```

### 7.4 FULL OUTER JOIN

```sql
-- Dono tables ke saare rows (MySQL mein native FULL JOIN nahi hai!)
-- PostgreSQL / SQL Server:
SELECT e.name, d.dept_name
FROM employees e
FULL OUTER JOIN departments d ON e.dept_id = d.dept_id;

-- MySQL WORKAROUND (UNION):
SELECT e.name, d.dept_name
FROM employees e LEFT JOIN departments d ON e.dept_id = d.dept_id
UNION
SELECT e.name, d.dept_name
FROM employees e RIGHT JOIN departments d ON e.dept_id = d.dept_id;
```

### 7.5 CROSS JOIN

```sql
-- Cartesian product (har row × har row)
-- Use case: combinations generate karna
SELECT
    c.color,
    s.size,
    CONCAT(c.color, '-', s.size) AS variant
FROM colors c
CROSS JOIN sizes s;
-- 3 colors × 4 sizes = 12 combinations
```

### 7.6 SELF JOIN

```sql
-- Ek table ko khud se join karo
-- Use case: employee aur uska manager (dono same table mein)
SELECT
    e.name        AS employee,
    m.name        AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.emp_id;

-- Find pairs of employees in same department
SELECT
    a.name AS emp1,
    b.name AS emp2,
    a.dept_id
FROM employees a
JOIN employees b ON a.dept_id = b.dept_id AND a.emp_id < b.emp_id;
```

### 7.7 Multiple JOINs

```sql
-- 3+ tables join karna
SELECT
    o.order_id,
    c.customer_name,
    p.product_name,
    o.quantity,
    o.order_date
FROM orders o
JOIN customers c  ON o.customer_id  = c.customer_id
JOIN products p   ON o.product_id   = p.product_id
JOIN employees e  ON o.sales_rep_id = e.emp_id
WHERE o.order_date >= '2023-01-01'
ORDER BY o.order_date DESC;
```

---

## 8. Subqueries & CTEs

### 💡 Eureka Moment
> *"Subquery = query ke andar query. Jaise tum pehle ek list banao 'sabse zyada salary wala department kaunsa hai', phir us answer ko use karo doosri query mein. CTE isko aur readable banata hai."*

### 8.1 Subqueries

```sql
-- WHERE mein subquery
SELECT name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);

-- IN mein subquery
SELECT name FROM employees
WHERE dept_id IN (
    SELECT dept_id FROM departments WHERE budget > 300000
);

-- Correlated subquery (outer query pe depend karta hai — slow!)
SELECT e.name, e.salary
FROM employees e
WHERE e.salary > (
    SELECT AVG(e2.salary)
    FROM employees e2
    WHERE e2.dept_id = e.dept_id  -- outer ke dept_id se match
);

-- EXISTS (faster than IN for large datasets)
SELECT d.dept_name
FROM departments d
WHERE EXISTS (
    SELECT 1 FROM employees e WHERE e.dept_id = d.dept_id
);

-- Scalar subquery (single value return karta hai)
SELECT
    name,
    salary,
    (SELECT AVG(salary) FROM employees) AS company_avg,
    salary - (SELECT AVG(salary) FROM employees) AS diff_from_avg
FROM employees;

-- FROM mein subquery (Derived Table)
SELECT dept_id, avg_sal
FROM (
    SELECT dept_id, AVG(salary) AS avg_sal
    FROM employees
    GROUP BY dept_id
) AS dept_averages
WHERE avg_sal > 75000;
```

### 8.2 CTEs (Common Table Expressions)

```sql
-- Basic CTE — subquery ko naam do
WITH dept_stats AS (
    SELECT
        dept_id,
        AVG(salary) AS avg_salary,
        COUNT(*)    AS headcount
    FROM employees
    GROUP BY dept_id
)
SELECT
    d.dept_name,
    ds.avg_salary,
    ds.headcount
FROM departments d
JOIN dept_stats ds ON d.dept_id = ds.dept_id
WHERE ds.avg_salary > 75000;

-- Multiple CTEs
WITH
high_earners AS (
    SELECT emp_id, name, salary, dept_id
    FROM employees
    WHERE salary > 80000
),
dept_info AS (
    SELECT dept_id, dept_name, budget
    FROM departments
    WHERE budget > 200000
)
SELECT h.name, h.salary, d.dept_name, d.budget
FROM high_earners h
JOIN dept_info d ON h.dept_id = d.dept_id;

-- Recursive CTE (hierarchy / tree traverse karna)
WITH RECURSIVE emp_hierarchy AS (
    -- Anchor: CEO (manager_id IS NULL)
    SELECT emp_id, name, manager_id, 1 AS level, name AS path
    FROM employees
    WHERE manager_id IS NULL

    UNION ALL

    -- Recursive: har manager ke employees
    SELECT e.emp_id, e.name, e.manager_id, h.level + 1, CONCAT(h.path, ' > ', e.name)
    FROM employees e
    JOIN emp_hierarchy h ON e.manager_id = h.emp_id
)
SELECT * FROM emp_hierarchy ORDER BY level, name;
```

---

## 9. Window Functions

### 💡 Eureka Moment
> *"Window functions GROUP BY se alag hain — GROUP BY groups collapse karta hai (5 rows → 1 row), Window function individual rows rakhta hai aur saath mein calculation bhi deta hai. 'Mujhe har employee ki salary aur uske department ka avg salary ek hi row mein chahiye' — yahi window function ka kaam hai."*

### Syntax Structure

```sql
function_name() OVER (
    PARTITION BY column    -- GROUP BY ki tarah (optional)
    ORDER BY column        -- Sort karo window ke andar (optional)
    ROWS/RANGE BETWEEN ... -- Frame define karo (optional)
)
```

### 9.1 Ranking Functions

```sql
SELECT
    name,
    dept_id,
    salary,

    -- ROW_NUMBER: Unique rank (ties ke case mein bhi different)
    ROW_NUMBER() OVER (ORDER BY salary DESC) AS row_num,

    -- RANK: Ties pe same rank, next rank skip hoti hai (1,1,3)
    RANK() OVER (ORDER BY salary DESC) AS rnk,

    -- DENSE_RANK: Ties pe same rank, next rank skip nahi hoti (1,1,2)
    DENSE_RANK() OVER (ORDER BY salary DESC) AS dense_rnk,

    -- NTILE: N buckets mein divide karo
    NTILE(4) OVER (ORDER BY salary) AS salary_quartile

FROM employees;

-- Per-department ranking (PARTITION BY)
SELECT
    name,
    dept_id,
    salary,
    RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS dept_rank
FROM employees;

-- Top 3 employees per department
WITH ranked AS (
    SELECT name, dept_id, salary,
           DENSE_RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rnk
    FROM employees
)
SELECT * FROM ranked WHERE rnk <= 3;
```

### 9.2 Aggregate Window Functions

```sql
SELECT
    name,
    dept_id,
    salary,

    -- Running total
    SUM(salary) OVER (ORDER BY hire_date) AS running_total,

    -- Department total (PARTITION BY)
    SUM(salary) OVER (PARTITION BY dept_id) AS dept_total,

    -- Percentage of department total
    ROUND(salary * 100.0 / SUM(salary) OVER (PARTITION BY dept_id), 2) AS pct_of_dept,

    -- Department average
    AVG(salary) OVER (PARTITION BY dept_id) AS dept_avg,

    -- Compare with avg
    salary - AVG(salary) OVER (PARTITION BY dept_id) AS diff_from_avg,

    -- Running count
    COUNT(*) OVER (PARTITION BY dept_id ORDER BY salary) AS running_count

FROM employees;
```

### 9.3 LAG / LEAD Functions

```sql
-- LAG: Previous row ki value
-- LEAD: Next row ki value
SELECT
    order_date,
    revenue,

    LAG(revenue, 1) OVER (ORDER BY order_date) AS prev_month_revenue,
    LEAD(revenue, 1) OVER (ORDER BY order_date) AS next_month_revenue,

    -- Month over month change
    revenue - LAG(revenue, 1) OVER (ORDER BY order_date) AS mom_change,

    -- % change
    ROUND(
        (revenue - LAG(revenue, 1) OVER (ORDER BY order_date)) * 100.0 /
        LAG(revenue, 1) OVER (ORDER BY order_date)
    , 2) AS mom_pct_change

FROM monthly_sales;
```

### 9.4 FIRST_VALUE / LAST_VALUE / NTH_VALUE

```sql
SELECT
    name,
    dept_id,
    salary,

    FIRST_VALUE(name) OVER (PARTITION BY dept_id ORDER BY salary DESC)
        AS highest_paid_in_dept,

    LAST_VALUE(name) OVER (
        PARTITION BY dept_id ORDER BY salary DESC
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) AS lowest_paid_in_dept,

    NTH_VALUE(salary, 2) OVER (PARTITION BY dept_id ORDER BY salary DESC)
        AS second_highest_salary

FROM employees;
```

---

## 10. Indexes & Performance

### 💡 Eureka Moment
> *"Index ek dictionary ke jaisa hai — book ke peeche wala index. Bina index ke database ko har page padhna padta hai (full table scan). Index ke saath directly page number pe jump karo. Trade-off: read fast, write thoda slow."*

### 10.1 Types of Indexes

```sql
-- 1. Single Column Index
CREATE INDEX idx_salary ON employees(salary);

-- 2. Composite Index (order matters!)
CREATE INDEX idx_dept_salary ON employees(dept_id, salary);
-- ✅ Works for: WHERE dept_id = 1
-- ✅ Works for: WHERE dept_id = 1 AND salary > 50000
-- ❌ Won't use: WHERE salary > 50000 (without dept_id)

-- 3. Unique Index
CREATE UNIQUE INDEX idx_unique_email ON employees(email);

-- 4. Full Text Index (text search ke liye)
CREATE FULLTEXT INDEX idx_ft_description ON products(description);
SELECT * FROM products WHERE MATCH(description) AGAINST('wireless bluetooth');

-- 5. Partial Index (PostgreSQL — specific rows pe index)
CREATE INDEX idx_active_users ON users(email) WHERE is_active = TRUE;

-- 6. Expression Index (PostgreSQL)
CREATE INDEX idx_lower_email ON users(LOWER(email));
```

### 10.2 EXPLAIN — Query Analysis

```sql
-- Query plan dekho
EXPLAIN SELECT * FROM employees WHERE salary > 70000;

-- Detailed analysis (MySQL)
EXPLAIN ANALYZE SELECT e.name, d.dept_name
FROM employees e
JOIN departments d ON e.dept_id = d.dept_id
WHERE e.salary > 70000;

-- Kya dhundo EXPLAIN mein:
-- type: ALL (bad - full scan) → range/ref/const (good)
-- key: NULL (no index used) → index name (good)
-- rows: jitna kam utna better
-- Extra: "Using filesort" or "Using temporary" = slow signal
```

### 10.3 Query Optimization Tips

```sql
-- ❌ BAD: Function on indexed column (index use nahi hoga)
SELECT * FROM employees WHERE YEAR(hire_date) = 2020;

-- ✅ GOOD: Range condition use karo
SELECT * FROM employees
WHERE hire_date BETWEEN '2020-01-01' AND '2020-12-31';

-- ❌ BAD: Leading wildcard (index use nahi hoga)
SELECT * FROM employees WHERE name LIKE '%sharma%';

-- ✅ GOOD: Suffix search to Full Text Search pe shift karo
SELECT * FROM employees WHERE MATCH(name) AGAINST('sharma');

-- ❌ BAD: SELECT * (unnecessary columns fetch)
SELECT * FROM employees;

-- ✅ GOOD: Specific columns
SELECT emp_id, name, salary FROM employees;

-- ❌ BAD: Correlated subquery (N+1 problem)
SELECT name, (SELECT dept_name FROM departments WHERE dept_id = e.dept_id)
FROM employees e;

-- ✅ GOOD: JOIN use karo
SELECT e.name, d.dept_name
FROM employees e JOIN departments d ON e.dept_id = d.dept_id;

-- ❌ BAD: OR on different columns (composite index use nahi hoga)
SELECT * FROM orders WHERE customer_id = 1 OR product_id = 5;

-- ✅ GOOD: UNION use karo
SELECT * FROM orders WHERE customer_id = 1
UNION
SELECT * FROM orders WHERE product_id = 5;
```

---

## 11. Transactions & ACID

### 💡 Eureka Moment
> *"Transaction = ek logical unit of work. Classic example: Bank transfer — Rahul ke account se 1000 rupaye gaye, Priya ke account mein aaye. Agar beech mein system crash ho gaya? Ya dono hote hain ya koi nahi. Yahi ACID guarantee karta hai."*

### ACID Properties

| Property | Meaning | Example |
|---|---|---|
| **Atomicity** | Sab hoga ya kuch nahi | Transfer complete ya rollback |
| **Consistency** | Data hamesha valid state mein | Balance negative nahi ho sakta |
| **Isolation** | Ek transaction doosre ko nahi dekhta | Dirty reads prevent |
| **Durability** | Commit ke baad permanent | Crash ke baad bhi data safe |

### 11.1 Transaction Basics

```sql
-- Start transaction
START TRANSACTION;  -- ya BEGIN;

-- Do operations
UPDATE accounts SET balance = balance - 1000 WHERE account_id = 1;
UPDATE accounts SET balance = balance + 1000 WHERE account_id = 2;

-- Sab theek hai? Permanent karo
COMMIT;

-- Kuch galat hua? Undo karo
ROLLBACK;

-- Real-world with error handling (MySQL Stored Procedure)
DELIMITER //
CREATE PROCEDURE transfer_money(
    IN from_account INT,
    IN to_account INT,
    IN amount DECIMAL(10,2)
)
BEGIN
    DECLARE EXIT HANDLER FOR SQLEXCEPTION
    BEGIN
        ROLLBACK;
        RESIGNAL;
    END;

    START TRANSACTION;

    UPDATE accounts
    SET balance = balance - amount
    WHERE account_id = from_account;

    -- Check if sufficient balance
    IF (SELECT balance FROM accounts WHERE account_id = from_account) < 0 THEN
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Insufficient funds';
    END IF;

    UPDATE accounts
    SET balance = balance + amount
    WHERE account_id = to_account;

    COMMIT;
END //
DELIMITER ;
```

### 11.2 SAVEPOINT

```sql
START TRANSACTION;

INSERT INTO orders VALUES (1, 'Product A', 100);
SAVEPOINT sp1;

INSERT INTO orders VALUES (2, 'Product B', 200);
SAVEPOINT sp2;

INSERT INTO orders VALUES (3, 'Product C', -50);  -- Invalid!

ROLLBACK TO sp2;  -- Sirf last insert undo karo (Product B rehega)
COMMIT;
```

### 11.3 Isolation Levels

```sql
-- Isolation level set karo
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;  -- MySQL default
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;

-- Check current level
SELECT @@transaction_isolation;  -- MySQL
SHOW TRANSACTION ISOLATION LEVEL;  -- PostgreSQL
```

| Level | Dirty Read | Non-Repeatable | Phantom Read | Performance |
|---|---|---|---|---|
| READ UNCOMMITTED | ✅ Possible | ✅ Possible | ✅ Possible | Fastest |
| READ COMMITTED | ❌ Prevented | ✅ Possible | ✅ Possible | Fast |
| REPEATABLE READ | ❌ Prevented | ❌ Prevented | ✅ Possible | Medium |
| SERIALIZABLE | ❌ Prevented | ❌ Prevented | ❌ Prevented | Slowest |

---

## 12. Stored Procedures, Functions & Triggers

### 💡 Eureka Moment
> *"Stored Procedure = database mein stored program. Bar bar likhne wali queries ko ek baar define karo, phir naam se call karo. Jaise microwave mein presets hote hain — 'Popcorn button' ek baar press karo, complex operation automatically hoti hai."*

### 12.1 Stored Procedures

```sql
DELIMITER //

-- Basic procedure
CREATE PROCEDURE get_employees_by_dept(IN p_dept_id INT)
BEGIN
    SELECT emp_id, name, salary, hire_date
    FROM employees
    WHERE dept_id = p_dept_id
    ORDER BY salary DESC;
END //

-- IN + OUT parameters
CREATE PROCEDURE get_dept_stats(
    IN  p_dept_id  INT,
    OUT p_avg_sal  DECIMAL(10,2),
    OUT p_headcount INT
)
BEGIN
    SELECT AVG(salary), COUNT(*)
    INTO p_avg_sal, p_headcount
    FROM employees
    WHERE dept_id = p_dept_id;
END //

DELIMITER ;

-- Call karo
CALL get_employees_by_dept(1);

CALL get_dept_stats(1, @avg, @count);
SELECT @avg AS avg_salary, @count AS headcount;
```

### 12.2 Functions

```sql
DELIMITER //

-- Scalar function (ek value return karta hai)
CREATE FUNCTION calculate_bonus(p_salary DECIMAL(10,2), p_years INT)
RETURNS DECIMAL(10,2)
DETERMINISTIC
BEGIN
    DECLARE v_bonus DECIMAL(10,2);

    IF p_years >= 10 THEN
        SET v_bonus = p_salary * 0.20;
    ELSEIF p_years >= 5 THEN
        SET v_bonus = p_salary * 0.15;
    ELSE
        SET v_bonus = p_salary * 0.10;
    END IF;

    RETURN v_bonus;
END //

DELIMITER ;

-- Use karo
SELECT name, salary, calculate_bonus(salary, YEAR(NOW()) - YEAR(hire_date)) AS bonus
FROM employees;
```

### 12.3 Triggers

```sql
-- AFTER INSERT trigger (audit log)
DELIMITER //

CREATE TRIGGER trg_after_salary_update
AFTER UPDATE ON employees
FOR EACH ROW
BEGIN
    IF OLD.salary != NEW.salary THEN
        INSERT INTO salary_audit (
            emp_id, old_salary, new_salary, changed_at, changed_by
        ) VALUES (
            NEW.emp_id, OLD.salary, NEW.salary, NOW(), USER()
        );
    END IF;
END //

-- BEFORE INSERT trigger (validation)
CREATE TRIGGER trg_before_employee_insert
BEFORE INSERT ON employees
FOR EACH ROW
BEGIN
    -- Salary ko trim karo negative se
    IF NEW.salary < 0 THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Salary cannot be negative';
    END IF;

    -- Email lowercase karo automatically
    SET NEW.email = LOWER(NEW.email);
END //

DELIMITER ;
```

---

## 13. Views & Materialized Views

### 💡 Eureka Moment
> *"View = saved query with a name. Ek complex query baar baar likhne ki jagah, ek baar view banao. Aisa laga jaise ek nayi table hai, lekin asal mein woh query hai. Materialized View = actual data stored karta hai — faster but stale ho sakta hai."*

### 13.1 Regular Views

```sql
-- View create karo
CREATE VIEW employee_details AS
SELECT
    e.emp_id,
    CONCAT(e.first_name, ' ', e.last_name) AS full_name,
    e.salary,
    d.dept_name,
    e.hire_date,
    DATEDIFF(NOW(), e.hire_date) / 365 AS years_employed
FROM employees e
JOIN departments d ON e.dept_id = d.dept_id
WHERE e.is_active = TRUE;

-- Use karo like a table
SELECT * FROM employee_details WHERE dept_name = 'Engineering';
SELECT dept_name, AVG(salary) FROM employee_details GROUP BY dept_name;

-- View update karo
CREATE OR REPLACE VIEW employee_details AS
SELECT e.emp_id, e.first_name, e.salary, d.dept_name
FROM employees e JOIN departments d ON e.dept_id = d.dept_id;

-- Drop karo
DROP VIEW IF EXISTS employee_details;

-- Updatable view (sirf simple views)
UPDATE employee_details SET salary = 80000 WHERE emp_id = 1;
```

### 13.2 Materialized Views (PostgreSQL)

```sql
-- PostgreSQL mein materialized view
CREATE MATERIALIZED VIEW dept_monthly_stats AS
SELECT
    d.dept_name,
    DATE_TRUNC('month', o.order_date) AS month,
    COUNT(*) AS order_count,
    SUM(o.total_amount) AS revenue
FROM orders o
JOIN departments d ON o.dept_id = d.dept_id
GROUP BY d.dept_name, DATE_TRUNC('month', o.order_date);

-- Index on materialized view
CREATE INDEX ON dept_monthly_stats(dept_name, month);

-- Refresh karo (data update hoga)
REFRESH MATERIALIZED VIEW dept_monthly_stats;               -- Blocks reads
REFRESH MATERIALIZED VIEW CONCURRENTLY dept_monthly_stats;  -- Non-blocking
```

---

## 14. Normalization

### 💡 Eureka Moment
> *"Normalization = data ko achhi tarah organize karna taaki redundancy kam ho aur integrity badhe. Jaise ek register mein student ka naam 50 jagah likhne ki jagah, ek jagah likho aur roll number se refer karo."*

### Normal Forms

```sql
-- ❌ UNNORMALIZED (1NF violation): multiple values in one cell
-- student_id | name   | subjects
-- 1          | Rahul  | Math, Physics, Chemistry
-- 2          | Priya  | Math, Biology

-- ✅ 1NF: Atomic values, no repeating groups
-- student_id | name  | subject
-- 1          | Rahul | Math
-- 1          | Rahul | Physics
-- 1          | Rahul | Chemistry
-- 2          | Priya | Math

-- ❌ 2NF violation: partial dependency
-- order_id | product_id | qty | product_name | customer_name
-- (product_name depends only on product_id, not full PK)

-- ✅ 2NF: Remove partial dependencies — separate table
-- orders: order_id, product_id, qty, customer_id
-- products: product_id, product_name, price
-- customers: customer_id, customer_name

-- ❌ 3NF violation: transitive dependency
-- emp_id | dept_id | dept_name | dept_location
-- (dept_name depends on dept_id, not emp_id)

-- ✅ 3NF: Remove transitive dependencies
-- employees: emp_id, dept_id
-- departments: dept_id, dept_name, dept_location

-- BCNF: Every determinant is a candidate key
-- 4NF: No multi-valued dependencies
-- 5NF: No join dependencies
```

---

## 15. Advanced Topics: Partitioning, Sharding, Replication

### 💡 Eureka Moment
> *"Jab ek table itni badi ho jaaye ki slow ho jaaye — partition karo (ek table ko logically pieces mein todo). Jab ek server enough na ho — shard karo (alag alag servers pe data daalo). Jab availability chahiye — replicate karo (copies banao)."*

### 15.1 Table Partitioning

```sql
-- RANGE Partitioning (MySQL)
CREATE TABLE orders (
    order_id    INT NOT NULL,
    order_date  DATE NOT NULL,
    amount      DECIMAL(10,2),
    PRIMARY KEY (order_id, order_date)
)
PARTITION BY RANGE (YEAR(order_date)) (
    PARTITION p2020 VALUES LESS THAN (2021),
    PARTITION p2021 VALUES LESS THAN (2022),
    PARTITION p2022 VALUES LESS THAN (2023),
    PARTITION p2023 VALUES LESS THAN (2024),
    PARTITION p_future VALUES LESS THAN MAXVALUE
);

-- LIST Partitioning
CREATE TABLE employees_by_region (
    emp_id INT, name VARCHAR(50), region VARCHAR(20)
)
PARTITION BY LIST COLUMNS (region) (
    PARTITION p_north VALUES IN ('Delhi', 'Punjab', 'Haryana'),
    PARTITION p_south VALUES IN ('Chennai', 'Bangalore', 'Hyderabad'),
    PARTITION p_west  VALUES IN ('Mumbai', 'Pune', 'Ahmedabad')
);

-- HASH Partitioning (even distribution)
CREATE TABLE user_events (
    user_id INT, event_date DATE, event_type VARCHAR(50)
)
PARTITION BY HASH(user_id)
PARTITIONS 8;
```

### 15.2 Query Hints & Optimizer

```sql
-- Force index use
SELECT * FROM employees FORCE INDEX (idx_dept_salary)
WHERE dept_id = 1 AND salary > 70000;

-- Ignore index
SELECT * FROM employees IGNORE INDEX (idx_email)
WHERE email = 'test@example.com';

-- MySQL Query Cache hint
SELECT SQL_NO_CACHE * FROM employees;

-- PostgreSQL planner hints (pg_hint_plan extension)
/*+ SeqScan(employees) */ SELECT * FROM employees;
/*+ IndexScan(employees idx_salary) */ SELECT * FROM employees WHERE salary > 70000;
```

---

## 16. SQL in Different Technologies

### 16.1 PostgreSQL Specific

```sql
-- Array operations
SELECT * FROM products WHERE 'electronics' = ANY(tags);
SELECT unnest(ARRAY[1,2,3]) AS num;

-- JSONB operations
SELECT data->>'name' AS name, data->'address'->>'city' AS city
FROM users WHERE data @> '{"active": true}';

-- Window functions (more powerful)
SELECT name, salary, percent_rank() OVER (ORDER BY salary) AS pct_rank FROM employees;

-- LATERAL JOIN
SELECT e.name, latest_order.*
FROM employees e
LEFT JOIN LATERAL (
    SELECT * FROM orders WHERE customer_id = e.emp_id
    ORDER BY order_date DESC LIMIT 1
) latest_order ON TRUE;

-- Generate series
SELECT generate_series('2020-01-01'::date, '2023-12-31'::date, '1 month'::interval) AS month;
```

### 16.2 MySQL Specific

```sql
-- JSON functions
SELECT JSON_EXTRACT(profile, '$.name') AS name FROM users;
SELECT JSON_VALUE(profile, '$.age') AS age FROM users;
INSERT INTO users (profile) VALUES ('{"name": "Rahul", "age": 25}');

-- Full Text Search
CREATE FULLTEXT INDEX idx_ft ON articles(title, body);
SELECT * FROM articles WHERE MATCH(title, body) AGAINST('SQL tutorial' IN NATURAL LANGUAGE MODE);

-- REGEXP
SELECT * FROM employees WHERE name REGEXP '^[AR]';  -- Starts with A or R
```

### 16.3 SQL Server (T-SQL) Specific

```sql
-- TOP instead of LIMIT
SELECT TOP 10 * FROM employees ORDER BY salary DESC;
SELECT TOP 10 PERCENT * FROM employees ORDER BY salary DESC;

-- STRING_AGG (GROUP_CONCAT equivalent)
SELECT dept_id, STRING_AGG(name, ', ') AS employee_list
FROM employees GROUP BY dept_id;

-- TRY_CAST
SELECT TRY_CAST('not a number' AS INT) AS result;  -- Returns NULL instead of error

-- Common Table Expressions with recursion
WITH RECURSIVE Numbers AS (
    SELECT 1 AS n
    UNION ALL
    SELECT n + 1 FROM Numbers WHERE n < 10
)
SELECT * FROM Numbers;

-- PIVOT (rows to columns)
SELECT *
FROM (SELECT dept_id, salary FROM employees) AS src
PIVOT (AVG(salary) FOR dept_id IN ([1],[2],[3])) AS pvt;
```

### 16.4 BigQuery / Cloud SQL

```sql
-- BigQuery: Partitioned tables by date
CREATE TABLE `project.dataset.events`
PARTITION BY DATE(event_date)
CLUSTER BY user_id
AS SELECT * FROM source_table;

-- Wildcard tables
SELECT * FROM `project.dataset.logs_*`
WHERE _TABLE_SUFFIX BETWEEN '20230101' AND '20231231';

-- ARRAY_AGG
SELECT user_id, ARRAY_AGG(product_id ORDER BY order_date) AS product_history
FROM orders GROUP BY user_id;

-- STRUCT
SELECT STRUCT(first_name AS fname, last_name AS lname) AS full_name FROM employees;
```

---

## 17. Interview Q&A — 50+ Questions

### 💡 Eureka Moment
> *"Interview mein SQL ke question 3 categories mein aate hain: Conceptual (kya hai), Scenario-based (yeh situation mein kya karoge), aur Coding (query likho). Teeno ke liye prepare karo!"*

---

### 🔵 BASIC LEVEL (0-1 yr experience)

**Q1. DELETE, TRUNCATE, DROP mein kya farak hai?**

> **Answer:** DELETE DML hai — specific rows remove karta hai WHERE clause ke saath, ROLLBACK possible hai, triggers fire hote hain, slow hai. TRUNCATE DDL hai — saara data hata deta hai, rollback nahi hota, triggers nahi fire hote, fast hai. DROP DDL hai — table ki definition aur saara data dono hata deta hai. Memory aid: "DELETE = eraser, TRUNCATE = white-out, DROP = shredder."

---

**Q2. WHERE aur HAVING mein kya difference hai?**

> **Answer:** WHERE individual rows ko filter karta hai GROUP BY se pehle. HAVING groups ko filter karta hai GROUP BY ke baad. WHERE mein aggregate functions (SUM, COUNT) use nahi kar sakte, HAVING mein kar sakte hain. Example: `WHERE salary > 50000` — individual rows. `HAVING COUNT(*) > 5` — groups.

---

**Q3. Primary Key aur Unique Key mein kya farak hai?**

> **Answer:** Primary Key: NULL allow nahi karta, ek table mein sirf ek hi ho sakti hai, automatically clustered index banta hai. Unique Key: NULL allow karta hai (ek baar), ek table mein multiple ho sakti hain, non-clustered index banta hai.

---

**Q4. INNER JOIN aur LEFT JOIN kab use karte hain?**

> **Answer:** INNER JOIN use karo jab sirf matched records chahiye — e.g., employees jo valid department mein hain. LEFT JOIN use karo jab left table ke saare records chahiye chahe right mein match ho ya na ho — e.g., saare departments (including those with no employees). Rule of thumb: "Are you OK with losing rows? No → LEFT JOIN."

---

**Q5. NULL kya hai aur isko kaise handle karte hain?**

> **Answer:** NULL = unknown / missing value. NULL kisi bhi value ke equal nahi hota, NULL khud se bhi equal nahi. Isliye `WHERE phone = NULL` galat hai — `WHERE phone IS NULL` sahi hai. Handling: `IFNULL(val, default)`, `COALESCE(val1, val2, ...)`, `NULLIF(a, b)`.

---

**Q6. Normalization kya hoti hai aur 1NF, 2NF, 3NF explain karo.**

> **Answer:** Normalization = data redundancy reduce karna, data integrity improve karna. 1NF: Har cell mein atomic value, no repeating groups. 2NF: 1NF + no partial dependency (non-key column full primary key pe depend kare). 3NF: 2NF + no transitive dependency (non-key column kisi aur non-key column pe depend na kare).

---

### 🟡 INTERMEDIATE LEVEL (1-3 yr experience)

**Q7. RANK vs DENSE_RANK vs ROW_NUMBER — difference bolo.**

```sql
-- Example:
-- Salaries: 90000, 85000, 85000, 70000

SELECT salary,
    ROW_NUMBER() OVER (ORDER BY salary DESC),  -- 1, 2, 3, 4 (always unique)
    RANK()       OVER (ORDER BY salary DESC),  -- 1, 2, 2, 4 (gap after tie)
    DENSE_RANK() OVER (ORDER BY salary DESC)   -- 1, 2, 2, 3 (no gap)
FROM employees;
```

> **Answer:** ROW_NUMBER hamesha unique number deta hai, ties bhi alag rakhe. RANK ties ko same rank deta hai lekin next rank skip karta hai (jaise Olympic mein 2 silver = no bronze). DENSE_RANK ties ko same rank deta hai, next rank skip nahi karta.

---

**Q8. Correlated Subquery vs Regular Subquery explain karo.**

```sql
-- Regular (inner query ek baar chalti hai)
SELECT name FROM employees
WHERE dept_id IN (SELECT dept_id FROM departments WHERE budget > 300000);

-- Correlated (outer ke har row ke liye inner chalti hai — N+1 problem!)
SELECT e.name FROM employees e
WHERE e.salary > (SELECT AVG(e2.salary) FROM employees e2 WHERE e2.dept_id = e.dept_id);
```

> **Answer:** Regular subquery outer query se independent hai — ek baar execute hoti hai. Correlated subquery outer query ke har row pe depend karti hai — N times execute hoti hai (N = outer rows). Correlated = slow, lekin sometimes avoidable nahi hota. Alternative: Window functions ya JOINs.

---

**Q9. Index kab help karta hai aur kab nahi?**

> **Answer:** Help karta hai: High cardinality columns (email, phone), WHERE/JOIN/ORDER BY columns, foreign keys. Help nahi karta: Low cardinality (boolean, gender — 2-3 values only), Function applied on column (`YEAR(hire_date)`), Leading wildcard LIKE (`%sharma%`), Very small tables (full scan faster), Columns rarely used in WHERE.

---

**Q10. CTE aur Subquery mein kya prefer karte ho aur kyun?**

> **Answer:** CTE prefer karo kyunki: Readable hai (naam dete hain logic ko), Reusable hai (ek hi CTE multiple baar refer kar sakte hain), Debuggable hai (step by step check karo), Recursive queries support karta hai. Performance: Modern databases mein mostly same, kuch cases mein CTE optimizer ke liye better hints provide karta hai.

---

**Q11. Window function ka real-world use case do.**

```sql
-- E-commerce: Last 3 months revenue vs previous period
WITH monthly AS (
    SELECT DATE_FORMAT(order_date, '%Y-%m') AS month,
           SUM(amount) AS revenue
    FROM orders GROUP BY 1
),
comparison AS (
    SELECT month, revenue,
           LAG(revenue, 1) OVER (ORDER BY month) AS prev_month,
           LAG(revenue, 3) OVER (ORDER BY month) AS same_month_last_quarter
    FROM monthly
)
SELECT *, ROUND((revenue - prev_month)*100/prev_month, 2) AS mom_growth_pct
FROM comparison ORDER BY month DESC LIMIT 6;
```

---

**Q12. Deadlock kya hota hai? Kaise prevent karte hain?**

> **Answer:** Deadlock = do transactions ek doosre ke lock release ka wait karti hain — circular dependency. Transaction A holds lock on Table1, wants Table2. Transaction B holds lock on Table2, wants Table1. Both wait forever. Prevention: Always tables ko same order mein lock karo, transactions chhote rakho, appropriate isolation level choose karo, application level retry logic add karo.

---

**Q13. EXPLAIN ka output kaise padhte hain?**

```sql
EXPLAIN SELECT e.name, d.dept_name
FROM employees e JOIN departments d ON e.dept_id = d.dept_id
WHERE e.salary > 70000;
```

> **Answer:** Key columns: **type** (ALL=bad full scan, ref/range=good index use), **key** (kaunsa index use ho raha hai, NULL=koi nahi), **rows** (estimated rows scanned — kam ho to better), **Extra** (Using filesort/temporary = slow, Using index = fast covering index).

---

**Q14. N+1 Query Problem kya hai?**

```sql
-- N+1 PROBLEM (ORM mein common)
-- 1 query: Fetch all users
SELECT * FROM users;  -- returns 100 users

-- N queries: For each user, fetch orders (100 separate queries!)
SELECT * FROM orders WHERE user_id = 1;
SELECT * FROM orders WHERE user_id = 2;
-- ... 98 more queries

-- SOLUTION: JOIN use karo
SELECT u.*, o.order_id, o.amount
FROM users u
LEFT JOIN orders o ON u.user_id = o.user_id;
```

---

**Q15. Covering Index kya hota hai?**

> **Answer:** Covering Index = query ke liye needed saare columns index mein hi available hain — actual table row read nahi karni padti (index-only scan). Example: `SELECT name, salary FROM employees WHERE dept_id = 1` — agar index `(dept_id, name, salary)` ho to table access hi nahi hoga. Bohot fast hota hai kyunki I/O kam hoti hai.

---

### 🔴 ADVANCED LEVEL (3+ yr experience)

**Q16. MVCC (Multi-Version Concurrency Control) explain karo.**

> **Answer:** MVCC = PostgreSQL/MySQL InnoDB mein use hone wala concurrency mechanism. Har row ki multiple versions store hoti hain. Read operation current "snapshot" dekhti hai (transaction start ke time ki), doosri transactions ke uncommitted changes nahi dikhte. Isliye readers writers ko block nahi karte aur vice versa. Undo log mein purani versions rakhi jaati hain. PostgreSQL mein VACUUM old versions clean karta hai.

---

**Q17. Materialized View kab use karte ho?**

> **Answer:** Jab complex aggregation query slow ho aur data real-time updated nahi chahiye. Use case: Daily reporting dashboards, pre-aggregated analytics (sales by month/region), heavy JOIN results. Trade-offs: Disk space zyada lagti hai, REFRESH pe stale data ho sakta hai. Strategy: Schedule REFRESH in off-peak hours, use CONCURRENTLY to avoid blocking.

---

**Q18. Database Partitioning strategies explain karo.**

> **Answer:** RANGE: Date ya numeric range (e.g., orders by year) — time-series data ke liye best. LIST: Specific values (e.g., region, country) — geographical data ke liye. HASH: Even distribution (e.g., user_id % 8) — write-heavy workloads ke liye. COMPOSITE: Range + Hash combination. Benefits: Partition pruning (sirf relevant partitions scan), maintenance (old partitions drop karo), parallel processing.

---

**Q19. Horizontal vs Vertical Scaling SQL databases ke liye.**

> **Answer:** Vertical Scaling (Scale Up): Better hardware — more RAM, faster CPU, faster SSD. Simple lekin limit hai. Horizontal Scaling (Scale Out): Multiple servers. SQL ke liye complex: Read Replicas (read traffic distribute karo), Sharding (data horizontally split karo by key), Distributed SQL (CockroachDB, TiDB — SQL interface with distributed storage). Most startups: Vertical + Read Replicas → phir Sharding if needed.

---

**Q20. Query ka performance improve karne ka approach kya hai?**

> **Answer (Systematic approach):**
> 1. EXPLAIN / EXPLAIN ANALYZE run karo
> 2. Full table scans identify karo (type=ALL)
> 3. Missing indexes add karo
> 4. Query rewrite karo (avoid functions on columns, avoid leading LIKE)
> 5. SELECT * → specific columns
> 6. Subqueries → JOINs / CTEs
> 7. Pagination: Keyset pagination > OFFSET for large datasets
> 8. Partitioning for large tables
> 9. Caching layer add karo (Redis)
> 10. Read replicas for reporting queries

---

**Q21. Write a query to find the second highest salary.**

```sql
-- Method 1: LIMIT OFFSET
SELECT DISTINCT salary FROM employees ORDER BY salary DESC LIMIT 1 OFFSET 1;

-- Method 2: Subquery
SELECT MAX(salary) FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);

-- Method 3: DENSE_RANK (best for Nth highest)
WITH ranked AS (
    SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM employees
)
SELECT salary FROM ranked WHERE rnk = 2;

-- Method 4: Variable (MySQL)
SET @nth = 2;
WITH ranked AS (
    SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM employees
)
SELECT salary FROM ranked WHERE rnk = @nth;
```

---

**Q22. Employees who earn more than their manager — query likho.**

```sql
-- Self join approach
SELECT e.name AS employee, e.salary AS emp_salary,
       m.name AS manager,  m.salary AS mgr_salary
FROM employees e
JOIN employees m ON e.manager_id = m.emp_id
WHERE e.salary > m.salary;
```

---

**Q23. Duplicate rows find karo aur delete karo.**

```sql
-- Find duplicates
SELECT email, COUNT(*) AS count
FROM employees
GROUP BY email
HAVING COUNT(*) > 1;

-- Delete duplicates (keep lowest emp_id)
DELETE e1 FROM employees e1
JOIN employees e2 ON e1.email = e2.email AND e1.emp_id > e2.emp_id;

-- PostgreSQL version
DELETE FROM employees
WHERE emp_id NOT IN (
    SELECT MIN(emp_id)
    FROM employees
    GROUP BY email
);
```

---

**Q24. Running total query likho.**

```sql
SELECT
    order_date,
    amount,
    SUM(amount) OVER (ORDER BY order_date ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS running_total
FROM orders
ORDER BY order_date;
```

---

**Q25. Department mein top 3 salary wale employees nikalo.**

```sql
WITH dept_ranking AS (
    SELECT
        name, dept_id, salary,
        DENSE_RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rnk
    FROM employees
)
SELECT d.dept_name, dr.name, dr.salary, dr.rnk
FROM dept_ranking dr
JOIN departments d ON dr.dept_id = d.dept_id
WHERE dr.rnk <= 3
ORDER BY d.dept_name, dr.rnk;
```

---

**Q26. Consecutive days login streak find karo.**

```sql
-- User login streak (consecutive days)
WITH daily_logins AS (
    SELECT DISTINCT user_id, DATE(login_time) AS login_date
    FROM user_logins
),
numbered AS (
    SELECT user_id, login_date,
           ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY login_date) AS rn,
           DATE_SUB(login_date, INTERVAL ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY login_date) DAY) AS grp
    FROM daily_logins
)
SELECT user_id, MIN(login_date) AS streak_start, MAX(login_date) AS streak_end,
       COUNT(*) AS streak_length
FROM numbered
GROUP BY user_id, grp
ORDER BY streak_length DESC;
```

---

**Q27. Pivot table — rows ko columns mein convert karo.**

```sql
-- Monthly sales by quarter (rows to columns)
SELECT
    YEAR(order_date) AS year,
    SUM(CASE WHEN QUARTER(order_date) = 1 THEN amount ELSE 0 END) AS Q1,
    SUM(CASE WHEN QUARTER(order_date) = 2 THEN amount ELSE 0 END) AS Q2,
    SUM(CASE WHEN QUARTER(order_date) = 3 THEN amount ELSE 0 END) AS Q3,
    SUM(CASE WHEN QUARTER(order_date) = 4 THEN amount ELSE 0 END) AS Q4,
    SUM(amount) AS annual_total
FROM orders
GROUP BY YEAR(order_date)
ORDER BY year;
```

---

**Q28. Islands and Gaps problem solve karo.**

```sql
-- Find gaps in order IDs (missing IDs)
SELECT a.order_id + 1 AS gap_start,
       MIN(b.order_id) - 1 AS gap_end
FROM orders a
JOIN orders b ON a.order_id < b.order_id
GROUP BY a.order_id
HAVING gap_start < MIN(b.order_id)
ORDER BY gap_start;

-- Modern approach (lead/lag)
WITH numbered AS (
    SELECT order_id,
           LEAD(order_id) OVER (ORDER BY order_id) AS next_id
    FROM orders
)
SELECT order_id + 1 AS gap_start, next_id - 1 AS gap_end
FROM numbered
WHERE next_id > order_id + 1;
```

---

**Q29. Customer retention rate calculate karo.**

```sql
-- Month-over-month retention
WITH monthly_customers AS (
    SELECT customer_id, DATE_FORMAT(order_date, '%Y-%m') AS month
    FROM orders
    GROUP BY customer_id, DATE_FORMAT(order_date, '%Y-%m')
),
retention AS (
    SELECT
        curr.month,
        COUNT(DISTINCT curr.customer_id) AS current_customers,
        COUNT(DISTINCT prev.customer_id) AS retained_customers
    FROM monthly_customers curr
    LEFT JOIN monthly_customers prev
        ON curr.customer_id = prev.customer_id
        AND prev.month = DATE_FORMAT(DATE_SUB(STR_TO_DATE(CONCAT(curr.month, '-01'), '%Y-%m-%d'), INTERVAL 1 MONTH), '%Y-%m')
    GROUP BY curr.month
)
SELECT month, current_customers, retained_customers,
       ROUND(retained_customers * 100.0 / current_customers, 2) AS retention_rate
FROM retention ORDER BY month;
```

---

**Q30. Slowly Changing Dimension (SCD Type 2) implement karo.**

```sql
-- SCD Type 2: Historical tracking
CREATE TABLE employee_history (
    history_id  INT AUTO_INCREMENT PRIMARY KEY,
    emp_id      INT,
    name        VARCHAR(100),
    salary      DECIMAL(10,2),
    dept_id     INT,
    valid_from  DATE NOT NULL,
    valid_to    DATE,  -- NULL means current record
    is_current  BOOLEAN DEFAULT TRUE,
    INDEX (emp_id, is_current)
);

-- Procedure to update with history
DELIMITER //
CREATE PROCEDURE update_employee_scd2(
    IN p_emp_id INT, IN p_new_salary DECIMAL(10,2)
)
BEGIN
    -- Close existing record
    UPDATE employee_history
    SET valid_to = CURDATE() - INTERVAL 1 DAY, is_current = FALSE
    WHERE emp_id = p_emp_id AND is_current = TRUE;

    -- Insert new record
    INSERT INTO employee_history (emp_id, name, salary, dept_id, valid_from)
    SELECT emp_id, name, p_new_salary, dept_id, CURDATE()
    FROM employees WHERE emp_id = p_emp_id;
END //
DELIMITER ;
```

---

### 🎯 QUICK FIRE ROUND

**Q31. Difference between UNION and UNION ALL?**
> UNION removes duplicates (uses DISTINCT internally — slower). UNION ALL keeps all rows (faster). Use UNION ALL when you know no duplicates or don't care.

**Q32. What is a Composite Index?**
> Index on multiple columns. Order matters! Index on (A, B) works for queries on A alone or A+B, but NOT for B alone (left-prefix rule).

**Q33. What is a Clustered Index?**
> Clustered Index = data physically sorted by this index. Only one per table. In MySQL InnoDB, Primary Key IS the clustered index. Non-clustered indexes point to clustered index value.

**Q34. Difference between stored procedure and function?**
> Function returns a value, can be used in SELECT. Procedure may or may not return values (OUT params), cannot be used in SELECT, can have transactions, can call other procedures.

**Q35. What is referential integrity?**
> FK relationship ka rule — child table mein koi value add nahi ho sakti jo parent table mein exist nahi karti. ON DELETE CASCADE, SET NULL, RESTRICT se behavior control hota hai.

**Q36. OLTP vs OLAP difference?**
> OLTP: Many small transactions (INSERT/UPDATE), normalized, low latency — operational DBs (MySQL, PostgreSQL). OLAP: Few complex queries on large data (SELECT with heavy aggregations), denormalized/star schema — analytical DBs (Redshift, BigQuery, Snowflake).

**Q37. What is a cursor?**
> Row-by-row processing mechanism in stored procedures. Avoid when possible — set-based operations are faster. Use when row-by-row logic absolutely required.

**Q38. Explain optimistic vs pessimistic locking.**
> Pessimistic: Lock row before reading (`SELECT ... FOR UPDATE`). No one else can modify. OLTP, high conflict. Optimistic: Read without lock, check version before write, retry if changed. Lower contention, better performance for read-heavy.

**Q39. What is a covering index scan?**
> When all required columns are in the index itself — no table lookup needed. Most efficient. Create composite index including all SELECT + WHERE + ORDER BY columns.

**Q40. Star Schema vs Snowflake Schema?**
> Star Schema: Central fact table + denormalized dimension tables (faster queries, more storage). Snowflake Schema: Normalized dimension tables (less storage, more JOINs, slightly slower). Star preferred for analytics.

---

### 💼 SCENARIO-BASED QUESTIONS

**Q41. Production database slow hai — kya karoge?**
> 1. Slow query log enable karo, 2. EXPLAIN top slow queries on, 3. Missing indexes identify karo, 4. Connection pool check karo, 5. Lock contention check (`SHOW PROCESSLIST`), 6. Hardware metrics (CPU, RAM, I/O), 7. Query cache effectiveness, 8. Consider read replica for reporting.

**Q42. 100 million row table pe query optimize karo.**
> Partitioning by date/range, appropriate indexes, avoid SELECT *, pagination with keyset instead of OFFSET, consider columnar store (ClickHouse/BigQuery), materialized views for aggregations, archiving old data.

**Q43. How would you design a URL shortener database?**

```sql
CREATE TABLE short_urls (
    id          BIGINT PRIMARY KEY AUTO_INCREMENT,
    short_code  VARCHAR(10) UNIQUE NOT NULL,
    long_url    TEXT NOT NULL,
    user_id     INT,
    created_at  TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expires_at  TIMESTAMP,
    click_count BIGINT DEFAULT 0,
    INDEX idx_short_code (short_code),
    INDEX idx_user_created (user_id, created_at)
);

CREATE TABLE url_clicks (
    id          BIGINT AUTO_INCREMENT PRIMARY KEY,
    short_code  VARCHAR(10),
    clicked_at  TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    ip_address  VARCHAR(45),
    user_agent  TEXT,
    referrer    TEXT,
    INDEX idx_code_time (short_code, clicked_at)
) PARTITION BY RANGE (YEAR(clicked_at)) (...);
```

**Q44. Design an e-commerce order management schema.**

```sql
CREATE TABLE customers (customer_id INT PK, name, email, phone, address_json JSON);
CREATE TABLE products (product_id INT PK, name, price, stock, category_id);
CREATE TABLE orders (order_id INT PK, customer_id FK, order_date, status ENUM, total);
CREATE TABLE order_items (item_id INT PK, order_id FK, product_id FK, qty, unit_price);
CREATE TABLE payments (payment_id INT PK, order_id FK, amount, method, status, paid_at);
CREATE TABLE shipping (ship_id INT PK, order_id FK, address, carrier, tracking, delivered_at);
```

---

## 🏋️ HANDS-ON PRACTICE EXERCISES

### Exercise 1: Employee Analysis
```sql
-- Setup
CREATE TABLE emp_practice (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(50), dept VARCHAR(30),
    salary DECIMAL(8,2), manager_id INT, hire_date DATE
);
INSERT INTO emp_practice (name, dept, salary, manager_id, hire_date) VALUES
('Alice','Engineering',95000,NULL,'2018-01-15'),
('Bob','Engineering',85000,1,'2019-03-20'),
('Carol','Marketing',75000,NULL,'2019-06-10'),
('Dave','Engineering',80000,1,'2020-09-05'),
('Eve','Marketing',70000,3,'2021-02-28'),
('Frank','HR',65000,NULL,'2020-11-12'),
('Grace','Engineering',90000,1,'2018-07-22'),
('Heidi','Marketing',72000,3,'2022-01-08');

-- Tasks:
-- 1. Each dept ka avg salary nikalo
-- 2. Highest paid employee in each dept
-- 3. Employees earning more than company average
-- 4. Manager ka naam aur unke reportees
-- 5. Dept mein top 2 earners
-- 6. Running total of salaries by hire_date
-- 7. Each employee ka salary vs dept avg difference
```

### Solutions
```sql
-- 1. Dept avg salary
SELECT dept, ROUND(AVG(salary), 2) AS avg_sal FROM emp_practice GROUP BY dept;

-- 2. Highest paid per dept
SELECT dept, name, salary FROM emp_practice
WHERE (dept, salary) IN (SELECT dept, MAX(salary) FROM emp_practice GROUP BY dept);

-- 3. Above company average
SELECT name, salary FROM emp_practice WHERE salary > (SELECT AVG(salary) FROM emp_practice);

-- 4. Manager and reportees
SELECT m.name AS manager, e.name AS employee
FROM emp_practice e JOIN emp_practice m ON e.manager_id = m.id;

-- 5. Top 2 earners per dept
WITH ranked AS (SELECT *, DENSE_RANK() OVER (PARTITION BY dept ORDER BY salary DESC) rk FROM emp_practice)
SELECT dept, name, salary FROM ranked WHERE rk <= 2;

-- 6. Running total
SELECT name, hire_date, salary, SUM(salary) OVER (ORDER BY hire_date) AS running_total FROM emp_practice;

-- 7. Salary vs dept avg
SELECT name, dept, salary, AVG(salary) OVER (PARTITION BY dept) AS dept_avg,
       salary - AVG(salary) OVER (PARTITION BY dept) AS diff FROM emp_practice;
```

---

## 📖 REFERENCE CHEAT SHEET

```
JOIN Types:          INNER, LEFT, RIGHT, FULL OUTER, CROSS, SELF
Window Functions:    ROW_NUMBER, RANK, DENSE_RANK, NTILE, LAG, LEAD,
                     FIRST_VALUE, LAST_VALUE, NTH_VALUE,
                     SUM/AVG/COUNT/MIN/MAX (as window)
Aggregate Fns:       COUNT, SUM, AVG, MIN, MAX, GROUP_CONCAT
String Fns:          CONCAT, UPPER, LOWER, TRIM, LENGTH, SUBSTRING,
                     REPLACE, LEFT, RIGHT, LPAD, RPAD, INSTR
Date Fns:            NOW, CURDATE, DATE_ADD, DATE_SUB, DATEDIFF,
                     DATE_FORMAT, EXTRACT, YEAR, MONTH, DAY
Conditional:         CASE WHEN, IF, IFNULL, COALESCE, NULLIF
Set Operations:      UNION, UNION ALL, INTERSECT, EXCEPT/MINUS
Constraints:         PRIMARY KEY, FOREIGN KEY, UNIQUE, NOT NULL,
                     CHECK, DEFAULT
Isolation Levels:    READ UNCOMMITTED → READ COMMITTED →
                     REPEATABLE READ → SERIALIZABLE
```

---

*📌 Note: MySQL syntax used as default. Differences in PostgreSQL/SQL Server noted where relevant.*
*💡 Practice these queries hands-on — SQL sirf padhne se nahi aata, likhne se aata hai!*
*🎯 3 years ke experience ke liye: Window functions, CTEs, Query optimization, Schema design — ye 4 areas mein strong raho.*
