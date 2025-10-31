# RDBMS_interview


# 🧠 RDBMS Rapid Fire Flash Cards

## 1. SQL Clauses & Execution Order

Common clauses: `SELECT`, `FROM`, `WHERE`, `GROUP BY`, `HAVING`,
`ORDER BY`.\
Execution order: **FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER
BY.**

------------------------------------------------------------------------

## 2. Date & Time Functions

Functions: `NOW()`, `CURDATE()`, `DATEDIFF()`, `YEAR()`, `MONTH()`.\
Query:

``` sql
SELECT name FROM employee WHERE DATEDIFF(CURDATE(), dob)/365 > 25;
```

If today = 12 Sept 2023 → only those born before 12 Sept 1998 qualify.

------------------------------------------------------------------------

## 3. Join Record Count

  Join Type    Records Returned
  ------------ --------------------------
  Left Join    3 (all from table1)
  Inner Join   1 (only matching)
  Full Outer   5 (all unique from both)

------------------------------------------------------------------------

## 4. Stored Procedure Optimization

A stored procedure is a precompiled SQL block.\
To optimize: add indexes, avoid loops, use set-based operations, and
minimize I/O.

------------------------------------------------------------------------

## 5. Cardinality & Relationships

Cardinality = number of unique values in a column.\
Relations: One-to-One, One-to-Many, Many-to-Many.\
Example: `dept_id` in employee table links to department table.

------------------------------------------------------------------------

## 6. Database Design Levels

Levels: **Conceptual → Logical → Physical.**\
Normalization occurs in **logical design** to remove redundancy.

------------------------------------------------------------------------

## 7. Second Highest Salary

Options:

``` sql
SELECT MAX(salary) FROM emp WHERE salary < (SELECT MAX(salary) FROM emp);
```

or use `RANK()` / `LIMIT 1 OFFSET 1`.\
Using window functions is most optimized.

------------------------------------------------------------------------

## 8. Temporary Tables

Temporary tables exist per session; global ones (`##temp`) are shared.\
Yes, they can have the same name as normal tables in different scopes.

------------------------------------------------------------------------

## 9. ROW_NUMBER vs RANK vs DENSE_RANK

`ROW_NUMBER` → sequential numbers.\
`RANK` → skips ranks for ties.\
`DENSE_RANK` → no gaps.\
Use `RANK` when you need skipped ranking (e.g., competitions).

------------------------------------------------------------------------

## 10. Finding Duplicates

``` sql
SELECT col, COUNT(*) FROM table GROUP BY col HAVING COUNT(*) > 1;
```

Delete:

``` sql
DELETE FROM table WHERE id NOT IN (SELECT MIN(id) FROM table GROUP BY col);
```

------------------------------------------------------------------------

## 11. Primary & Foreign Key

Primary = unique identifier; Foreign = references another table's
primary key.\
Yes, a column from a composite PK can be FK in another table.

------------------------------------------------------------------------

## 12. Data Modelling & Normalization

Normalization reduces redundancy.\
Forms:\
- **UNF:** Unorganized data\
- **1NF:** Atomic values\
- **2NF:** Remove partial dependency\
- **3NF:** Remove transitive dependency\
- **BCNF:** Stronger 3NF\
- **Denormalization:** Improves query speed by combining tables.

------------------------------------------------------------------------

## 13. Star vs Snowflake Schema

**Star:** Fact table + denormalized dimension tables.\
**Snowflake:** Normalized dimensions.\
Star = more denormalized → faster queries.

------------------------------------------------------------------------

## 14. Fact Table Keys

Yes, a single primary key from a fact table can be linked to multiple
dimension tables.

------------------------------------------------------------------------

## 15. INTERSECT vs INNER JOIN

`INTERSECT` → returns common rows from two queries.\
`INNER JOIN` → matches rows based on related columns.

------------------------------------------------------------------------

## 16. Department-wise Query

``` sql
SELECT e.name, e.salary, d.name 
FROM employee e 
JOIN department d ON e.deptid = d.id 
WHERE e.salary > 5000;
```

------------------------------------------------------------------------

## 17. Correlated Subquery

A subquery that depends on outer query values.\
Executes once per outer row.

------------------------------------------------------------------------

## 18. Trigger

Trigger = event-based SQL procedure.\
Example:

``` sql
CREATE TRIGGER salary_check 
BEFORE INSERT ON emp 
FOR EACH ROW IF NEW.salary < 0 THEN SIGNAL SQLSTATE '45000';
```

------------------------------------------------------------------------

## 19. Views & DML Limitation

Views = virtual tables for restricted access.\
Use `WITH CHECK OPTION` to limit DML.\
A table and view can't share the same name.

------------------------------------------------------------------------

## 20. Types of Views

1.  **Simple View** -- from single table\
2.  **Complex View** -- from multiple tables\
    Used for security, abstraction, and simplification.

------------------------------------------------------------------------

## 21. Transactions & ACID

Transaction = logical unit of work.\
**ACID:** Atomicity, Consistency, Isolation, Durability.

------------------------------------------------------------------------

## 22. Change Gender Values

``` sql
UPDATE emp SET gender = 
CASE WHEN gender='M' THEN 'F' ELSE 'M' END;
```

------------------------------------------------------------------------

## 23. Join Outputs (Example)

  Join Type   Result Description
  ----------- ---------------------------
  Inner       Common values
  Left        All left + matching right
  Right       All right + matching left
  Full        All records both sides
  Cross       Cartesian product
  Self        Join table with itself

------------------------------------------------------------------------

## 24. Dept Avg Salary

``` sql
SELECT d.name, AVG(e.salary) AS avg_sal
FROM employee e JOIN department d ON e.deptid=d.id
GROUP BY d.name ORDER BY avg_sal DESC;
```

------------------------------------------------------------------------

## 25. Records Missing in Another Table

``` sql
SELECT * FROM A WHERE id NOT IN (SELECT id FROM B);
```

------------------------------------------------------------------------

## 26. Second Highest per Dept

``` sql
SELECT * FROM (
  SELECT name, deptid, salary,
  DENSE_RANK() OVER(PARTITION BY deptid ORDER BY salary DESC) AS rnk
  FROM emp
) t WHERE rnk = 2;
```

------------------------------------------------------------------------

## 27. Years with No Hiring

``` sql
SELECT year FROM generate_series(1970,1990)
WHERE year NOT IN (SELECT YEAR(hiredate) FROM emp);
```

------------------------------------------------------------------------

## 28. Employees Not Managers

``` sql
SELECT * FROM emp WHERE id NOT IN (SELECT mgrid FROM emp);
```

------------------------------------------------------------------------

## 29. Mini Statement

Fetch last 10 transactions ordered by date, compute running balance
using window:

``` sql
SELECT *, SUM(amount) OVER(ORDER BY date_time) AS balance
FROM txn WHERE acc_id = ? ORDER BY date_time DESC LIMIT 10;
```

------------------------------------------------------------------------

## 30. Window Functions

Used for analytics without collapsing rows.\
`PARTITION BY` → divides data groups, `ORDER BY` → defines sequence.

------------------------------------------------------------------------

## 31. Order/Product Queries

-   Unique customers on 5 Jan 2021:

    ``` sql
    SELECT COUNT(DISTINCT customer_id) FROM orders WHERE DATE(order_timestamp)='2021-01-05';
    ```

-   Category summary:

    ``` sql
    SELECT p.product_category, COUNT(*), SUM(order_amount)
    FROM orders o JOIN product_master p ON o.product_id=p.product_id
    GROUP BY p.product_category;
    ```

-   Top 5 products:

    ``` sql
    SELECT p.product_name, SUM(order_amount)
    FROM orders o JOIN product_master p ON o.product_id=p.product_id
    WHERE QUARTER(order_timestamp)=4
    GROUP BY p.product_name ORDER BY SUM(order_amount) DESC LIMIT 5;
    ```

-   No orders:

    ``` sql
    SELECT * FROM product_master WHERE product_id NOT IN (SELECT product_id FROM orders);
    ```

------------------------------------------------------------------------

## 32. NoSQL vs RDBMS

RDBMS → structured, schema-based, uses SQL.\
NoSQL → schema-less, stores JSON-like docs, scales horizontally.\
Preferred for high scalability and unstructured data.

------------------------------------------------------------------------

## 33. MongoDB Salary Query

``` js
db.employees.find({salary:{$gt:1000}}, {name:1, salary:1}).sort({salary:-1});
```

------------------------------------------------------------------------

## 34. Sharding in NoSQL

Sharding = horizontal partitioning of data across servers.\
Shard key should have **high cardinality** and **uniform distribution**
to balance load.
