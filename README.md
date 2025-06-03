<h1 style="text-align: center; padding: 40px 2px">Querying Data in PostgreSQL</h1>
*Learning SELECT, Aliases, ORDER BY, and DISTINCT with Lucy, Nathanael and Stella*

<h2 style="padding-top: 60px">Introduction</h2>
The SELECT statement is the foundation of data retrieval in PostgreSQL. This tutorial covers essential querying techniques using the `employees` table:

```sql
CREATE TABLE employees (
  id SERIAL PRIMARY KEY,
  first_name VARCHAR(50),
  last_name VARCHAR(50),
  department VARCHAR(50),
  hire_date DATE,
  salary NUMERIC(10,2),
  email VARCHAR(100)
);

INSERT INTO employees VALUES
(1, 'Stella', 'Stephanie', 'Engineering', '2023-01-15', 89000, 's.stephanie@company.com'),
(2, 'Nathanael', 'Mutua', 'Marketing', '2022-03-22', 90000, 'nathanael.m@company.com'),
(3, 'Lucy', 'Wanjiru', 'Director', '2023-11-05', 92000, 'l.wanjiru@company.com'),
(4, 'Mwangi', 'Johnson', 'HR', '2021-08-30', 68000, 'mwangij@company.com'),
(5, 'Daniel', 'Njoroge', 'Engineering', '2023-01-15', 85000, 'dan.njoro@company.com'),
(6, 'Anna', 'Kiptoo', NULL, '2023-05-01', 75000, 'anna.kip@company.com'),
(7, 'Wilson', 'Kiptoo', 'Engineering', '2024-02-12', 60000, 'wilson.kip@company.com'),
(8, 'Mercy', 'Mumo', 'Marketing', '2024-02-12', 42000, 'mercy.mumo@company.com');
```


<h2 style="padding-top: 60px">Setup</h2>

### Requirements
- Have PostgreSQL installed..[click here for a tutorial](https://www.youtube.com/watch?v=GpqJzWCcQXY)
- Have PGADmin 4 installed...[click here for a tutorial](https://www.youtube.com/watch?v=4qH-7w5LZsA)
- Create a database (you can name it as you wish)

<img src="./images/create-database.png" alt="Creating Database" width="350" >

- Now we can start, just open the query tool:

<img src="./images/access-query.png" alt="Accessing Query Tool" width="350" >

<h2 style="padding-top: 60px">1. The SELECT Statement</h2>
It is kinda like the foundation of data retrieval in PostgreSQL. It is a command for retrieving data from tables within a PostgreSQL database. It enables users to specify which columns to fetch and apply filters for targeted results.

### Basic Syntax
```sql
SELECT column1, column2, ...
FROM table_name;
```

### Key Examples
### Fetch specific columns
We specify the columns to be selected after the 'SELECT' keyword.

#### Selecting a single Column

```sql
SELECT first_name
FROM employees;
```
```
 first_name 
------------
 Stella    
 Nathanael
 Lucy
 Mwangi
 Daniel    
 ... --Means there's more in the Data Output
```

#### Selecting Multiple Columns
We separate each column with a comma (,)
```sql
SELECT first_name, last_name, department 
FROM employees;
```
```
 first_name |  last_name  |   department  
------------+-------------+---------------
 Stella     | Stephanie  |  Engineering
 Nathanael  | Mutua      |  Marketing
 Lucy       | Wanjiru    |  Director
 ...
```

### Fetch all columns
We use the (*) asterisk symbol.
```sql
SELECT * FROM employees;
```
> **Avoid SELECT * in production:** Retrieves all columns which can:
> - Reduce database performance
> - Increase network traffic
> - Cause application slowdowns

### Using expressions
```sql
SELECT 
  first_name || ' ' || last_name,
  salary
FROM employees;
```
```
     ?column?     | salary 
------------------+----------
 Sarah Stephanie  |  89000.00
 Lucy Wanjiru     |  92000.00
 Nathanael Mutua  |  90000.00
 ...
```
> Notice the default column name. We'll learn to customize these with aliases in the next section.

### Using WHERE to target and filter

To filter results based on specific conditions, we can use the WHERE clause. For example, to retrieve employees in the Engineering department:

```sql
SELECT 
  first_name , last_name, salary
FROM employees
WHERE department = 'Engineering';
```
```
     first_name   |      last_name   | salary     |
------------------+------------------+------------+
 Stella           |  Stephanie       |  85000.00  |
 Daniel           |  Njoroge         |  85000.00  |
 ...
```

<h2 style="padding-top: 60px">2. Column Aliases</h2>
Temporary names assigned to columns or expressions for readability.

### Syntax Options
```sql
SELECT column_name AS alias_name
SELECT column_name alias_name  -- AS is optional
SELECT expression AS "Alias With Space"
```

### Examples of Aliasing
#### Basic aliasing
We will replace the column heads for two columns in our employees table.

```sql
SELECT 
  first_name AS "First Name", 
  last_name AS surname
FROM employees;
```
```
  First Name  |   surname   
--------------+--------------
 Stella       |   Stephanie
 Nathanael    |   Mutua
 Lucy         |   Wanjiru
 Mwangi       |   Johnson
 ...   
```
> [!NOTE]
> To alias names with spaces we enclose them in double quotes

#### Expression aliasing
```sql
SELECT 
  first_name || ' ' || last_name AS full_name,
  salary * 1.1 AS proposed_salary
FROM employees;
```
```
   full_name       | proposed_salary 
-------------------+-----------------
 Stella Stephanie  |        97900.00
 Lucy Wanjiru      |        101200.00
 ...
```

#### Aliases with special characters
```sql
SELECT 
  email AS "Work Email",
  salary AS "Monthly Salary ($)"
FROM employees;
```

```
     Work Email            |   Monthly Salary
---------------------------+------------------- 
  s.stephanie@company.com	 |    89000.00
  nathanael.m@company.com	 |    90000.00
  l.wanjiru@company.com	   |    92000.00
  ...
```

<h2 style="padding-top: 60px">3. ORDED BY Clause</h2>
Sorts query results based on specified columns or expressions.
Usually basically ascending and descending, but we can do more.

### Basic Syntax
```sql
SELECT columns
FROM table
ORDER BY column1 [ASC|DESC], 
         column2 [ASC|DESC];
```

### Sorting Techniques
#### Single column sorting
```sql
SELECT first_name, hire_date
FROM employees
ORDER BY hire_date DESC;  -- Newest first
```
```
 first_name  |  hire_date
-------------+-------------
  Wilson     |  2024-02-12
  Mercy      |  2024-02-12
  Lucy       |  2023-11-05
  ...
```

#### Multi-column sorting
```sql
SELECT department, first_name, salary
FROM employees
ORDER BY department ASC, salary DESC;
```
```
   department  |  first_name  |  salary
---------------+--------------+-----------   
 Director	     |   Lucy	      |  92000.00
 Engineering	 |   Stella  	  |  89000.00
 ...
```
#### Sorting by expression
```sql
SELECT first_name, last_name
FROM employees
ORDER BY LENGTH(first_name), last_name;
```
```
 department    | 	first_name	|  salary
---------------+--------------+-----------
 "Director"	   |  "Lucy"	    |  92000.00
 "Engineering" |	"Daniel"	  |  85000.00
 ...
```

#### NULL handling
```sql
SELECT first_name, department
FROM employees
ORDER BY department NULLS LAST;
```

```sql
SELECT first_name, department
FROM employees
ORDER BY department NULLS FIRST;
```

<h2 style="padding-top: 60px">4. SELECT DISTINCT Clause</h2>
Removes duplicate rows from query results.

### Basic Syntax
```sql
SELECT DISTINCT column1
FROM table_name;

SELECT DISTINCT column1, column2
FROM table_name;
```

### Duplicate Elimination
#### Single column distinct
```sql
SELECT DISTINCT department 
FROM employees;
```
```
 department  
--------------
 Engineering
 Marketing
 Director
 HR
 null
```

#### Multi-column distinct
```sql
SELECT DISTINCT department, salary
FROM employees;
```
```
 first_name  |  department  
-------------+--------------
 Marketing   |  42000.00
 [null]      |  75000.00
 HR          |  68000.00
 Marketing   |  72000.00
 Engineering |  85000.00
 Director    |  92000.00
 Engineering |  60000.00
```

#### DISTINCT with expressions
```sql
SELECT DISTINCT 
  salary / 1000 AS salary_band
FROM employees;
```
```
 salary_band 
-------------
    92.00
    60.00
    68.00
    85.00
    75.00
    89.00
```
> **Performance thought:** DISTINCT sorts results internally which can be costly on large datasets.

<h2 style="padding-top: 60px">Querying Tips</h2>
> - **SELECT Specific Columns**: Avoid SELECT * in production, it's kinda risky.
> - **Use Aliases Wisely**: Improve readability but avoid names that are too long.
> - **DISTINCT Alternatives**: Consider GROUP BY for complex deduplication.

<h2 style="padding-top: 60px">PostgreSQL Querying Summary</h2>

| Clause             | Purpose                          | Example                          |
|--------------------|----------------------------------|----------------------------------|
| SELECT             | Specify columns to retrieve      | SELECT id, name FROM users       |
| AS                 | Assign column alias              | SELECT salary * 12 AS annual     |
| ORDER BY           | Sort results                     | ORDER BY hire_date DESC          |
| DISTINCT           | Remove duplicates                | SELECT DISTINCT department       |
| NULLS FIRST/LAST   | Control NULL position in sorts   | ORDER BY score NULLS LAST        |

<p style="padding-top: 40px; font-weight: 800;">PostgreSQL Querying Tutorial - <i>Nash, Stella & Lucy</i></p>