# SQL-101
Welcome to my SQL Study Notes repository! This collection is designed to serve as a quick reference guide for anyone interested in learning SQL, from beginners to those looking to refresh their knowledge.

Feel free to explore, contribute, and use these notes as a handy reference for your SQL learning journey. Let's dive into the world of SQL together!

## Table of Contents
1. [Introduction](#introduction)
2. [Querying Data](#querying-data)
3. [Sorting Data](#sorting-data)
4. [Filtering Data](#filtering-data)
5. [Joining Tables](#joining-tables)

   
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

**Syntax:**

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

**Evaluation Order:** `FROM` -> `SELECT`

**Example:** The employees table has eight columns: employeeNumber, lastName, firstName, extension, email, officeCode, reportsTo, and jobTitle.

```sql
SELECT 
    lastname, 
    firstname, 
    jobtitle
FROM
    employees
```
**Output:**

```sql
| lastname  | firstname | jobtitle             |
|-----------|-----------|----------------------|
| Murphy    | Diane     | President            |
| Patterson | Mary      | VP Sales             |
| Firrelli  | Jeff      | VP Marketing         |
| Patterson | William   | Sales Manager (APAC) |
| Bondur    | Gerard    | Sale Manager (EMEA)  |
...
```
  
### SELECT DISTINCT

When querying data from a table, you may get duplicate rows. To remove these duplicate rows, a `DISTINCT` clause is used in the `SELECT` statement.

**Syntax:**

```sql
SELECT DISTINCT
  select_list
FROM
  table_name;
```
**Evaluation order:** `FROM` -> `SELECT` -> `DISTINCT`

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

**Syntax:**

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

**Evaluation order:** `FROM` -> `SELECT` -> `DISTINCT` -> `ORDER BY`

**Example:** 

```sql
SELECT 
  firstName, 
  lastName, 
  reportsTo 
FROM 
  employees 
ORDER BY 
  reportsTo;
```

**Output:**
```sql

| firstName | lastName  | reportsTo |
|-----------|-----------|-----------|
| Diane     | Murphy    |      NULL |
| Mary      | Patterson |      1002 |
| Jeff      | Firrelli  |      1002 |
| William   | Patterson |      1056 |
| Gerard    | Bondur    |      1056 |
...
```

## Filtering Data

### WHERE
The WHERE clause allows you to specify a search condition for the rows returned by a query (filter rows by condition).

**Syntax:**

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
  
**Evaluation Order:** `FROM` -> `WHERE` -> `SELECT` -> `DISTINCT` -> `ORDER BY`

**Example:** 

```sql
SELECT 
    lastname, 
    firstname, 
    jobtitle,
    officeCode
FROM
    employees
WHERE
    jobtitle = 'Sales Rep'
```

**Output:**
```sql

| lastname  | firstname | jobtitle  |
|-----------|-----------|-----------|
| Jennings  | Leslie    | Sales Rep |
| Thompson  | Leslie    | Sales Rep |
| Firrelli  | Julie     | Sales Rep |
| Patterson | Steve     | Sales Rep |
| Tseng     | Foon Yue  | Sales Rep |
| Vanauf    | George    | Sales Rep |
| Bondur    | Loui      | Sales Rep |

```

### AND
The `AND` operator can be used to combine multiple Boolean expressions to filter data. The AND operator returns true when both expressions are true; otherwise, it returns false.

**Example:** 
```sql
SELECT 
    customername, 
    country, 
    state, 
    creditlimit
FROM
    customers
WHERE
    country = 'USA' AND 
    state = 'CA' AND 
    creditlimit > 100000;
```
**Output:**
```sql
| customername                 | country | state | creditlimit |
|------------------------------|---------|-------|-------------|
| Mini Gifts Distributors Ltd. | USA     | CA    |   210500.00 |
| Collectable Mini Designs Co. | USA     | CA    |   105000.00 |
| Corporate Gift Ideas Co.     | USA     | CA    |   105000.00 |
```
### OR

The `OR` operator can be used to combine multiple Boolean expressions to filter data. The AND operator returns true when either expressions are true; otherwise, it returns false. SQL evaluates the OR operator after the AND operator if an expression contains both AND and OR operators.

**Example:** 
```sql
SELECT    
	customername, 
	country
FROM    
	customers
WHERE country = 'USA' OR 
      country = 'France';
```
**Output:**
```sql
| customername                 | country |
|------------------------------|---------|
| Atelier graphique            | France  |
| Signal Gift Stores           | USA     |
| La Rochelle Gifts            | France  |
| Mini Gifts Distributors Ltd. | USA     |
| Mini Wheels Co.              | USA     |
| Land of Toys Inc.            | USA     |
| Saveley & Henriot, Co.       | France  |
| Muscle Machine Inc           | USA     |
| Diecast Classics Inc.        | USA     |
...
```

### IN/NOT IN
Used in the WHERE clause, the IN operator allows you to determine if a value matches any value in a list of values.

**Syntax:**

```sql
value IN (value1, value2, value3,...)

value NOT IN (value1, value2, value3,...)
```
- Functionally, it is equivalent to a combination of `OR` statements
- Adding the NOT clause negates the expression and returns TRUE if the value does not match any values in the list

### BETWEEN/NOT BETWEEN
Used in the WHERE clause, the BETWEEN operator is a logical operator that tests whether a value is in a range or not. 

**Syntax:**
```sql
value BETWEEN val1 AND val2;

value NOT BETWEEN val1 AND val2;

```
- Adding the NOT clause negates the expression and returns TRUE if the value is outside of the specified range
  
### LIKE/NOT LIKE
Used in the WHERE clause, the LIKE operator is a logical operator that tests whether a string contains a specified pattern or not.

**Syntax:**

```sql
expression LIKE pattern ESCAPE escape_character

expression NOT LIKE pattern ESCAPE escape_character
```

- If the expression matches the pattern, the LIKE operator returns TRUE. Otherwise, it returns FALSE.
- Two wildcard characters can be used to construct the pattern
  - The percentage `%` wildcard matches any string of zero or more characters.
  - The underscore `_` wildcard matches any single character.
- Adding the NOT clause negates the expression and returns TRUE if the string does not contain the specified pattern

**Example:** This example uses the LIKE operator to find employees whose first names start with the letter a.
```sql
SELECT 
    employeeNumber, 
    lastName, 
    firstName
FROM
    employees
WHERE
    firstName LIKE 'a%';
```
**Output:**
```sql
| employeeNumber  | lastname  | firstname        |
|-----------------|-----------|------------------|
| 1143            | Bow       | Anthony          |
| 1611            | Fixter    | Andy             |

```
### LIMIT

The LIMIT clause is used in the SELECT statement to constrain the number of rows to return. 

**Syntax:**
```sql
SELECT 
    select_list
FROM
    table_name
LIMIT [offset,] row_count;
```
- The offset specifies the offset of the first row to return. The offset of the first row is 0, not 1
- The row_count specifies the maximum number of rows to return

**Evaluation Order:** `FROM` -> `WHERE` -> `SELECT` -> `DISTINCT` -> `ORDER BY` ->`LIMIT`

### IS NULL/IS NOT NULL
IS NULL is a comparison operator used to test whether a value is NULL or not. IS NOT NULL checks whether a value is not NULL. This operator can be used in the SELECT or WHERE clauses.

**Syntax:**
```sql
value IS NULL

value IS NOT NULL
```

## Joining Tables

## Grouping Data

## Subqueries

## Set Operators

## Database Management

