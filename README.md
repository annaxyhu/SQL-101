# SQL Quick Reference Guide
Welcome to my SQL Study Notes repository! This collection is designed to serve as a quick reference guide for anyone interested in learning SQL, from beginners to those looking to refresh their knowledge. Note that the syntax in this guide is specific to MySQL. 

Feel free to explore, contribute, and use these notes as a handy reference for your SQL learning journey.

Handy tip: drop down the outline (on the top right) for easier navigation!

## Table of Contents
1. [Introduction](#introduction)
2. [Querying Data](#querying-data)
3. [Sorting Data](#sorting-data)
4. [Filtering Data](#filtering-data)
5. [Joining Tables](#joining-tables)
6. [Grouping Data](#grouping-data)
7. [Subqueries](#subqueries)
8. [Set Operators](#set-operators)
9. [Managing Databases](#managing-databases)
10. [Working with Tables](#working-with-tables)
11. [Constraints](#constraints)
12. [Data Types](#data-types)
13. [Modifying Data](#modifying-data)
14. [Common Table Expressions](#common-table-expressions)

   
## Introduction
### What is SQL? 

SQL (Structured Query Language) is a query language used for storing and processing information in a relational database. SQL commands can be used to store, update, remove, search and retrieve information from the database. 

SQL commands are broadly categorized into the following subsets: 
1. **Data Definition Language (DDL)**: commands used to create, alter, and drop database objects like tables, indexes, views, and schemas. Ex. CREATE, ALTER, DROP, TRUNCATE
2. **Data Query Language (DQL)**: commands that retrieve data from databases. Ex. SELECT FROM
3. **Data Manipulation Language (DML)**: commands used to modify or handle data within a database. Ex. INSERT, UPDATE, DELETE, MERGE
4. **Data Control Language (DCL)**: commands used to control and grant access to data in a database. Ex. GRANT, REVOKE
5. **Transaction Control Language (TCL)**: commands used to manage the changes made by DML statements (like INSERT, UPDATE, DELETE). Ex. COMMIT, ROLLBACK, SAVEPOINT

### Database Basics
A database is an organized collection of data stored in a structured format. It consists of tables, which hold the data, and relationships between the tables. Each table consists of rows (also known as records) and columns (also known as fields). Columns define the type of data that can be stored, such as text, numbers, or dates.

## Querying Data

### SELECT FROM 

The `SELECT` statement allows you to select data (one or more columns) from one or more tables.

Syntax:

```sql
SELECT
  select_list
FROM
  table_name;
```
- First, specify one or more columns from which you want to select data after the `SELECT` keyword. If the select_list has multiple columns, you need to separate them by a comma `,`
- Second, specify the name of the table from which you want to select data after the `FROM` keyword
- The result of a `SELECT` statement is called a result set
- SELECT * selects data from all columns of the table. In practice, you should use the SELECT * for the ad-hoc queries only

Evaluation Order: `FROM` -> `SELECT`
  
### SELECT DISTINCT

When querying data from a table, you may get duplicate rows. To remove these duplicate rows, a `DISTINCT` clause is used in the `SELECT` statement.

Syntax:

```sql
SELECT DISTINCT
  select_list
FROM
  table_name;
```
Evaluation order: `FROM` -> `SELECT` -> `DISTINCT`

### SELECT Without Referencing a Table

`SELECT` statements can also be used to perform simple calculations or to execute a function without a table reference. 

Example: 
```sql
SELECT
  CONCAT('John',' ','Doe');
```
Output:
```
+--------------------------+
| CONCAT('John',' ','Doe') |
+--------------------------+
| John Doe                 |
+--------------------------+
```

## Sorting Data

### ORDER BY

The `ORDER BY` clause sort the result set by one or more columns.

Syntax:

```sql
SELECT
  select_list
FROM
  table_name
ORDER BY
  column1 ASC,
  column1 DESC,
  ...;
```
- Specify the columns you want to sort after the `ORDER BY` clause, in either `ASC` or `DESC` order
- If no order is specified, `ORDER BY` sorts the result set in ascending (`ASC`) order
- In MySQL, `NULL` is alower than non-NULL values. When ordered by `ASC`, `NULL`s appear first in the result set 

Evaluation order: `FROM` -> `SELECT` -> `DISTINCT` -> `ORDER BY`

## Filtering Data

### WHERE
The WHERE clause allows you to specify a search condition for the rows returned by a query (filter rows by condition).

Syntax:

```sql
SELECT
  select_list
FROM
  table_name
WHERE
  search_condition;
```
- The `search_condition` is a combination of one or more expressions using logical operators
- The `SELECT` statement will include any row that satisfies the `search_condition` in the result set

The following comparison operators can be use to form the search_condition expression in the WHERE clause:
1. `=`: Equal to. Can be used with almost every data type.
2. `!=` or `<>`: Not equal to.
3. `<`: Less than
4. `>`: Greater than
5. `<=`: Less than or equal to
6. `>=`: Greater than or equal to
  
Evaluation Order: `FROM` -> `WHERE` -> `SELECT` -> `DISTINCT` -> `ORDER BY`

### AND
The `AND` operator can be used to combine multiple Boolean expressions to filter data. The AND operator returns true when both expressions are true; otherwise, it returns false.

Syntax:

```sql
SELECT
  select_list
FROM
  table_name
WHERE
  search_condition1 AND search_condition2;
```
### OR

The `OR` operator can be used to combine multiple Boolean expressions to filter data. The AND operator returns true when either expressions are true; otherwise, it returns false. SQL evaluates the OR operator after the AND operator if an expression contains both AND and OR operators.

Syntax:

```sql
SELECT
  select_list
FROM
  table_name
WHERE
  search_condition1 OR search_condition2;
```

### IN/NOT IN
Used in the WHERE clause, the IN operator allows you to determine if a value matches any value in a list of values.

Syntax:

```sql
value IN (value1, value2, value3,...)

value NOT IN (value1, value2, value3,...)
```
- Functionally, it is equivalent to a combination of `OR` statements
- Adding the NOT clause negates the expression and returns TRUE if the value does not match any values in the list

### BETWEEN/NOT BETWEEN
Used in the WHERE clause, the BETWEEN operator is a logical operator that tests whether a value is in a range or not. 

Syntax:
```sql
value BETWEEN val1 AND val2;

value NOT BETWEEN val1 AND val2;

```
- Adding the NOT clause negates the expression and returns TRUE if the value is outside of the specified range
  
### LIKE/NOT LIKE
Used in the WHERE clause, the LIKE operator is a logical operator that tests whether a string contains a specified pattern or not.

Syntax:

```sql
expression LIKE pattern ESCAPE escape_character

expression NOT LIKE pattern ESCAPE escape_character
```

- If the expression matches the pattern, the LIKE operator returns TRUE. Otherwise, it returns FALSE.
- Two wildcard characters can be used to construct the pattern
  - The percentage `%` wildcard matches any string of zero or more characters.
  - The underscore `_` wildcard matches any single character.
- When the pattern itself contains the wildcard character (ex. you want to find all entries that contain '_20') include the escape character `\` before the wildcard. ex. '%\_20'
  - You can also use the `ESCAPE` clause to declare a different escape character other than the default
- Adding the NOT clause negates the expression and returns TRUE if the string does not contain the specified pattern

### LIMIT

The LIMIT clause is used in the SELECT statement to constrain the number of rows to return. 

Syntax:
```sql
SELECT 
    select_list
FROM
    table_name
LIMIT [offset,] row_count;
```
- The offset specifies the offset of the first row to return. The offset of the first row is 0, not 1
- The row_count specifies the maximum number of rows to return

Evaluation Order: `FROM` -> `WHERE` -> `SELECT` -> `DISTINCT` -> `ORDER BY` ->`LIMIT`

### IS NULL/IS NOT NULL
IS NULL is a comparison operator used to test whether a value is NULL or not. IS NOT NULL checks whether a value is not NULL. This operator can be used in the SELECT or WHERE clauses.

**Syntax:**
```sql
value IS NULL

value IS NOT NULL
```

## Joining Tables

### Aliases
Sometimes column or table names get so technical or long that it makes a query's output difficult to understand. You can assign a temporary name to a column or a table using the `AS` keyword. 

Syntax:

```sql
/* To assign an alias to a column */
SELECT 
   [column_1 or expression] AS descriptive_name
FROM table_name;
```
OR

```sql
/* To assign an alias to a table */

SELECT table_name AS table_alias
```
### INNER JOIN
INNER JOIN joins two tables based on a condition known as a join predicate. It compares each row from the first table with each row from the second table.
- If the join condition is satisfied, a new row with all attributes from each table will be included in the result set
- Else, the row is ignored

Syntax: 
```sql
SELECT column_list
FROM table_1
INNER JOIN table_2 ON join_condition;

# OR, if the column used for matching in both tables is the same

INNER JOIN table_2 USING (column_name);
```
- INNER JOIN is the default join type; as such, the keyword INNER may be omitted

### LEFT JOIN
The LEFT JOIN is a type of outer join and also requires a join predicate. For each row in the left table, the LEFT JOIN compares with every row in the right table.
- If the join condition is satisfied, a new row with all attributes from each table will be included in the result set
- Else, a new row is still created, containing columns of the row in the left table and `NULL` for columns of the row in the right table
Ergo, all rows from left table is outputted in the result set, regardless if there are matching rows in the right table. 

Syntax: 
```sql
SELECT column_list 
FROM table_1 
LEFT JOIN table_2 ON join_condition;

# OR, if the column used for matching in both tables is the same

LEFT JOIN table_2 USING (column_name);
```
### RIGHT JOIN
The RIGHT JOIN is similar to the LEFT JOIN except that the treatment of left and right tables is reversed. The RIGHT JOIN clause selects all rows from the right table and matches rows in the left table.
- If the join condition is satisfied, a new row with all attributes from each table will be included in the result set
- Else, a new row is still created, containing columns of the row in the right table and `NULL` for columns of the row in the left table
Ergo, all rows from right table is outputted in the result set, regardless if there are matching rows in the left table. 

Syntax: 
```sql
SELECT column_list 
FROM table_1 
RIGHT JOIN table_2 ON join_condition;

# OR, if the column used for matching in both tables is the same

RIGHT JOIN table_2 USING (column_name);
```
- Any RIGHT JOIN can be represented using a LEFT JOIN and vice versa
### CROSS JOIN
CROSS JOIN makes a cartesian product of rows from the joined tables. The cross join combines each row from the first table with every row from the right table to make the result set.

Syntax:
```sql
SELECT select_list
FROM table_1
CROSS JOIN table_2;
```
### Self Join
The self join is a technique that joins a table to itself. In MySQL, since it does not have a specific syntax, self-joins are performed via a regular join or left join. 

Since you reference the same table within a single query, you need to use table aliases to assign the table a temporary name when you reference it for the second time.

## Grouping Data
### GROUP BY
The GROUP BY clause groups rows into summary rows based on column values or expressions. It returns one row per group. 

Syntax:
```sql
SELECT 
    c1, c2,..., cn, aggregate_function(ci)
FROM
    table_name
WHERE
    conditions
GROUP BY c1 , c2,...,cn;
```
- GROUP BY comes after the FROM and WHERE clauses, and before ORDER BY and LIMIT
- In practice, GROUP BY is often used with aggregate functions such as SUM, AVG, MAX, MIN and COUNT, that appears in the SELECT clause
- If you use a GROUP BY clause without useing an aggregate function in the SELECT statement, it behaves like the DISTINCT clause

Evaluation Order: `FROM` -> `WHERE` -> `GROUP BY` -> `SELECT` -> `DISTINCT` -> `ORDER BY` -> `LIMIT`
### HAVING
The HAVING clause allows you to apply a condition to the groups returned by the GROUP BY clause and only include groups that meet the specified condition. 

Syntax:
```sql
SELECT 
    select_list
FROM 
    table_name
WHERE 
    search_condition
GROUP BY 
    group_by_expression
HAVING 
    group_condition;
```
Evaluation Order: `FROM` -> `WHERE` -> `GROUP BY` -> `HAVING -> `SELECT` -> `DISTINCT` -> `ORDER BY` -> `LIMIT`

### HAVING COUNT
When you combine the GROUP BY clause with the COUNT function, you will get both the groups and the number of items in each group.

Syntax:

```sql
SELECT 
  c1, 
  COUNT(c2) 
FROM 
  table_1 
GROUP BY 
  c1
HAVING 
  COUNT(c2)...
```

### ROLLUP
The ROLLUP is an extension of the GROUP BY clause. The ROLLUP option allows you to include extra rows that represent the subtotals, which are commonly referred to as super-aggregate rows, along with the grand total row. 
- The ROLLUP assumes a hierarchy among the input columns. For example, if the input column is (c1,c2), the hierarchy c1 > c2

Syntax:
```sql
SELECT 
    select_list
FROM 
    table_name
GROUP BY 
    c1, c2, c3 WITH ROLLUP; # MySQL specific syntax
```

## Subqueries
### Subquery
A subquery is a query nested within another query such as SELECT, INSERT, UPDATE or DELETE. When executing the query, the subquery is evaluated first and the outer query uses the result of the subquery. It can be used in the FROM and WHERE clauses

Example:
```sql
SELECT 
    lastName, firstName
FROM
    employees
WHERE
    officeCode IN 	(SELECT 
            			officeCode
			FROM
           			offices
        		WHERE
            			country = 'USA');
```
### Correlated Subquery
A correlated subquery is a subquery that uses the data from the outer query. In other words, a correlated subquery depends on the outer query. A correlated subquery is evaluated once for each row in the outer query.

### Derived Table
A derived table is a virtual table returned from a SELECT statement.
- A derived table is similar to a temporary table, but using a derived table in the SELECT statement is much simpler than a temporary table because it does not require creating a temporary table.
- Unlike a subquery, a derived table must have an alias so that you can reference its name later in the query.

Syntax:
```sql
SELECT 
    select_list
FROM
    (SELECT 
        select_list
    FROM
        table_1) derived_table_name
WHERE 
    derived_table_name.c1 > 0;
```

### EXISTS/NOT EXISTS
The EXISTS operator is used to test for the existence of any record in a subquery. The EXISTS operator returns TRUE if the subquery returns one or more records.

```sql
SELECT 
    column_name(s)
FROM
    table_name
WHERE EXISTS
    (SELECT
	column_name(s)
     FROM
	table_name
     WHERE
	condition);
```
## Set Operators

### UNION
The UNION operator combines two or more result sets of queries into a single set (essentially appends rows).

Syntax:
```sql
SELECT column_list
UNION [DISTINCT | ALL]
SELECT column_list
UNION [DISTINCT | ALL]
SELECT column_list
```
- The number and order of columns that appear in the SELECT statements must be the same
- The data types of columns must be the same or compatible
- By default, UNION uses the DISTINCT option if you omit the keyword
- ALL keyword keeps the duplicate rows

### EXCEPT
The EXCEPT operator retrieves rows from the query1 result set that don't appear in the query 2 result set.

Syntax:
```sql
query1
EXCEPT [ALL | DISTINCT]
query2;
```
- The number and order of columns that appear in the result sets must be the same
- The data types of columns must be the same or compatible
- By default, EXCEPT uses the DISTINCT option if you omit the keyword

### INTERSECT
The INTERSECT operator compares the result sets of two queries and returns the common rows.
  
Syntax:
```sql
query1
INTERSECT [ALL | DISTINCT]
query2;
```
- The order and number of columns in the two result sets must be the same
- The data types of columns must be the same or compatible
- By default, INTERSECT uses the DISTINCT option if you omit the keyword
  
## Database Management
### CREATE DATABASE
### DROP DATABASE

## Working With Tables
### CREATE TABLE
### AUTO-INCREMENT
### ALTER TABLE
### Renaming Tables
### Removing a column from a table
### Adding a column to a table
### DROP TABLE
### Temporary tables
### TRUNCATE TABLE
### Generated columns

## Constraints

## Data Types

## Modifying Data

## Common Table Expressions

### Common Table Expressions

A common table expression is a named temporary result set that exists solely within the execution scope of a single SQL statement, such as SELECT, INSERT, UPDATE, or DELETE. It can be used to break down complex queries into simpler, more manageable queries. 

Syntax:

```sql
WITH cte_name (column_list) AS (
    query
)
```
- Define a CTE with the name `cte_name`. You can specify a list of columns that the CTE will have, but that is optional
- The query result is what is stored in the CTE
- An example of how you can use a CTE: `SELECT * FROM cte_name`
















