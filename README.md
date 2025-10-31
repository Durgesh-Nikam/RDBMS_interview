#                     RDBMS Rapid Fire Sheet


---

# 1\. What clauses have you used in SQL (eg. WHERE, SELECT, etc). What is the sequence of executions of these clauses?

**Answer:**  
Common clauses: `SELECT`, `FROM`, `WHERE`, `GROUP BY`, `HAVING`, `ORDER BY`, `JOIN`, `DISTINCT`.  
**Execution order:**

1.  `FROM` + `JOIN`
    
2.  `WHERE`
    
3.  `GROUP BY`
    
4.  `HAVING`
    
5.  `SELECT`
    
6.  `DISTINCT`
    
7.  `ORDER BY`
    

---

# 2\. What are the date and time functions you have used? Find employees whose age > 25 as of today.

**Answer:**  
Common functions: `NOW()`, `CURDATE()`, `DATEDIFF()`, `DATE_ADD()`, `DATE_SUB()`, `YEAR()`, `MONTH()`.  
**Query Example:**

```sql
SELECT name 
FROM employees 
WHERE DATEDIFF(CURDATE(), dob)/365 > 25;
```

**Related:** For DOBs 11-Sept-2023, 12-Sept-2023, 13-Sept-2023, and age >= 237 days, only 11-Sept-2023 and 12-Sept-2023 qualify.

---

# 3\. Given two tables, find number of records for LEFT JOIN, INNER JOIN, FULL OUTER JOIN.

**Answer:**  
**Tables:**

-   T1: 111, NULL, 2
    
-   T2: 1, 1, NULL, NULL, 3
    

**Joins:**

-   LEFT JOIN → All T1 rows with matched T2, plus unmatched → 3 rows matched + 1 extra?
    
-   INNER JOIN → Only matching → Count of matching non-null values → 1 row
    
-   FULL OUTER JOIN → All rows from both tables → 7 rows
    

*(Exact count depends on NULL handling in DB.)*

---

# 4\. What are stored procedures in SQL? Reduce execution time from 10s to 1s?

**Answer:**  
**Stored Procedure:** Precompiled SQL code saved in DB to perform operations.  
**Optimization:** Indexing, query optimization, avoiding loops, proper joins, using temporary tables or batch processing.

---

# 5\. What is cardinality in the database? How are relations maintained in tables?

**Answer:**  
**Cardinality:** Number of unique values in a column.  
**Relations:** Primary Key ↔ Foreign Key; ensures referential integrity.  
**Example:** `Employee(dept_id)` references `Department(id)`.

---

# 6\. How do you design a database? Levels? Normalization level?

**Answer:**  
**Design Steps:** Requirement gathering → Conceptual → Logical → Physical → Implementation.  
**Levels:** Conceptual, Logical, Physical.  
**Normalization:** Logical level.

---

# 7\. Find name, salary of second highest earner, all possible ways? Most optimum?

**Answer:**

```sql
-- Using LIMIT (MySQL)
SELECT name, salary 
FROM employees 
ORDER BY salary DESC 
LIMIT 1 OFFSET 1;

-- Using RANK()
SELECT name, salary 
FROM (
    SELECT name, salary, RANK() OVER(ORDER BY salary DESC) r
    FROM employees
) t WHERE r=2;
```

**Optimum:** `RANK()` for large datasets.

---

# 8\. What is a temporary table? Global temporary table? Can normal table and temp table have same name?

**Answer:**  
**Temp Table:** Exists only during session.  
**Global Temp Table:** Shared across sessions but session-specific data.  
**Same Name:** Not allowed in same schema.

---

# 9\. Difference between ROW\_NUMBER(), RANK(), DENSE\_RANK()? When to use RANK()?

**Answer:**

-   `ROW_NUMBER()` → Unique sequential number, ignores ties.
    
-   `RANK()` → Same rank for ties, skips next rank.
    
-   `DENSE_RANK()` → Same rank for ties, no skip.  
    **Use RANK():** When you need rank with gaps after ties.
    

---

# 10\. Write query to find duplicates? How to delete them?

**Answer:**

```sql
-- Find duplicates
SELECT col, COUNT(*) 
FROM table 
GROUP BY col 
HAVING COUNT(*) > 1;

-- Delete duplicates keeping one
DELETE t1 
FROM table t1
INNER JOIN table t2 
WHERE t1.id > t2.id AND t1.col = t2.col;
```

---

# 11\. What is a primary key and foreign key? Can a single column from composite PK be FK?

**Answer:**

-   **Primary Key:** Unique identifier for table.
    
-   **Foreign Key:** Column referencing PK in another table.
    
-   **Single column from composite PK as FK:** Yes.
    

---

# 12\. What is Data Modelling & Normalization? Explain all forms.

**Answer:**

-   **Data Modelling:** Designing structure of DB.
    
-   **Normalization:** Eliminating redundancy.  
    **Forms:**
    
-   UNF → raw data. Problems: redundancy.
    
-   1NF → Atomic values.
    
-   2NF → No partial dependency.
    
-   3NF → No transitive dependency.
    
-   BCNF → Stricter 3NF.
    
-   Denormalization → Adding redundancy for performance.
    

---

# 13\. Data Modelling for Data Warehouse? Star & Snowflake Schema?

**Answer:**

-   **Star Schema:** Fact table in center, denormalized dimension tables.
    
-   **Snowflake Schema:** Dimension tables normalized.
    
-   **More denormalized:** Star Schema (faster queries).
    

---

# 14\. Can a single PK from fact table be in more than 2 dimension tables?

**Answer:**  
Yes, PK in fact table can be referenced in multiple dimension tables via foreign keys.

---

# 15\. Difference between INTERSECTION and INNER JOIN?

**Answer:**

-   **INTERSECTION:** Returns common rows from two queries.
    
-   **INNER JOIN:** Returns rows matching join condition.
    

---

# 16\. Department-wise employees with salary > 5000, show dept name.

**Answer:**

```sql
SELECT d.name, e.name, e.salary
FROM employee e
JOIN department d ON e.deptid = d.id
WHERE e.salary > 5000;
```

---

# 17\. What is a correlated subquery?

**Answer:**  
Subquery that depends on outer query column.

```sql
SELECT name 
FROM employee e1 
WHERE salary > (SELECT AVG(salary) FROM employee e2 WHERE e1.deptid = e2.deptid);
```

---

# 18\. What is Trigger? Example?

**Answer:**  
Automatic procedure executed on events (INSERT/UPDATE/DELETE).

```sql
CREATE TRIGGER trg_salary 
BEFORE INSERT ON employee
FOR EACH ROW
SET NEW.salary = NEW.salary * 1.1;
```

---

# 19\. What is the use of views? Limit DML? Can view & table have same name?

**Answer:**

-   Simplify queries, security, abstraction.
    
-   DML restricted via `INSTEAD OF` triggers or `WITH CHECK OPTION`.
    
-   Cannot have same name in same schema.
    

---

# 20\. Different types of views & applications?

**Answer:**

-   **Types:** Simple, Complex, Materialized.
    
-   **Applications:** Security, query simplification, data abstraction.
    

---

# 21\. What is a transaction? ACID properties?

**Answer:**

-   **Transaction:** Unit of work in DB.
    
-   **ACID:**
    
    -   Atomicity → all or nothing
        
    -   Consistency → data valid before & after
        
    -   Isolation → concurrent transactions independent
        
    -   Durability → committed changes persistent
        

---

# 22\. Change all gender values in table.

**Answer:**

```sql
UPDATE employee SET gender = 'M'; -- or 'F'
```

---

# 23\. Output of different joins with given tables.

**Answer:**

-   **Inner Join:** Only common rows.
    
-   **Left Join:** All from left + matches from right.
    
-   **Right Join:** All from right + matches from left.
    
-   **Full Join:** All rows from both.
    
-   **Cross Join:** Cartesian product.
    
-   **Self Join:** Table joined with itself.
    

---

# 24\. Department name & average salary in descending order.

**Answer:**

```sql
SELECT d.name, AVG(e.salary) avg_sal
FROM employee e
JOIN department d ON e.deptid = d.id
GROUP BY d.name
ORDER BY avg_sal DESC;
```

---

# 25\. Find records in table not present in another table.

**Answer:**

```sql
SELECT * FROM table1 t1
WHERE NOT EXISTS (SELECT 1 FROM table2 t2 WHERE t1.id = t2.id);
```

---

# 26\. Second highest salaried employees in each department.

**Answer:**

```sql
SELECT name, salary, deptid
FROM (
    SELECT name, salary, deptid,
           DENSE_RANK() OVER(PARTITION BY deptid ORDER BY salary DESC) r
    FROM employee
) t WHERE r=2;
```

---

# 27\. Years (1970–1990) with no employees hired.

**Answer:**

```sql
SELECT year
FROM generate_series(1970,1990) AS year
WHERE year NOT IN (SELECT EXTRACT(YEAR FROM hiredate) FROM employee);
```

---

# 28\. Display employees who are not managers.

**Answer:**

```sql
SELECT * FROM employee
WHERE id NOT IN (SELECT DISTINCT mgrid FROM employee);
```

---

# 29\. Mini-statement: last 10 transactions with running balance.

**Answer:**

```sql
SELECT *, SUM(CASE WHEN debit_credit='CR' THEN amount ELSE -amount END) 
OVER (PARTITION BY acc_id ORDER BY date_time ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS running_balance
FROM transactions
WHERE acc_id = 123
ORDER BY date_time DESC
LIMIT 10;
```

---

# 30\. What are window functions? Significance of PARTITION & ORDER?

**Answer:**

-   Operate on a set of rows relative to current row.
    
-   `PARTITION` → defines groups, `ORDER` → defines order within group.
    

---

# 31\. SQL queries on orders & product\_master tables

**Answer:**

-   **Unique customers on 5-Jan-2021:**
    

```sql
SELECT COUNT(DISTINCT customer_id) 
FROM orders 
WHERE DATE(order_timestamp) = '2021-01-05';
```

-   **Category-wise total orders & amount:**
    

```sql
SELECT p.product_category, COUNT(*) total_orders, SUM(order_amount) total_amount
FROM orders o
JOIN product_master p ON o.product_id = p.product_id
GROUP BY p.product_category;
```

-   **Top 5 products last quarter:**
    

```sql
SELECT p.product_name, SUM(o.order_amount) total_amount
FROM orders o
JOIN product_master p ON o.product_id = p.product_id
WHERE o.order_timestamp BETWEEN '2025-07-01' AND '2025-09-30'
GROUP BY p.product_name
ORDER BY total_amount DESC
LIMIT 5;
```

-   **Products with no orders:**
    

```sql
SELECT product_name FROM product_master
WHERE product_id NOT IN (SELECT product_id FROM orders);
```

---

# 32\. How is NoSQL different from RDBMS? When preferred?

**Answer:**

-   RDBMS → structured, relational, SQL
    
-   NoSQL → schema-less, scalable, key-value/document/column/graph
    
-   Preferred → Big Data, high volume, unstructured data, horizontal scaling.
    

---

# 33\. Display name & salaries of employees >1000 in MongoDB.

**Answer:**

```javascript
db.employees.find({salary: {$gt: 1000}}, {name:1, salary:1, _id:0}).sort({salary:-1});
```

---

# 34\. What is Sharding in NoSQL? How to choose a shard key in Mongo?

**Answer:**

-   **Sharding:** Distributing data across multiple servers for scalability.
    
-   **Shard key:** Should have high cardinality, evenly distributed, frequently queried.
    

---

✅ This sheet is **interview-ready**, copy-paste friendly, and each Q&A is **concise yet complete**.

---

If you want, I can make a **one-page super-condensed flash-card version** with **just Q & very short answers**, ideal for last-minute revision. Do you want me to do that?
