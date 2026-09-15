# DBMS + SQL --- Interview Notes

## 1. DBMS

A DBMS manages persistent data and provides:

-   storage
-   retrieval
-   update
-   concurrency control
-   transactions
-   security
-   recovery
-   integrity

``` mermaid
flowchart LR
    A[Application] --> B[DBMS]
    B --> C[Query Processor]
    B --> D[Transaction Manager]
    B --> E[Storage Manager]
    C --> F[Database]
    D --> F
    E --> F
```

## 2. DBMS vs File System

DBMS adds:

-   structured querying
-   concurrency control
-   transactions
-   constraints
-   indexing
-   recovery
-   access control

------------------------------------------------------------------------

# 3. Database models

### Relational

Data in tables.

Examples: PostgreSQL, MySQL, Oracle.

### NoSQL

Common categories:

-   key-value
-   document
-   column-family
-   graph

Examples include Redis, MongoDB, Cassandra and graph databases.

------------------------------------------------------------------------

# 4. Keys

### Super key

Any attribute set uniquely identifying a row.

### Candidate key

Minimal super key.

### Primary key

Chosen candidate key.

### Alternate key

Candidate key not selected as primary.

### Foreign key

References a key in another table.

------------------------------------------------------------------------

# 5. ER Model

Entities represent real-world objects.

Attributes describe them.

Relationships connect entities.

Common relationship cardinalities:

-   1:1
-   1:N
-   M:N

M:N is usually represented using an associative/junction table.

------------------------------------------------------------------------

# 6. Functional dependency

`X → Y` means values of X determine values of Y.

Example:

``` text
student_id → student_name
```

This is central to normalization.

------------------------------------------------------------------------

# 7. Normalization

Goal: reduce redundancy and modification anomalies.

### 1NF

Atomic values; no repeating groups.

### 2NF

1NF + no partial dependency on part of a composite candidate key.

### 3NF

2NF + no problematic transitive dependency of non-key attributes on a
key.

### BCNF

Every determinant is a candidate key.

### Anomalies

-   insertion anomaly
-   update anomaly
-   deletion anomaly

### Denormalization

Intentionally adding redundancy to improve read performance or simplify
queries.

Trade-off:

-   faster reads
-   more storage/redundancy
-   harder writes/consistency

------------------------------------------------------------------------

# 8. SQL command categories

### DDL

`CREATE`, `ALTER`, `DROP`, `TRUNCATE`

### DML

`INSERT`, `UPDATE`, `DELETE`

### DQL

`SELECT`

### DCL

`GRANT`, `REVOKE`

### TCL

`COMMIT`, `ROLLBACK`, `SAVEPOINT`

------------------------------------------------------------------------

# 9. Joins

### INNER JOIN

Only matching rows.

### LEFT JOIN

All left rows + matching right rows.

### RIGHT JOIN

All right rows + matching left rows.

### FULL OUTER JOIN

All rows from both sides.

### SELF JOIN

Table joined with itself.

------------------------------------------------------------------------

# 10. WHERE vs HAVING

`WHERE` filters rows before aggregation.

`HAVING` filters groups after aggregation.

``` sql
SELECT department, COUNT(*) AS cnt
FROM employees
WHERE salary > 50000
GROUP BY department
HAVING COUNT(*) >= 5;
```

Logical processing is conceptually:

``` text
FROM/JOIN
→ WHERE
→ GROUP BY
→ HAVING
→ SELECT
→ ORDER BY
→ LIMIT
```

------------------------------------------------------------------------

# 11. Subquery vs CTE

### Subquery

Query nested inside another query.

### CTE

``` sql
WITH department_totals AS (
    SELECT department, COUNT(*) cnt
    FROM employees
    GROUP BY department
)
SELECT *
FROM department_totals
WHERE cnt > 5;
```

CTEs improve readability and can support recursive queries.

------------------------------------------------------------------------

# 12. Window functions

Window functions calculate across related rows without collapsing them.

``` sql
SELECT
    employee,
    department,
    salary,
    RANK() OVER (
        PARTITION BY department
        ORDER BY salary DESC
    ) AS rnk
FROM employees;
```

### ROW_NUMBER vs RANK vs DENSE_RANK

``` text
Values: 100 100 90

ROW_NUMBER: 1 2 3
RANK:       1 1 3
DENSE_RANK: 1 1 2
```

High-value interview topic.

------------------------------------------------------------------------

# 13. Indexing

An index is an auxiliary structure that accelerates lookups.

Benefits:

-   faster reads
-   efficient filtering
-   faster joins/orderings in suitable cases

Costs:

-   extra storage
-   slower writes
-   index maintenance

Do not index every column.

## B-tree intuition

Balanced tree structure allowing logarithmic navigation and ordered
access.

B+ trees store actual searchable entries in leaves and are widely used
in database indexing.

------------------------------------------------------------------------

# 14. Transactions

A transaction is a logical unit of work.

Example:

``` text
Debit A
Credit B
```

Both must succeed as one logical operation.

------------------------------------------------------------------------

# 15. ACID

### Atomicity

All or nothing.

### Consistency

Valid state before and after transaction.

### Isolation

Concurrent transactions behave according to the chosen isolation
guarantees.

### Durability

Committed data survives failures.

------------------------------------------------------------------------

# 16. Concurrency anomalies

### Dirty read

Transaction reads uncommitted data.

### Non-repeatable read

Same row produces different committed value within a transaction.

### Phantom read

Repeated range query sees newly inserted/deleted matching rows.

Isolation levels trade concurrency against stronger guarantees.

------------------------------------------------------------------------

# 17. Locks and deadlocks

Two transactions can deadlock:

``` text
T1 holds A → waits for B
T2 holds B → waits for A
```

Avoidance strategies include:

-   consistent lock ordering
-   short transactions
-   timeouts
-   deadlock detection/recovery

------------------------------------------------------------------------

# 18. SQL interview problems

## Second highest salary

``` sql
SELECT MAX(salary)
FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);
```

Be careful about duplicates and "second distinct" semantics.

## Nth highest

Use `DENSE_RANK()` when distinct salary rank is intended.

## Duplicate rows

``` sql
SELECT email, COUNT(*)
FROM users
GROUP BY email
HAVING COUNT(*) > 1;
```

## Employees earning more than department average

``` sql
SELECT e.*
FROM employees e
JOIN (
    SELECT department_id, AVG(salary) avg_salary
    FROM employees
    GROUP BY department_id
) d
ON e.department_id = d.department_id
WHERE e.salary > d.avg_salary;
```

------------------------------------------------------------------------

# 19. Query optimization

Think about:

-   indexes
-   filtering early
-   avoiding unnecessary columns
-   join cardinality
-   query plans
-   avoiding repeated correlated work
-   pagination strategy

Use `EXPLAIN`/`EXPLAIN ANALYZE` in PostgreSQL to understand query plans.

------------------------------------------------------------------------

# 20. PostgreSQL interview topics

Know:

-   primary/foreign keys
-   constraints
-   indexes
-   transactions
-   isolation
-   `EXPLAIN ANALYZE`
-   JSON/JSONB basics
-   sequences/identity
-   connection pooling
-   `VACUUM`/autovacuum at a high level

------------------------------------------------------------------------

# 21. Important Q&A

### Q: Primary key vs unique key?

A primary key identifies the row and is the table's primary identity
constraint. A unique constraint also enforces uniqueness but is not
necessarily the primary identifier; exact NULL behavior depends on the
DBMS.

### Q: Why indexes slow writes?

Because every insert/update/delete may need index structures updated.

### Q: DELETE vs TRUNCATE?

`DELETE` removes rows and can use a WHERE clause. `TRUNCATE` removes all
rows more directly and is generally faster for clearing a table; exact
transactional/identity behavior depends on DBMS.

### Q: WHERE vs HAVING?

WHERE filters rows before grouping; HAVING filters aggregated groups.

### Q: Normalization vs denormalization?

Normalization reduces redundancy and anomalies. Denormalization
intentionally introduces redundancy for performance or simpler reads.

### Q: What is a deadlock?

A circular wait where transactions/resources block each other
indefinitely unless the DBMS detects/recover or prevention rules break
the cycle.


# 22. Additional DBMS topics

## B-tree vs B+ tree

Both are balanced tree structures used for efficient indexing.

B+ trees commonly keep searchable records/entries in leaf nodes and connect leaves for efficient ordered/range scans.

## Clustered vs non-clustered index

Terminology differs across DBMSs, so explain the concept rather than assuming identical implementation.

A clustered organization aligns table storage/order with an index key; a secondary/non-clustered index separately points toward rows.

## View

A view is a stored query definition that behaves like a virtual table.

Uses:

- abstraction
- security
- reusable query logic

## Materialized view

Stores the query result physically and must be refreshed.

Useful for expensive analytical queries.

## Stored procedure vs function

DBMS-specific, but generally procedures represent executable database routines, while functions return a value and may have additional usage restrictions depending on DBMS.

## OLTP vs OLAP

**OLTP**

- many small transactions
- current operational data
- strong consistency/transaction requirements

**OLAP**

- analytical queries
- aggregations
- historical data
- read-heavy workloads

## Replication

Copies data across database nodes.

Benefits:

- availability
- read scaling
- disaster recovery

Challenges:

- replication lag
- consistency
- failover

## Sharding

Horizontally partitions data across multiple nodes.

Challenges:

- shard key selection
- cross-shard queries
- rebalancing
- transactions across shards

## CAP theorem

For a distributed system, under a network partition, a system must trade between strong consistency and availability.

Do not oversimplify CAP as "you can only choose two at all times"; the theorem specifically concerns behavior during partitions.

## SQL NULL

`NULL` means unknown/missing, not zero or empty string.

Use:

```sql
IS NULL
IS NOT NULL
```

not:

```sql
= NULL
```

## SQL injection

Never build SQL with raw user input.

Use parameterized queries/prepared statements.

## Connection pooling

Creating DB connections repeatedly is expensive. A pool reuses connections and controls concurrency.

