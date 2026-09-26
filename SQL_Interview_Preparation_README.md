# SQL Interview Preparation Report

I’ll use **W3Schools SQL Tutorial as the primary reference**, but I’ll organize the material specifically for **technical interviews**, not simply reproduce the tutorial. W3Schools currently covers SQL syntax, querying, filtering, aggregation, joins, set operations, grouping, subqueries, `CASE`, NULL handling, database/table operations, constraints, indexes, views, stored procedures, dates, SQL injection, prepared statements, and data types. ([w3schools.com](https://www.w3schools.com/sql/))

[W3Schools SQL Tutorial](https://www.w3schools.com/sql/?utm_source=chatgpt.com)

---

# PART 1 — SQL INTERVIEW ROADMAP

For interviews, I recommend learning SQL in this order:

| Level | Topics | Interview Importance |
|---|---|---|
| 1 | Database basics, tables, rows, columns | ⭐⭐⭐ |
| 2 | `SELECT`, `WHERE`, `ORDER BY`, `DISTINCT` | ⭐⭐⭐⭐⭐ |
| 3 | `AND`, `OR`, `NOT`, `IN`, `BETWEEN`, `LIKE` | ⭐⭐⭐⭐⭐ |
| 4 | Aggregate functions | ⭐⭐⭐⭐⭐ |
| 5 | `GROUP BY`, `HAVING` | ⭐⭐⭐⭐⭐ |
| 6 | Joins | ⭐⭐⭐⭐⭐ |
| 7 | Subqueries | ⭐⭐⭐⭐⭐ |
| 8 | `CASE`, NULL handling | ⭐⭐⭐⭐⭐ |
| 9 | Set operations | ⭐⭐⭐⭐ |
| 10 | Constraints and keys | ⭐⭐⭐⭐⭐ |
| 11 | DDL/DML/DQL/TCL concepts | ⭐⭐⭐⭐ |
| 12 | Views, indexes | ⭐⭐⭐⭐ |
| 13 | Transactions and ACID | ⭐⭐⭐⭐⭐ |
| 14 | Normalization | ⭐⭐⭐⭐⭐ |
| 15 | CTEs | ⭐⭐⭐⭐⭐ |
| 16 | Window functions | ⭐⭐⭐⭐⭐ |
| 17 | Query optimization | ⭐⭐⭐⭐ |
| 18 | Stored procedures/triggers | ⭐⭐⭐ |
| 19 | SQL security | ⭐⭐⭐⭐ |
| 20 | Complex interview problems | ⭐⭐⭐⭐⭐ |

W3Schools itself organizes the core tutorial around `SELECT`, filtering, aggregation, joins, grouping, `HAVING`, `EXISTS`, `ANY`, `ALL`, `CASE`, NULL functions and database-management topics such as constraints, indexes and views. ([w3schools.com](https://www.w3schools.com/sql/))

---

# PART 2 — SQL FUNDAMENTALS

## 1. What is SQL?

**SQL = Structured Query Language.**

It is used to:

- retrieve data
- insert data
- update data
- delete data
- create tables
- modify database structures
- define relationships
- aggregate/analyze data

Example:

```sql
SELECT name, salary
FROM employees
WHERE salary > 50000;
```

Conceptually:

```text
Database
   |
   +-- Employees
   |      |
   |      +-- employee_id
   |      +-- name
   |      +-- salary
   |
   +-- Departments
          |
          +-- department_id
          +-- department_name
```

W3Schools describes SQL as a standard language for storing, manipulating and retrieving data and demonstrates queries such as `SELECT * FROM Customers`. ([w3schools.com](https://www.w3schools.com/Sql/?utm_source=chatgpt.com))

---

# 2. Database vs Table

### Database

A database is a collection of related data.

### Table

A table stores data in rows and columns.

Example:

### Employees

| id | name | department | salary |
|---:|---|---|---:|
| 1 | A | IT | 60000 |
| 2 | B | HR | 50000 |
| 3 | C | IT | 70000 |

- **Row** → one record
- **Column** → one attribute
- **Table** → collection of related records
- **Database** → collection of tables

---

# 3. SQL Command Categories

This is a **very common interview question**.

## DDL — Data Definition Language

Used to define database structure.

```sql
CREATE
ALTER
DROP
TRUNCATE
```

Example:

```sql
CREATE TABLE employees (
    id INT,
    name VARCHAR(100),
    salary DECIMAL(10,2)
);
```

---

## DML — Data Manipulation Language

Used to modify data.

```sql
INSERT
UPDATE
DELETE
```

Example:

```sql
INSERT INTO employees
VALUES (1, 'Rahul', 60000);
```

---

## DQL — Data Query Language

Primarily:

```sql
SELECT
```

Example:

```sql
SELECT * FROM employees;
```

---

## DCL — Data Control Language

Used for permissions.

```sql
GRANT
REVOKE
```

---

## TCL — Transaction Control Language

Used to manage transactions.

```sql
COMMIT
ROLLBACK
SAVEPOINT
```

### Interview question

**Q: Difference between DELETE, DROP and TRUNCATE?**

| DELETE | TRUNCATE | DROP |
|---|---|---|
| Removes rows | Removes all rows | Removes table |
| `WHERE` possible | Generally no `WHERE` | Table structure removed |
| DML | DDL in many DBMSs | DDL |
| Table remains | Table remains | Table gone |

Example:

```sql
DELETE FROM employees
WHERE id = 5;
```

```sql
TRUNCATE TABLE employees;
```

```sql
DROP TABLE employees;
```

Exact transaction/rollback behavior can differ by DBMS, so in interviews specify whether you're talking about MySQL, PostgreSQL, SQL Server, etc.

---

# PART 3 — SELECT

The most fundamental SQL command.

```sql
SELECT column1, column2
FROM table_name;
```

Example:

```sql
SELECT name, salary
FROM employees;
```

All columns:

```sql
SELECT *
FROM employees;
```

W3Schools uses `SELECT * FROM Customers` as its basic SQL example. ([w3schools.com](https://www.w3schools.com/Sql/?utm_source=chatgpt.com))

---

# 4. DISTINCT

Removes duplicate results.

```sql
SELECT DISTINCT department
FROM employees;
```

If employees are:

```text
IT
IT
HR
Finance
Finance
```

Result:

```text
IT
HR
Finance
```

---

# 5. WHERE

Filters individual rows.

```sql
SELECT *
FROM employees
WHERE salary > 50000;
```

Important:

```sql
WHERE salary > 50000
```

filters rows **before grouping**.

---

# 6. Comparison Operators

```text
=       equal
<>      not equal
!=      not equal
>       greater
<       smaller
>=      greater/equal
<=      smaller/equal
```

Example:

```sql
SELECT *
FROM employees
WHERE salary >= 60000;
```

---

# 7. AND / OR / NOT

### AND

Both conditions must be true.

```sql
SELECT *
FROM employees
WHERE department = 'IT'
AND salary > 60000;
```

### OR

At least one condition.

```sql
SELECT *
FROM employees
WHERE department = 'IT'
OR department = 'HR';
```

### NOT

Negates a condition.

```sql
SELECT *
FROM employees
WHERE NOT department = 'HR';
```

---

# 8. IN

Instead of:

```sql
WHERE department = 'IT'
OR department = 'HR'
OR department = 'Finance'
```

use:

```sql
WHERE department IN ('IT', 'HR', 'Finance');
```

---

# 9. BETWEEN

Used for ranges.

```sql
SELECT *
FROM employees
WHERE salary BETWEEN 50000 AND 80000;
```

Important interview point:

`BETWEEN` is generally **inclusive of both endpoints**.

---

# 10. LIKE

Used for pattern matching.

### Starts with A

```sql
WHERE name LIKE 'A%'
```

### Ends with A

```sql
WHERE name LIKE '%A'
```

### Contains A

```sql
WHERE name LIKE '%A%'
```

### Exactly one character before A

```sql
WHERE name LIKE '_A%'
```

---

# 11. Wildcards

W3Schools covers `LIKE` and wildcard pattern matching as separate SQL topics. ([w3schools.com](https://www.w3schools.com/sql/))

### `%`

Zero or more characters.

```sql
'A%'
```

### `_`

Exactly one character.

```sql
'_A%'
```

---

# PART 4 — ORDER BY

Used for sorting.

```sql
SELECT *
FROM employees
ORDER BY salary;
```

Default:

```text
ASC
```

Descending:

```sql
SELECT *
FROM employees
ORDER BY salary DESC;
```

Multiple columns:

```sql
SELECT *
FROM employees
ORDER BY department ASC, salary DESC;
```

---

# PART 5 — ALIAS

Used to give temporary names.

```sql
SELECT
    name AS employee_name,
    salary AS annual_salary
FROM employees;
```

Table alias:

```sql
SELECT e.name
FROM employees e;
```

This becomes extremely important with joins.

---

# PART 6 — NULL

`NULL` means **missing/unknown value**.

It is NOT:

```text
0
''
'NULL'
FALSE
```

Wrong:

```sql
WHERE salary = NULL
```

Correct:

```sql
WHERE salary IS NULL;
```

And:

```sql
WHERE salary IS NOT NULL;
```

### Very common interview question

**Why doesn't `salary = NULL` work?**

Because `NULL` represents an unknown value. Comparisons using `=` do not produce TRUE for NULL; use `IS NULL` / `IS NOT NULL`.

---

# PART 7 — AGGREGATE FUNCTIONS

W3Schools specifically covers:

```text
MIN()
MAX()
COUNT()
SUM()
AVG()
```

as core SQL aggregation functions. ([w3schools.com](https://www.w3schools.com/sql/))

---

## COUNT

```sql
SELECT COUNT(*)
FROM employees;
```

Count non-null values:

```sql
SELECT COUNT(salary)
FROM employees;
```

### Important difference

```sql
COUNT(*)
```

counts rows.

```sql
COUNT(column)
```

counts non-NULL values in that column.

---

## SUM

```sql
SELECT SUM(salary)
FROM employees;
```

---

## AVG

```sql
SELECT AVG(salary)
FROM employees;
```

---

## MIN

```sql
SELECT MIN(salary)
FROM employees;
```

---

## MAX

```sql
SELECT MAX(salary)
FROM employees;
```

---

# PART 8 — GROUP BY

This is one of the **most important SQL interview concepts**.

Suppose:

| employee | department | salary |
|---|---|---:|
| A | IT | 60000 |
| B | IT | 70000 |
| C | HR | 50000 |
| D | HR | 55000 |

Question:

> Find average salary by department.

```sql
SELECT department, AVG(salary)
FROM employees
GROUP BY department;
```

Result:

| department | avg |
|---|---:|
| IT | 65000 |
| HR | 52500 |

W3Schools demonstrates `GROUP BY` with aggregate functions such as `COUNT()` and also shows grouping combined with joins. ([w3schools.com](https://www.w3schools.com/sql/sql_Groupby.asp?utm_source=chatgpt.com))

---

# PART 9 — HAVING

This is frequently confused with `WHERE`.

### WHERE

Filters **rows**.

### HAVING

Filters **groups after aggregation**.

W3Schools explicitly distinguishes them this way: `WHERE` filters individual rows before grouping, while `HAVING` filters groups after aggregation. ([w3schools.com](https://www.w3schools.com/sql/sql_having.asp?utm_source=chatgpt.com))

Example:

```sql
SELECT department, AVG(salary) AS avg_salary
FROM employees
GROUP BY department
HAVING AVG(salary) > 60000;
```

---

## WHERE + GROUP BY + HAVING

```sql
SELECT department, AVG(salary)
FROM employees
WHERE salary > 40000
GROUP BY department
HAVING AVG(salary) > 60000;
```

Think:

```text
Raw rows
   ↓
WHERE
   ↓
GROUP BY
   ↓
Aggregate
   ↓
HAVING
   ↓
Result
```

---

# PART 10 — JOINS

This is arguably the **single most important SQL interview topic**.

A JOIN combines rows from multiple tables based on related columns. W3Schools defines JOINs this way and covers INNER, LEFT, RIGHT, FULL and SELF JOINs. ([w3schools.com](https://www.w3schools.com/Sql/sql_join.asp?utm_source=chatgpt.com))

Suppose:

### Employees

| id | name | dept_id |
|---:|---|---:|
| 1 | A | 10 |
| 2 | B | 20 |
| 3 | C | 30 |

### Departments

| dept_id | department |
|---:|---|
| 10 | IT |
| 20 | HR |
| 40 | Finance |

---

# 11. INNER JOIN

Returns matching rows from both tables.

```sql
SELECT e.name, d.department
FROM employees e
INNER JOIN departments d
ON e.dept_id = d.dept_id;
```

Result:

```text
A  IT
B  HR
```

W3Schools notes that `JOIN` without a specified type is equivalent to `INNER JOIN` in this context. ([w3schools.com](https://www.w3schools.com/sql/sql_join_inner.asp?sid=8tX8Ef&utm_source=chatgpt.com))

---

# 12. LEFT JOIN

Returns **all rows from the left table**.

```sql
SELECT e.name, d.department
FROM employees e
LEFT JOIN departments d
ON e.dept_id = d.dept_id;
```

Result:

```text
A    IT
B    HR
C    NULL
```

W3Schools explicitly notes that unmatched right-side values become NULL. ([w3schools.com](https://www.w3schools.com/sql/sql_ref_join.asp?utm_source=chatgpt.com))

---

# 13. RIGHT JOIN

All rows from right table.

```sql
SELECT e.name, d.department
FROM employees e
RIGHT JOIN departments d
ON e.dept_id = d.dept_id;
```

Result includes Finance even though no employee belongs to it.

---

# 14. FULL OUTER JOIN

Returns all rows from both sides.

Conceptually:

```text
matching rows
+
left-only rows
+
right-only rows
```

Note: **MySQL does not natively support `FULL OUTER JOIN`**, so in MySQL you commonly emulate it using `LEFT JOIN` + `RIGHT JOIN`/`UNION`, depending on the exact requirement.

---

# 15. SELF JOIN

Joining a table with itself.

Example employee-manager relationship:

| id | name | manager_id |
|---:|---|---:|
| 1 | CEO | NULL |
| 2 | A | 1 |
| 3 | B | 1 |

```sql
SELECT
    e.name AS employee,
    m.name AS manager
FROM employees e
LEFT JOIN employees m
ON e.manager_id = m.id;
```

---

# PART 11 — JOIN INTERVIEW TRICK

Question:

> Find employees who don't belong to any department.

Use:

```sql
SELECT e.*
FROM employees e
LEFT JOIN departments d
ON e.dept_id = d.dept_id
WHERE d.dept_id IS NULL;
```

This pattern is **extremely important**.

It is called an **anti-join pattern**.

---

# PART 12 — UNION vs UNION ALL

## UNION

Combines result sets and removes duplicates.

```sql
SELECT city FROM customers
UNION
SELECT city FROM suppliers;
```

## UNION ALL

Keeps duplicates.

```sql
SELECT city FROM customers
UNION ALL
SELECT city FROM suppliers;
```

### Interview question

**Which is faster?**

Generally `UNION ALL`, because it doesn't need duplicate elimination.

---

# PART 13 — SUBQUERIES

A query inside another query.

Example:

> Find employees earning more than the average salary.

```sql
SELECT *
FROM employees
WHERE salary >
(
    SELECT AVG(salary)
    FROM employees
);
```

---

## Subquery in WHERE

```sql
SELECT *
FROM employees
WHERE department_id IN
(
    SELECT department_id
    FROM departments
    WHERE location = 'Delhi'
);
```

---

## Subquery in FROM

```sql
SELECT *
FROM
(
    SELECT department, AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department
) x
WHERE avg_salary > 60000;
```

---

## Correlated Subquery

The inner query depends on the outer query.

Example:

```sql
SELECT e1.*
FROM employees e1
WHERE salary >
(
    SELECT AVG(e2.salary)
    FROM employees e2
    WHERE e2.department_id = e1.department_id
);
```

Meaning:

> Find employees earning more than the average salary of their own department.

This is a classic interview problem.

---

# PART 14 — EXISTS

Checks whether a subquery returns at least one row.

```sql
SELECT *
FROM customers c
WHERE EXISTS
(
    SELECT 1
    FROM orders o
    WHERE o.customer_id = c.customer_id
);
```

Meaning:

> Find customers who have placed at least one order.

---

# EXISTS vs IN

Very common interview question.

`IN` compares against values.

`EXISTS` checks whether rows exist.

Conceptually:

```sql
WHERE id IN (...)
```

vs

```sql
WHERE EXISTS (...)
```

Performance depends on the DBMS, data distribution, indexes and query plan. Don't say "EXISTS is always faster" in an interview.

---

# PART 15 — CASE

`CASE` is SQL's conditional expression.

W3Schools describes `CASE` as producing different outputs based on conditions. ([w3schools.com](https://www.w3schools.com/sql/sql_ref_case.asp?utm_source=chatgpt.com))

Example:

```sql
SELECT
    name,
    salary,
    CASE
        WHEN salary >= 100000 THEN 'High'
        WHEN salary >= 60000 THEN 'Medium'
        ELSE 'Low'
    END AS salary_category
FROM employees;
```

---

# CASE + aggregation

Very important for interviews.

### Count employees with salary > 50K

```sql
SELECT
    SUM(
        CASE
            WHEN salary > 50000 THEN 1
            ELSE 0
        END
    ) AS high_salary_count
FROM employees;
```

---

# PART 16 — NULL FUNCTIONS

Useful functions include:

```sql
COALESCE()
IFNULL()       -- MySQL
NULLIF()
```

Example:

```sql
SELECT
    name,
    COALESCE(phone, 'Not Available')
FROM employees;
```

`COALESCE()` returns the first non-NULL expression.

---

# PART 17 — DATE FUNCTIONS

For interviews, know:

```text
CURRENT_DATE
CURRENT_TIMESTAMP
YEAR()
MONTH()
DAY()
DATEDIFF()
DATE_ADD()
DATE_SUB()
```

Example:

```sql
SELECT *
FROM orders
WHERE order_date >= '2026-01-01';
```

Monthly aggregation:

```sql
SELECT
    YEAR(order_date),
    MONTH(order_date),
    COUNT(*) AS orders
FROM orders
GROUP BY YEAR(order_date), MONTH(order_date);
```

W3Schools' reference includes a broad collection of date functions such as `DATEDIFF`, `DATE_ADD`, `DATE_SUB`, `YEAR`, `MONTH`, `DAY`, `NOW` and `CURRENT_DATE`. ([w3schools.com](https://www.w3schools.com/sql/))

---

# PART 18 — STRING FUNCTIONS

Important ones:

```text
UPPER()
LOWER()
LENGTH()
TRIM()
CONCAT()
SUBSTRING()
REPLACE()
```

Example:

```sql
SELECT UPPER(name)
FROM employees;
```

```sql
SELECT CONCAT(first_name, ' ', last_name) AS full_name
FROM employees;
```

---

# PART 19 — CONSTRAINTS

Constraints enforce rules on table data.

W3Schools identifies the common constraints as `NOT NULL`, `UNIQUE`, `PRIMARY KEY`, `FOREIGN KEY`, `CHECK`, and `DEFAULT`. ([w3schools.com](https://www.w3schools.com/sql/sql_constraints.asp?utm_source=chatgpt.com))

---

## NOT NULL

```sql
name VARCHAR(100) NOT NULL
```

Prevents NULL.

---

## UNIQUE

```sql
email VARCHAR(255) UNIQUE
```

Prevents duplicate values.

---

## PRIMARY KEY

Uniquely identifies a row.

```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    name VARCHAR(100)
);
```

A primary key is essentially a uniqueness + non-null requirement, subject to DBMS implementation details.

---

# 20. FOREIGN KEY

Creates a relationship.

```sql
CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(100)
);
```

```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    name VARCHAR(100),
    department_id INT,

    FOREIGN KEY (department_id)
    REFERENCES departments(department_id)
);
```

---

# 21. CHECK

```sql
salary DECIMAL(10,2)
CHECK (salary >= 0)
```

Ensures the condition is satisfied.

---

# 22. DEFAULT

```sql
status VARCHAR(20) DEFAULT 'Active'
```

If no value is provided:

```text
Active
```

is inserted.

---

# PART 20 — PRIMARY KEY vs UNIQUE KEY

| Primary Key | UNIQUE |
|---|---|
| Identifies row | Enforces uniqueness |
| Cannot be NULL | NULL behavior depends on DBMS |
| One primary key constraint per table | Multiple UNIQUE constraints possible |
| Used as primary identifier | Used for alternate unique values |

---

# PART 21 — INDEXES

Indexes help databases locate rows efficiently.

Example:

```sql
CREATE INDEX idx_employee_name
ON employees(name);
```

W3Schools includes `CREATE INDEX` as a database topic and describes indexes as a mechanism intended to speed retrieval. ([w3schools.com](https://www.w3schools.com/sql/sql_constraints.asp?utm_source=chatgpt.com))

### Why indexes?

Without an appropriate index, a query may need to scan many rows.

With an index:

```text
Query
  ↓
Index
  ↓
Relevant rows
```

### But indexes have a cost.

They:

- consume storage
- increase write overhead
- must be maintained during `INSERT`
- must be maintained during `UPDATE`
- must be maintained during `DELETE`

Therefore:

> **Don't index every column.**

---

# PART 22 — VIEW

A view is a stored query definition that behaves like a virtual table.

```sql
CREATE VIEW employee_view AS
SELECT name, department, salary
FROM employees
WHERE salary > 60000;
```

Then:

```sql
SELECT *
FROM employee_view;
```

Useful for:

- abstraction
- reusable queries
- security
- simplifying complex queries

W3Schools includes views among its SQL database topics. ([w3schools.com](https://www.w3schools.com/sql/))

---

# PART 23 — TRANSACTIONS

A transaction is a logical unit of work.

Example:

Bank transfer:

```text
Account A - ₹1000
Account B + ₹1000
```

Both operations should succeed, or neither should happen.

```sql
START TRANSACTION;

UPDATE accounts
SET balance = balance - 1000
WHERE id = 1;

UPDATE accounts
SET balance = balance + 1000
WHERE id = 2;

COMMIT;
```

If something fails:

```sql
ROLLBACK;
```

---

# PART 24 — ACID

Extremely important.

## A — Atomicity

All operations happen or none happen.

## C — Consistency

Database moves from one valid state to another valid state.

## I — Isolation

Concurrent transactions should not improperly interfere with each other.

## D — Durability

Committed changes survive failures according to the database's durability guarantees.

### Interview question

**Explain ACID using a bank transaction.**

Use the transfer example above.

---

# PART 25 — NORMALIZATION

Very common in SQL/DBMS interviews.

Goal:

> Reduce unnecessary redundancy and prevent anomalies.

---

## 1NF

Each cell contains atomic values.

Bad:

| id | phones |
|---|---|
| 1 | 9876, 1234 |

Better:

| id | phone |
|---|---|
| 1 | 9876 |
| 1 | 1234 |

---

## 2NF

Must be in 1NF and have no **partial dependency** on part of a composite key.

---

## 3NF

Must be in 2NF and remove **transitive dependency**.

Example:

```text
EmployeeID → DepartmentID
DepartmentID → DepartmentName
```

Then:

```text
EmployeeID → DepartmentName
```

indirectly.

Separate department information into its own table.

---

## Denormalization

Sometimes intentionally introduces redundancy for performance/read efficiency.

### Interview question

**Normalization vs denormalization?**

Normalization:

```text
less redundancy
better consistency
more joins
```

Denormalization:

```text
more redundancy
fewer joins
potentially faster reads
more storage/update complexity
```

---

# PART 26 — CTE

CTE = Common Table Expression.

Syntax:

```sql
WITH department_salary AS
(
    SELECT department_id, AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department_id
)
SELECT *
FROM department_salary
WHERE avg_salary > 60000;
```

Advantages:

- readability
- reusable within a statement
- useful for complex queries
- recursive queries

---

# PART 27 — WINDOW FUNCTIONS

**Important addition for modern SQL interviews.**

W3Schools' main SQL tutorial list emphasizes aggregation, joins, grouping, etc.; for interviews you should additionally learn window functions because they solve many ranking and running-total problems without collapsing rows. ([w3schools.com](https://www.w3schools.com/sql/))

Basic syntax:

```sql
function() OVER (
    PARTITION BY ...
    ORDER BY ...
)
```

---

## ROW_NUMBER

```sql
SELECT
    name,
    department,
    salary,
    ROW_NUMBER() OVER (
        PARTITION BY department
        ORDER BY salary DESC
    ) AS rn
FROM employees;
```

Ranks employees within each department.

---

## RANK

```sql
RANK() OVER (
    ORDER BY salary DESC
)
```

If salaries are:

```text
100
100
90
```

Ranks:

```text
1
1
3
```

---

## DENSE_RANK

```text
1
1
2
```

### RANK vs DENSE_RANK

This is a very common interview question.

---

# PART 28 — LEAD AND LAG

### LAG

Access previous row.

```sql
SELECT
    order_date,
    revenue,
    LAG(revenue) OVER (
        ORDER BY order_date
    ) AS previous_revenue
FROM sales;
```

### LEAD

Access next row.

```sql
LEAD(revenue) OVER (
    ORDER BY order_date
)
```

Useful for:

- month-over-month analysis
- comparing current and previous records
- detecting changes

---

# PART 29 — SQL QUERY EXECUTION ORDER

This is **extremely important**.

When you write:

```sql
SELECT department, AVG(salary)
FROM employees
WHERE salary > 50000
GROUP BY department
HAVING AVG(salary) > 60000
ORDER BY AVG(salary) DESC;
```

Conceptual logical execution order is approximately:

```text
FROM
  ↓
JOIN
  ↓
WHERE
  ↓
GROUP BY
  ↓
HAVING
  ↓
SELECT
  ↓
DISTINCT
  ↓
ORDER BY
  ↓
LIMIT/OFFSET
```

This explains many interview questions.

For example:

> Why can't I normally use an aggregate in WHERE?

Because aggregation logically happens after the row-level filtering stage.

Use:

```sql
HAVING
```

instead.

---

# PART 30 — CODING SECTION

Now let's move to the part you should actually practice.

I'll use this schema throughout.

## Employees

```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    name VARCHAR(100),
    department_id INT,
    salary DECIMAL(10,2),
    manager_id INT,
    joining_date DATE
);
```

## Departments

```sql
CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(100)
);
```

## Projects

```sql
CREATE TABLE projects (
    project_id INT PRIMARY KEY,
    project_name VARCHAR(100),
    department_id INT
);
```

## Employee Projects

```sql
CREATE TABLE employee_projects (
    employee_id INT,
    project_id INT,
    PRIMARY KEY(employee_id, project_id)
);
```

---

# LEVEL 1 — BASIC SQL QUESTIONS

## Q1. Display all employees.

```sql
SELECT *
FROM employees;
```

---

## Q2. Display employee names and salaries.

```sql
SELECT name, salary
FROM employees;
```

---

## Q3. Employees earning more than 60,000.

```sql
SELECT *
FROM employees
WHERE salary > 60000;
```

---

## Q4. Employees in IT department

Assuming department ID 10:

```sql
SELECT *
FROM employees
WHERE department_id = 10;
```

---

## Q5. Sort employees by salary descending.

```sql
SELECT *
FROM employees
ORDER BY salary DESC;
```

---

## Q6. Find unique departments.

```sql
SELECT DISTINCT department_id
FROM employees;
```

---

# LEVEL 2 — AGGREGATION

## Q7. Count employees.

```sql
SELECT COUNT(*)
FROM employees;
```

---

## Q8. Find maximum salary.

```sql
SELECT MAX(salary)
FROM employees;
```

---

## Q9. Find average salary.

```sql
SELECT AVG(salary)
FROM employees;
```

---

## Q10. Total salary expenditure.

```sql
SELECT SUM(salary)
FROM employees;
```

---

# LEVEL 3 — GROUP BY

## Q11. Number of employees per department.

```sql
SELECT
    department_id,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department_id;
```

---

## Q12. Average salary per department.

```sql
SELECT
    department_id,
    AVG(salary) AS avg_salary
FROM employees
GROUP BY department_id;
```

---

## Q13. Departments with more than 5 employees.

```sql
SELECT
    department_id,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department_id
HAVING COUNT(*) > 5;
```

---

# LEVEL 4 — JOINS

## Q14. Employee name + department name

```sql
SELECT
    e.name,
    d.department_name
FROM employees e
JOIN departments d
ON e.department_id = d.department_id;
```

---

## Q15. Show all employees, including employees without departments.

```sql
SELECT
    e.name,
    d.department_name
FROM employees e
LEFT JOIN departments d
ON e.department_id = d.department_id;
```

---

## Q16. Find employees without a valid department.

```sql
SELECT e.*
FROM employees e
LEFT JOIN departments d
ON e.department_id = d.department_id
WHERE d.department_id IS NULL;
```

---

# LEVEL 5 — SECOND-HIGHEST SALARY

One of the **classic SQL interview questions**.

## Method 1 — DISTINCT + LIMIT

MySQL:

```sql
SELECT DISTINCT salary
FROM employees
ORDER BY salary DESC
LIMIT 1 OFFSET 1;
```

---

## Method 2 — MAX

```sql
SELECT MAX(salary)
FROM employees
WHERE salary <
(
    SELECT MAX(salary)
    FROM employees
);
```

This returns the second-highest **distinct** salary.

---

# Q18. Nth highest salary

Using a window function:

```sql
SELECT salary
FROM
(
    SELECT
        salary,
        DENSE_RANK() OVER (
            ORDER BY salary DESC
        ) AS rnk
    FROM employees
) x
WHERE rnk = 3;
```

This gives the third-highest distinct salary.

---

# Q19. Highest-paid employee

```sql
SELECT *
FROM employees
WHERE salary =
(
    SELECT MAX(salary)
    FROM employees
);
```

---

# Q20. Highest-paid employee in each department

```sql
SELECT *
FROM
(
    SELECT
        e.*,
        DENSE_RANK() OVER (
            PARTITION BY department_id
            ORDER BY salary DESC
        ) AS rnk
    FROM employees e
) x
WHERE rnk = 1;
```

---

# Q21. Employees earning above department average

```sql
SELECT *
FROM
(
    SELECT
        e.*,
        AVG(salary) OVER (
            PARTITION BY department_id
        ) AS dept_avg
    FROM employees e
) x
WHERE salary > dept_avg;
```

This is a **very good interview problem**.

---

# LEVEL 6 — DUPLICATES

## Q22. Find duplicate names.

```sql
SELECT
    name,
    COUNT(*) AS cnt
FROM employees
GROUP BY name
HAVING COUNT(*) > 1;
```

---

# Q23. Delete duplicate records

This is DBMS-specific and should be handled carefully.

A common pattern using `ROW_NUMBER()`:

```sql
WITH duplicates AS
(
    SELECT
        employee_id,
        ROW_NUMBER() OVER (
            PARTITION BY name, department_id, salary
            ORDER BY employee_id
        ) AS rn
    FROM employees
)
DELETE FROM employees
WHERE employee_id IN
(
    SELECT employee_id
    FROM duplicates
    WHERE rn > 1
);
```

Before actually deleting data, first run the corresponding `SELECT`.

---

# LEVEL 7 — DATE QUESTIONS

## Q24. Employees who joined after 2025-01-01

```sql
SELECT *
FROM employees
WHERE joining_date > '2025-01-01';
```

---

## Q25. Employees who joined in 2025

MySQL:

```sql
SELECT *
FROM employees
WHERE YEAR(joining_date) = 2025;
```

---

# LEVEL 8 — CASE

## Q26. Categorize employees by salary.

```sql
SELECT
    name,
    salary,
    CASE
        WHEN salary >= 100000 THEN 'High'
        WHEN salary >= 60000 THEN 'Medium'
        ELSE 'Low'
    END AS category
FROM employees;
```

---

# LEVEL 9 — EXISTS

## Q27. Find employees assigned to at least one project.

```sql
SELECT *
FROM employees e
WHERE EXISTS
(
    SELECT 1
    FROM employee_projects ep
    WHERE ep.employee_id = e.employee_id
);
```

---

# LEVEL 10 — MULTIPLE JOINS

## Q28. Employee + department + project

```sql
SELECT
    e.name,
    d.department_name,
    p.project_name
FROM employees e
JOIN departments d
    ON e.department_id = d.department_id
JOIN employee_projects ep
    ON e.employee_id = ep.employee_id
JOIN projects p
    ON ep.project_id = p.project_id;
```

---

# LEVEL 11 — TOP N PER GROUP

## Q29. Top 3 employees in each department.

```sql
SELECT *
FROM
(
    SELECT
        e.*,
        ROW_NUMBER() OVER (
            PARTITION BY department_id
            ORDER BY salary DESC
        ) AS rn
    FROM employees e
) x
WHERE rn <= 3;
```

If ties should receive the same ranking:

```sql
DENSE_RANK()
```

instead.

---

# LEVEL 12 — RUNNING TOTAL

Suppose:

```text
date       revenue
Jan 1      100
Jan 2      200
Jan 3      300
```

Query:

```sql
SELECT
    order_date,
    revenue,
    SUM(revenue) OVER (
        ORDER BY order_date
    ) AS running_total
FROM sales;
```

Result:

```text
100
300
600
```

---

# LEVEL 13 — MONTH-OVER-MONTH

```sql
SELECT
    month,
    revenue,
    LAG(revenue) OVER (
        ORDER BY month
    ) AS previous_month
FROM monthly_sales;
```

Then:

```sql
SELECT
    month,
    revenue,
    previous_month,
    revenue - previous_month AS change
FROM ...
```

---

# PART 31 — MOST IMPORTANT SQL INTERVIEW QUESTIONS

These are the questions I would make sure you can answer **without looking at notes**.

---

## SQL THEORY QUESTIONS

### 1. What is SQL?

### 2. What is DBMS?

### 3. What is RDBMS?

### 4. SQL vs MySQL?

Important:

```text
SQL = language
MySQL = database management system
```

---

### 5. What is a primary key?

### 6. What is a foreign key?

### 7. Primary key vs unique key?

### 8. What is a composite key?

### 9. What is a candidate key?

### 10. What is a surrogate key?

---

# QUERY QUESTIONS

### 11. WHERE vs HAVING?

**Answer:**

```text
WHERE  → filters rows
HAVING → filters groups
```

---

### 12. GROUP BY vs ORDER BY?

```text
GROUP BY → creates groups
ORDER BY → sorts result
```

---

### 13. WHERE vs ON in JOIN?

This is important.

`ON` defines the matching relationship.

`WHERE` filters the resulting rows.

With outer joins, moving a condition from `ON` to `WHERE` can change the result significantly.

---

### 14. INNER JOIN vs LEFT JOIN?

```text
INNER JOIN
→ only matching records

LEFT JOIN
→ everything from left
→ matching data from right
→ NULL if no match
```

W3Schools explicitly defines these differences. ([w3schools.com](https://www.w3schools.com/Sql/sql_join.asp?utm_source=chatgpt.com))

---

### 15. UNION vs UNION ALL?

```text
UNION
→ removes duplicates

UNION ALL
→ keeps duplicates
```

---

### 16. DELETE vs TRUNCATE vs DROP?

You should be able to explain this immediately.

---

### 17. COUNT(*) vs COUNT(column)?

```text
COUNT(*)      → rows
COUNT(column) → non-NULL column values
```

---

### 18. What is NULL?

Not zero, not empty string and not false.

---

### 19. Why use IS NULL instead of = NULL?

Because NULL represents unknown/missing data and equality comparisons don't test NULL correctly.

---

### 20. What is an index?

A structure used to improve data retrieval, at the cost of storage and write/update overhead.

---

# DATABASE DESIGN QUESTIONS

### 21. What is normalization?

### 22. Explain 1NF, 2NF, 3NF.

### 23. What is denormalization?

### 24. What is referential integrity?

### 25. What is a foreign-key constraint?

---

# TRANSACTION QUESTIONS

### 26. What is a transaction?

### 27. Explain ACID.

### 28. COMMIT vs ROLLBACK?

### 29. What is a deadlock?

### 30. What are transaction isolation levels?

For interviews, know at least:

```text
READ UNCOMMITTED
READ COMMITTED
REPEATABLE READ
SERIALIZABLE
```

---

# ADVANCED SQL QUESTIONS

### 31. What is a subquery?

### 32. What is a correlated subquery?

### 33. EXISTS vs IN?

### 34. What is a CTE?

### 35. What is a recursive CTE?

### 36. What is a window function?

### 37. RANK vs DENSE_RANK vs ROW_NUMBER?

### 38. LEAD vs LAG?

### 39. What is a view?

### 40. What is a stored procedure?

### 41. What is a trigger?

---

# VERY COMMON CODING QUESTIONS

Make sure you can solve these:

### 42. Find second-highest salary.

### 43. Find third-highest salary.

### 44. Find Nth-highest salary.

### 45. Find duplicate records.

### 46. Delete duplicates.

### 47. Find employees earning more than average salary.

### 48. Find employees earning more than department average.

### 49. Find highest salary per department.

### 50. Find top 3 salaries per department.

### 51. Find employees without a department.

### 52. Find departments without employees.

### 53. Find customers who never placed an order.

### 54. Find customers who placed more than 5 orders.

### 55. Find the department with the highest average salary.

### 56. Find the employee with the highest salary.

### 57. Find employees who joined in the last 6 months.

### 58. Calculate running total.

### 59. Calculate month-over-month growth.

### 60. Find consecutive records.

---

# PART 32 — THE 15 SQL QUESTIONS I WOULD PRIORITIZE

If your interview is close, focus especially on these:

## 1. Second highest salary

```sql
SELECT MAX(salary)
FROM employees
WHERE salary < (
    SELECT MAX(salary)
    FROM employees
);
```

---

## 2. Nth highest salary

```sql
SELECT salary
FROM (
    SELECT
        salary,
        DENSE_RANK() OVER (
            ORDER BY salary DESC
        ) AS rnk
    FROM employees
) x
WHERE rnk = N;
```

---

## 3. Duplicate records

```sql
SELECT name, COUNT(*)
FROM employees
GROUP BY name
HAVING COUNT(*) > 1;
```

---

## 4. Employees above average

```sql
SELECT *
FROM employees
WHERE salary > (
    SELECT AVG(salary)
    FROM employees
);
```

---

## 5. Employees above department average

```sql
SELECT *
FROM (
    SELECT
        e.*,
        AVG(salary) OVER (
            PARTITION BY department_id
        ) AS avg_salary
    FROM employees e
) x
WHERE salary > avg_salary;
```

---

## 6. Highest salary by department

```sql
SELECT department_id, MAX(salary)
FROM employees
GROUP BY department_id;
```

---

## 7. Highest-paid employee in every department

```sql
SELECT *
FROM (
    SELECT
        e.*,
        RANK() OVER (
            PARTITION BY department_id
            ORDER BY salary DESC
        ) AS rnk
    FROM employees e
) x
WHERE rnk = 1;
```

---

## 8. Customers with no orders

```sql
SELECT c.*
FROM customers c
LEFT JOIN orders o
ON c.customer_id = o.customer_id
WHERE o.customer_id IS NULL;
```

---

## 9. Departments with more than 5 employees

```sql
SELECT department_id, COUNT(*)
FROM employees
GROUP BY department_id
HAVING COUNT(*) > 5;
```

---

## 10. Top 3 employees per department

```sql
SELECT *
FROM (
    SELECT
        e.*,
        ROW_NUMBER() OVER (
            PARTITION BY department_id
            ORDER BY salary DESC
        ) rn
    FROM employees e
) x
WHERE rn <= 3;
```

---

## 11. Running total

```sql
SELECT
    order_date,
    amount,
    SUM(amount) OVER (
        ORDER BY order_date
    ) AS running_total
FROM orders;
```

---

## 12. Previous record

```sql
SELECT
    order_date,
    amount,
    LAG(amount) OVER (
        ORDER BY order_date
    ) AS previous_amount
FROM orders;
```

---

## 13. Rank employees

```sql
SELECT
    name,
    salary,
    RANK() OVER (
        ORDER BY salary DESC
    ) AS salary_rank
FROM employees;
```

---

## 14. Conditional aggregation

```sql
SELECT
    department_id,
    SUM(
        CASE
            WHEN salary > 60000 THEN 1
            ELSE 0
        END
    ) AS high_paid
FROM employees
GROUP BY department_id;
```

---

## 15. Employees who have projects

```sql
SELECT *
FROM employees e
WHERE EXISTS (
    SELECT 1
    FROM employee_projects ep
    WHERE ep.employee_id = e.employee_id
);
```

---

# PART 33 — SQL INTERVIEW CHEAT SHEET

Memorize this query structure:

```sql
SELECT
    columns
FROM table1
JOIN table2
    ON condition
WHERE row_condition
GROUP BY columns
HAVING group_condition
ORDER BY columns
LIMIT n;
```

And remember the logical flow:

```text
FROM
 ↓
JOIN
 ↓
WHERE
 ↓
GROUP BY
 ↓
HAVING
 ↓
SELECT
 ↓
ORDER BY
 ↓
LIMIT
```

---

# PART 34 — COMMON TRAPS

## Trap 1

Wrong:

```sql
WHERE salary = NULL
```

Correct:

```sql
WHERE salary IS NULL
```

---

## Trap 2

Wrong:

```sql
WHERE COUNT(*) > 5
```

Correct:

```sql
HAVING COUNT(*) > 5
```

---

## Trap 3

Using:

```sql
LIMIT 1
```

for second-highest salary.

You need to handle duplicates.

---

## Trap 4

Confusing:

```text
RANK
DENSE_RANK
ROW_NUMBER
```

Remember:

```text
Values:
100
100
90

ROW_NUMBER:
1
2
3

RANK:
1
1
3

DENSE_RANK:
1
1
2
```

---

## Trap 5

Thinking:

> LEFT JOIN always returns more rows than INNER JOIN.

Not necessarily. The number of rows depends on the relationship/cardinality and duplicate matches.

---

## Trap 6

Thinking:

> Index always makes queries faster.

No.

Indexes can improve reads but add storage and write/maintenance overhead.

---

# PART 35 — WHAT YOU SHOULD BE ABLE TO EXPLAIN VERBALLY

In an interview, don't just write:

```sql
SELECT ...
```

Explain your thought process.

For example:

> "First I group the employees by department because I need department-level aggregation. Then I calculate the average salary. Finally, I use HAVING because I'm filtering the aggregated groups rather than individual employee rows."

That explanation demonstrates much more SQL knowledge than simply producing the query.

---

# PART 36 — INTERVIEW PREPARATION ORDER

I recommend this sequence:

### DAY 1

```text
SELECT
WHERE
AND
OR
NOT
IN
BETWEEN
LIKE
DISTINCT
ORDER BY
```

### DAY 2

```text
COUNT
SUM
AVG
MIN
MAX
GROUP BY
HAVING
```

### DAY 3

```text
INNER JOIN
LEFT JOIN
RIGHT JOIN
FULL JOIN
SELF JOIN
```

### DAY 4

```text
Subqueries
Correlated subqueries
EXISTS
IN
CASE
NULL
COALESCE
```

### DAY 5

```text
Keys
Constraints
Normalization
Indexes
Views
```

### DAY 6

```text
Transactions
ACID
Isolation
CTEs
```

### DAY 7

```text
Window functions
ROW_NUMBER
RANK
DENSE_RANK
LAG
LEAD
Running totals
Top-N problems
```

---

# PART 37 — FINAL INTERVIEW CHECKLIST

Before your interview, you should be able to solve **without notes**:

- [ ] Basic `SELECT`
- [ ] Filtering with `WHERE`
- [ ] `LIKE`
- [ ] `IN`
- [ ] `BETWEEN`
- [ ] `DISTINCT`
- [ ] `ORDER BY`
- [ ] `COUNT`
- [ ] `SUM`
- [ ] `AVG`
- [ ] `MIN`
- [ ] `MAX`
- [ ] `GROUP BY`
- [ ] `HAVING`
- [ ] INNER JOIN
- [ ] LEFT JOIN
- [ ] RIGHT JOIN
- [ ] SELF JOIN
- [ ] UNION
- [ ] UNION ALL
- [ ] Subqueries
- [ ] Correlated subqueries
- [ ] EXISTS
- [ ] CASE
- [ ] NULL
- [ ] COALESCE
- [ ] Primary key
- [ ] Foreign key
- [ ] Unique key
- [ ] Constraints
- [ ] Index
- [ ] View
- [ ] Normalization
- [ ] ACID
- [ ] Transactions
- [ ] CTE
- [ ] Window functions
- [ ] `ROW_NUMBER`
- [ ] `RANK`
- [ ] `DENSE_RANK`
- [ ] `LAG`
- [ ] `LEAD`
- [ ] Second-highest salary
- [ ] Nth-highest salary
- [ ] Duplicate records
- [ ] Top-N per group
- [ ] Above-average salary
- [ ] Department-wise average
- [ ] Customers with no orders
- [ ] Running total
- [ ] Date-based analysis

W3Schools also provides interactive SQL exercises covering syntax, `SELECT`, filtering, aggregation, joins, `GROUP BY`, `HAVING`, `EXISTS`, `CASE`, constraints, indexes, views and other topics, so it is useful to practice each section after learning the concept. ([w3schools.com](https://www.w3schools.com/Sql/sql_exercises.asp?utm_source=chatgpt.com))

[W3Schools SQL Exercises](https://www.w3schools.com/sql/sql_exercises.asp?utm_source=chatgpt.com)

## The most important takeaway

For a **data engineering/data analytics/software engineering interview**, don't stop at basic W3Schools syntax. Your real target should be:

```text
SQL Basics
    ↓
Filtering
    ↓
Aggregation
    ↓
GROUP BY + HAVING
    ↓
JOINS
    ↓
SUBQUERIES
    ↓
CASE + NULL
    ↓
CTEs
    ↓
WINDOW FUNCTIONS
    ↓
Complex analytical queries
    ↓
Optimization + Indexes
    ↓
Transactions + ACID
    ↓
Database Design
```

If you master that progression, you move from **"I know SQL syntax"** to **"I can solve SQL interview problems."**
