# DBMS Cheat Sheet

## Table of Content

- [Some of The Most Important SQL Commands](#some-of-the-most-important-sql-commands)
- [Create Database](#create-database)
- [Drop Database](#drop-database)
- [Backup Database](#backup-database)
- [Create Table](#create-table)
- [Drop Table](#drop-table)
- [Truncate Table](#truncate-table)
- [Alter Table](#alter-table)
- [Constraints](#constraints)
- [Date Data Types](#date-data-types)
- [View](#view)
- [Datatype](#datatype)
- [Functions](#functions)
- [Select](#select)
- [Where](#where)
- [And Or Not](#and-or-not)
- [Order By](#order-by)
- [Insert](#insert)
- [Null](#null)
- [Update](#update)
- [Delete](#delete)
- [Wildcard and Like](#wildcard-and-like)
- [In](#in)
- [Between](#between)
- [Aliases](#aliases)
- [Join](#join)
- [Union](#union)
- [Group by](#group-by)
- [Having](#having)
- [Exists](#exists)
- [Any All](#any-all)
- [Select Into](#select-into)
- [Insert Into Select](#insert-into-select)
- [Case](#case)
- [Null Functions](#null-functions)
- [Stored Procedures](#stored-procedures)

---

## Some of The Most Important SQL Commands

- `SELECT` extracts data from a database
- `UPDATE` updates data in a database
- `DELETE` deletes data from a database
- `INSERT INTO` inserts new data into a database
- `CREATE DATABASE` creates a new database
- `ALTER DATABASE` modifies a database
- `CREATE TABLE` creates a new table
- `ALTER TABLE` modifies a table
- `DROP TABLE` deletes a table
- `CREATE INDEX` creates an index (search key)
- `DROP INDEX` deletes an index

---

## Create Database

The following command will create database called "testDB":

```sql
CREATE DATABASE TestDB;
```

## Drop Database

The following command will delete database called "testDB":

```sql
DROP DATABASE TestDB;
```

## Backup Database

This command is used to create a full back up of an existing SQL database.

```sql
BACKUP DATABASE DatabaseName TO DISK = 'filepath';
```

A differential back up only backs up the parts of the database that have changed since the last full database backup.

```sql
BACKUP DATABASE DatabaseName TO DISK = 'filepath' WITH DIFFERENTIAL;
```

## Create Table

The following command creates a table called "Persons" that contains five columns: PersonID, LastName, FirstName, Address, and City:

```sql
CREATE TABLE Persons (
    PersonID int,
    LastName varchar(255),
    FirstName varchar(255),
    Address varchar(255),
    City varchar(255)
);
```

A copy of an existing table can also be created using `CREATE TABLE`. It can have same number of columns and names.

If you create a new table using an existing table, the new table will be filled with the existing values from the old table. The following command creates a new table called "TestTables" (which is a copy of the "Customers" table):

```sql
CREATE TABLE TestTable AS
SELECT CustomerName, ContactName
FROM customers;
```

## Drop Table

The following command is used to delete an existing table "Persons" in the database.

```sql
DROP TABLE Persons
```

## Truncate Table

This command is used to delete the data inside a table, but not the table itself. The following command deletes all the rows present in the table "Persons":

```sql
TRUNCATE TABLE Persons
```

## Alter Table

This command is used to add, delete, or modify columns in an existing table and is also used to add and drop various constraints on an existing table.  
The following command adds an "Email" column to the "Customers" table:

```sql
ALTER TABLE Customers ADD Email varchar(255);
```

The following command deletes the "Email" column from the "Customers" table:

```sql
ALTER TABLE Customers DROP COLUMN Email varchar(255);
```

The following command changes the datatype of "DateOfBirth" column to year (2 or 4 digits format) from the "Persons" table:

```sql
ALTER TABLE Persons MODIFY DateOfBirth year;
```

## Constraints

Constraints are used to limit the type of data that can go into a table. This ensures the accuracy and reliability of the data in the table. If there is any violation between the constraint and the data action, the action is aborted.

Constraints can be **column level** or **table level**. Column level constraints apply to a column, and table level constraints apply to the whole table.

- ### `NOT NULL` - Ensures that a column cannot have a NULL value

  ```sql
  CREATE TABLE Persons (
  ID int NOT NULL,
  LastName varchar(255) NOT NULL,
  FirstName varchar(255) NOT NULL,
  Age int
  );
  ```

  ```sql
  ALTER TABLE Persons MODIFY Age int NOT NULL;
  ```

- ### `UNIQUE` - Ensures that all values in a column are different

  ```sql
  CREATE TABLE Persons (
    ID int NOT NULL UNIQUE,
    LastName varchar(255) NOT NULL,
    FirstName varchar(255),
    Age int
  );
  ```

  ```sql
  ALTER TABLE Persons ADD UNIQUE (ID);
  ```

  To name a `UNIQUE` constraint, and to define a `UNIQUE` constraint on multiple columns, use the following command is used:

  ```sql
  CREATE TABLE Persons (
    ID int NOT NULL,
    LastName varchar(255) NOT NULL,
    FirstName varchar(255),
    Age int,
    CONSTRAINT UC_Person UNIQUE (ID, LastName)
  );
  ```

  ```sql
  ALTER TABLE Persons ADD CONSTRAINT UC_Person UNIQUE (ID, LastName);
  ```

  To drop a UNIQUE constraint, use the following command is used:

  ```sql
  ALTER TABLE Persons DROP CONSTRAINT UC_Person;
  ```

  _There is no special query for dropping unnamed constraints_

- ### `PRIMARY KEY` - A combination of a NOT NULL and UNIQUE. Uniquely identifies each row in a table

  **There can be only one primary key in a table.**

  ```sql
  CREATE TABLE Persons (
    ID int NOT NULL PRIMARY KEY,
    LastName varchar(255) NOT NULL,
    FirstName varchar(255),
    Age int
  );
  ```

  ```sql
  ALTER TABLE Persons ADD PRIMARY KEY (ID);
  ```

  In this command there is only ONE PRIMARY KEY (PK_Person). However, the VALUE of the primary key is made up of TWO COLUMNS (ID + LastName).

  ```sql
  CREATE TABLE Persons (
    ID int NOT NULL,
    LastName varchar(255) NOT NULL,
    FirstName varchar(255),
    Age int,
    CONSTRAINT PK_Person PRIMARY KEY (ID,LastName)
  );
  ```

  ```sql
  ALTER TABLE Persons ADD CONSTRAINT PK_Person PRIMARY KEY (ID,LastName);
  ```

  To drop a `PRIMARY KEY` constraint the following command is used:

  ```sql
  ALTER TABLE Persons DROP CONSTRAINT PK_Person;
  ```

- ### `FOREIGN KEY` - Prevents actions that would destroy links between tables

  The `FOREIGN KEY` constraint is used to prevent actions that would destroy links between tables. A `FOREIGN KEY` is a field (or collection of fields) in one table, that refers to the `PRIMARY KEY` in another table.

  ```sql
  CREATE TABLE Orders (
    OrderID int NOT NULL PRIMARY KEY,
    OrderNumber int NOT NULL,
    PersonID int FOREIGN KEY REFERENCES Persons(PersonID)
  );
  ```

  ```sql
  CREATE TABLE Orders (
    OrderID int NOT NULL,
    OrderNumber int NOT NULL,
    PersonID int,
    PRIMARY KEY (OrderID),
    CONSTRAINT FK_PersonOrder FOREIGN KEY (PersonID)
    REFERENCES Persons(PersonID)
  );
  ```

  ```sql
  ALTER TABLE Orders ADD FOREIGN KEY (PersonID) REFERENCES Persons(PersonID);
  ```

  ```sql
  ALTER TABLE Orders ADD CONSTRAINT FK_PersonOrder FOREIGN KEY (PersonID) REFERENCES Persons(PersonID);
  ```

  ```sql
  ALTER TABLE Orders DROP CONSTRAINT FK_PersonOrder;
  ```

- ### `CHECK` - Ensures that the values in a column satisfies a specific condition

  The `CHECK` constraint is used to limit the value range that can be placed in a column. If you define a `CHECK` constraint on a table it can limit the values in certain columns based on values in other columns in the row.

  The following command creates a `CHECK` constraint on the "Age" column when the "Persons" table is created. The `CHECK` constraint ensures that the age of a person must be 18, or older:

  ```sql
  CREATE TABLE Persons (
    ID int NOT NULL,
    LastName varchar(255) NOT NULL,
    FirstName varchar(255),
    Age int CHECK (Age>=18)
  );
  ```

  ```sql
  CREATE TABLE Persons (
    ID int NOT NULL,
    LastName varchar(255) NOT NULL,
    FirstName varchar(255),
    Age int,
    City varchar(255),
    CONSTRAINT CHK_Person CHECK (Age>=18 AND City="Texas")
  );
  ```

  ```sql
  ALTER TABLE Persons ADD CHECK (Age>=18);
  ```

  ```sql
  ALTER TABLE Persons ADD CONSTRAINT CHK_PersonAge CHECK (Age>=18 AND City="Texas");
  ```

  ```sql
  ALTER TABLE Persons DROP CONSTRAINT CHK_PersonAge;
  ```

- ### `DEFAULT` - Sets a default value for a column if no value is specified

  The `DEFAULT` constraint is used to set a default value for a column. The following command sets a DEFAULT value for the "City" column when the "Persons" table is created:

  ```sql
  CREATE TABLE Persons (
    ID int NOT NULL,
    LastName varchar(255) NOT NULL,
    FirstName varchar(255),
    Age int,
    City varchar(255) DEFAULT "Texas"
  );
  ```

  The `DEFAULT` constraint can also be used to insert system values, by using functions like `GETDATE():`

  ```sql
  CREATE TABLE Orders (
    ID int NOT NULL,
    OrderNumber int NOT NULL,
    OrderDate date DEFAULT GETDATE()
  );
  ```

  ```sql
  ALTER TABLE Persons MODIFY City DEFAULT "Texas";
  ```

  ```sql
  ALTER TABLE Persons ALTER COLUMN City DROP DEFAULT;
  ```

- ### `CREATE INDEX` - Used to create and retrieve data from the database very quickly

  The `CREATE INDEX` statement is used to create indexes in tables. Indexes are used to retrieve data from the database more quickly than otherwise. The users cannot see the indexes, they are just used to speed up searches/queries. The following command creates an index on a table where duplicate values are allowed:

  ```sql
  CREATE INDEX index_name ON table_name (column1, column2, ...);
  ```

  The following commands creates a unique index on a table where duplicate values are not allowed:

  ```sql
  CREATE UNIQUE INDEX index_name ON table_name (column1, column2, ...);
  ```

  If you want to create an index on a combination of columns, you can list the column names within the parentheses, separated by commas:

  ```sql
  CREATE INDEX IndexPersonName ON Persons (LastName, FirstName);
  ```

  The `DROP INDEX` statement is used to delete an index in a table.

  ```sql
  DROP INDEX index_name;
  ```

## Date Data Types

- `DATE` - format YYYY-MM-DD
- `DATETIME` - format: YYYY-MM-DD HH:MI:SS.nnn
- `SMALLDATETIME` - format: YYYY-MM-DD HH:MI:SS
- `TIMESTAMP` - format: a unique number

## View

In SQL, a view is a virtual table based on the result-set of an SQL statement. A view contains rows and columns, just like a real table. The fields in a view are fields from one or more real tables in the database. You can add SQL statements and functions to a view and present the data as if the data were coming from one single table. A view is created with the `CREATE VIEW` statement.

The following command creates a view that shows all customers from Brazil:

```sql
CREATE VIEW [Brazil Customers] AS
SELECT CustomerName, ContactName
FROM Customers
WHERE Country = 'Brazil';
```

We can query the view above as follows:

```sql
SELECT * FROM [Brazil Customers];
```

A view can be updated with the `CREATE OR REPLACE VIEW` statement. The following command adds the "City" column to the "Brazil Customers" view:

```sql
CREATE OR REPLACE VIEW [Brazil Customers] AS
SELECT CustomerName, ContactName, City
FROM Customers
WHERE Country = 'Brazil';
```

A view is deleted with the `DROP VIEW` statement. The following command drops the "Brazil Customers" view:

```sql
DROP VIEW [Brazil Customers];
```

## Datatype

The data type of a column defines what value the column can hold: integer, character, money, date and time, binary, and so on.

[Visit W3 Schools Datatype page](https://www.w3schools.com/sql/sql_datatypes.asp)

## Functions

SQL Server has many built-in functions.

[Visit W3 Schools Server function page](https://www.w3schools.com/sql/sql_ref_sqlserver.asp)

---

## Select

The `SELECT` statement is used to select data from a database. The following command selects the "CustomerName" and "City" columns from the "Customers" table:

```sql
SELECT CustomerName, City FROM Customers;
```

The following command selects all the columns from the "Customers" table:

```sql
SELECT * FROM Customers;
```

The `SELECT DISTINCT` statement is used to return only distinct (different) values. The following command selects only the DISTINCT values from the "Country" column in the "Customers" table:

```sql
SELECT DISTINCT Country FROM Customers;
```

The `SELECT TOP` clause is used to specify the number of records to return.

```sql
SELECT column_name(s) FROM table_name WHERE ROWNUM <= number;
```

```sql
SELECT * FROM (SELECT column_name(s) FROM table_name ORDER BY column_name(s)) WHERE ROWNUM <= number;
```

## Where

The `WHERE` clause is used to filter records. The following command selects all the customers from the country "Mexico", in the "Customers" table:

```sql
SELECT * FROM Customers WHERE Country='Mexico';
```

## And Or Not

The `WHERE` clause can be combined with `AND`, `OR`, and `NOT` operators. The `AND` operator displays a record if all the conditions separated by `AND` are TRUE.
The `OR` operator displays a record if any of the conditions separated by `OR` is TRUE. The `NOT` operator displays a record if the condition(s) is `NOT` TRUE.

- ### `AND`

  The following command selects all fields from "Customers" where country is "Germany" AND city is "Berlin":

  ```sql
  SELECT * FROM Customers WHERE Country='Germany' AND City='Berlin';
  ```

- ### `OR`

  The following command selects all fields from "Customers" where country is "Germany" OR "Spain":

  ```sql
  SELECT * FROM Customers WHERE Country='Germany' OR Country='Spain';
  ```

- ### `NOT`

  The following command selects all fields from "Customers" where country is NOT "Germany":

  ```sql
  SELECT * FROM Customers WHERE NOT Country='Germany';
  ```

## Order By

The `ORDER BY` keyword is used to sort the result-set in ascending or descending order. The `ORDER BY` keyword sorts the records in ascending order by default. To sort the records in descending order, use the `DESC` keyword. The following command selects all customers from the "Customers" table, sorted by the "Country" column:

```sql
SELECT * FROM Customers ORDER BY Country;
```

The following command selects all customers from the "Customers" table, sorted DESCENDING by the "Country" column:

```sql
SELECT * FROM Customers ORDER BY Country DESC;
```

The following command selects all customers from the "Customers" table, sorted by the "Country" and the "CustomerName" column. This means that it orders by Country, but if some rows have the same Country, it orders them by CustomerName:

```sql
SELECT * FROM Customers ORDER BY Country, CustomerName;
```

## Insert

The `INSERT INTO` statement is used to insert new records in a table. If you are adding values for all the columns of the table, you do not need to specify the column names in the SQL query. However, make sure the order of the values is in the same order as the columns in the table. The following command inserts a new record in the "Customers" table:

```sql
INSERT INTO Customers (CustomerName, ContactName, Address, City, PostalCode, Country) VALUES ('Cardinal', 'Tom B. Erichsen', 'Skagen 21', 'Stavanger', '4006', 'Norway');
```

## Null

A `NULL` value is different from a zero value or a field that contains spaces. A field with a `NULL` value is one that has been left blank during record creation. It is not possible to test for NULL values with comparison operators, such as =, <, or <>. We will have to use the `IS NULL` and `IS NOT NULL` operators instead. The following command lists all customers with a NULL value in the "Address" field:

```sql
SELECT CustomerName, ContactName, Address FROM Customers WHERE Address IS NULL;
```

The following command lists all customers with a value in the "Address" field:

```sql
SELECT CustomerName, ContactName, Address FROM Customers WHERE Address IS NOT NULL;
```

## Update

The `UPDATE` statement is used to modify the existing records in a table. Be careful when updating records in a table! Notice the `WHERE` clause in the `UPDATE` statement. The `WHERE` clause specifies which record(s) that should be updated. If you omit the `WHERE` clause, all records in the table will be updated. The following command updates the first customer (CustomerID = 1) with a new contact person and a new city:

```sql
UPDATE Customers SET ContactName = 'Alfred Schmidt', City= 'Frankfurt' WHERE CustomerID = 1;
```

## Delete

The `DELETE` statement is used to delete existing records in a table. Be careful when deleting records in a table! Notice the `WHERE` clause in the `DELETE` statement. The `WHERE` clause specifies which record(s) should be deleted. If you omit the `WHERE` clause, all records in the table will be deleted. The following command deletes the customer "Alfreds Futterkiste" from the "Customers" table:

```sql
DELETE FROM Customers WHERE CustomerName='Alfreds Futterkiste';
```

## Wildcard and Like

A wildcard character is used to substitute one or more characters in a string. The `LIKE` operator is used in a `WHERE` clause to search for a specified pattern in a column.

| Symbol | Description                                         |
| ------ | --------------------------------------------------- |
| %      | Represents zero or more characters                  |
| \_     | Represents a single character                       |
| []     | Represents any single character within the brackets |
| ^      | Represents any character not in the brackets        |
| -      | Represents a range of characters                    |

**All the wildcards can also be used in combinations.**

The following SQL statement selects all customers with a City containing the pattern "es":

```sql
SELECT * FROM Customers WHERE City LIKE '%es%';
```

The following SQL statement selects all customers with a City starting with "L", followed by any character, followed by "n", followed by any character, followed by "on":

```sql
SELECT * FROM Customers WHERE City LIKE 'L_n_on';
```

The following SQL statement selects all customers with a City starting with "a", "b", or "c":

```sql
SELECT * FROM Customers WHERE City LIKE '[a-c]%';
```

The two following SQL statements select all customers with a City NOT starting with "b", "s", or "p":

```sql
SELECT * FROM Customers WHERE City LIKE '[!bsp]%';
```

## In

The `IN` operator allows you to specify multiple values in a `WHERE` clause. The `IN` operator is a shorthand for multiple `OR` conditions. The following SQL statement selects all customers that are located in "Germany", "France" or "UK":

```sql
SELECT * FROM Customers WHERE Country IN ('Germany', 'France', 'UK');
```

The following SQL statement selects all customers that are from the same countries as the suppliers:

```sql
SELECT * FROM Customers WHERE Country IN (SELECT Country FROM Suppliers);
```

## Between

The `BETWEEN` operator selects values within a given range. The values can be numbers, text, or dates. The `BETWEEN` operator is inclusive: begin and end values are included. The following SQL statement selects all products with a price between 10 and 20:

```sql
SELECT * FROM Products WHERE Price BETWEEN 10 AND 20;
```

The following SQL statement selects all products with a price between 10 and 20. In addition; do not show products with a CategoryID of 1,2, or 3:

```sql
SELECT * FROM Products WHERE Price BETWEEN 10 AND 20 AND CategoryID NOT IN (1,2,3);
```

## Aliases

SQL aliases are used to give a table, or a column in a table, a temporary name. Aliases are often used to make column names more readable. An alias only exists for the duration of that query. An alias is created with the `AS` keyword. The following SQL statement creates two aliases, one for the CustomerID column and one for the CustomerName column:

```sql
SELECT CustomerID AS ID, CustomerName AS Customer FROM Customers;
```

_It requires double quotation marks or square brackets if the alias name contains spaces_

The following SQL statement selects all the orders from the customer with CustomerID=4 (Around the Horn). We use the "Customers" and "Orders" tables, and give them the table aliases of "c" and "o" respectively (Here we use aliases to make the SQL shorter):

```sql
SELECT o.OrderID, o.OrderDate, c.CustomerName
FROM Customers AS c, Orders AS o
WHERE c.CustomerName='Around the Horn' AND c.CustomerID=o.CustomerID;
```

## Join

A `JOIN` clause is used to combine rows from two or more tables, based on a related column between them.

- ### `INNER JOIN` (INNER): Returns records that have matching values in both tables

  The following SQL statement selects all orders with customer information:

  ```sql
  SELECT Orders.OrderID, Customers.CustomerName
  FROM Orders
  INNER JOIN Customers ON Orders.CustomerID = Customers.CustomerID;
  ```

  The following SQL statement selects all orders with customer and shipper information:

  ```sql
  SELECT Orders.OrderID, Customers.CustomerName, Shippers.ShipperName
  FROM ((Orders
  INNER JOIN Customers ON Orders.CustomerID = Customers.CustomerID)
  INNER JOIN Shippers ON Orders.ShipperID = Shippers.ShipperID);
  ```

- ### `LEFT JOIN` (OUTER): Returns all records from the left table, and the matched records from the right table

  The following SQL statement will select all customers, and any orders they might have:

  ```sql
  SELECT Customers.CustomerName, Orders.OrderID
  FROM Customers
  LEFT JOIN Orders ON Customers.CustomerID = Orders.CustomerID
  ORDER BY Customers.CustomerName;
  ```

- ### `RIGHT JOIN` (OUTER): Returns all records from the right table, and the matched records from the left table

  The following SQL statement will return all employees, and any orders they might have placed:

  ```sql
  SELECT Orders.OrderID, Employees.LastName, Employees.FirstName
  FROM Orders
  RIGHT JOIN Employees ON Orders.EmployeeID = Employees.EmployeeID
  ORDER BY Orders.OrderID;
  ```

- ### `FULL OUTER JOIN` (OUTER): Returns all records when there is a match in either left or right table
  The following SQL statement selects all customers, and all orders:
  ```sql
  SELECT Customers.CustomerName, Orders.OrderID
  FROM Customers
  FULL OUTER JOIN Orders ON Customers.CustomerID=Orders.CustomerID
  ORDER BY Customers.CustomerName;
  ```
- ### Self Join: A self join is a regular join, but the table is joined with itself.
  The following SQL statement matches customers that are from the same city:
  ```sql
  SELECT A.CustomerName AS CustomerName1, B.CustomerName AS CustomerName2, A.City
  FROM Customers A, Customers B
  WHERE A.CustomerID <> B.CustomerID
  AND A.City = B.City
  ORDER BY A.City;
  ```

## Union

The `UNION` operator is used to combine the result-set of two or more `SELECT` statements. Every SELECT statement within UNION must have the same number of columns. The columns must also have similar data types. The columns in every SELECT statement must also be in the same order. The following SQL statement returns the cities (only distinct values) from both the "Customers" and the "Suppliers" table:

```sql
SELECT City FROM Customers UNION SELECT City FROM Suppliers ORDER BY City;
```

The following SQL statement returns the cities (duplicate values also) from both the "Customers" and the "Suppliers" table:

```sql
SELECT City FROM Customers UNION ALL SELECT City FROM Suppliers ORDER BY City;
```

## Group by

The `GROUP BY` statement groups rows that have the same values into summary rows, like "find the number of customers in each country". The `GROUP BY` statement is often used with aggregate functions (`COUNT()`, `MAX()`, `MIN()`, `SUM()`, `AVG()`) to group the result-set by one or more columns. The following SQL statement lists the number of customers in each country:

```sql
SELECT COUNT(CustomerID), Country
FROM Customers
GROUP BY Country;
```

The following SQL statement lists the number of orders sent by each shipper:

```sql
SELECT Shippers.ShipperName, COUNT(Orders.OrderID) AS NumberOfOrders FROM Orders
LEFT JOIN Shippers ON Orders.ShipperID = Shippers.ShipperID
GROUP BY ShipperName;
```

## Having

The `HAVING` clause was added to SQL because the `WHERE` keyword cannot be used with aggregate functions. The following SQL statement lists the number of customers in each country. Only include countries with more than 5 customers:

```sql
SELECT COUNT(CustomerID), Country
FROM Customers
GROUP BY Country
HAVING COUNT(CustomerID) > 5;
```

The following SQL statement lists the number of customers in each country, sorted high to low (Only include countries with more than 5 customers):

```sql
SELECT COUNT(CustomerID), Country
FROM Customers
GROUP BY Country
HAVING COUNT(CustomerID) > 5
ORDER BY COUNT(CustomerID) DESC;
```

## Exists

The `EXISTS` operator is used to test for the existence of any record in a subquery. The following SQL statement returns TRUE and lists the suppliers with a product price less than 20:

```sql
SELECT SupplierName
FROM Suppliers
WHERE EXISTS (SELECT ProductName FROM Products WHERE Products.SupplierID = Suppliers.supplierID AND Price < 20);
```

## Any All

The `ANY` and `ALL` operators allow you to perform a comparison between a single column value and a range of other values.

- ### `ANY`: returns a boolean value as a result; returns TRUE if ANY of the subquery values meet the condition

  The following SQL statement lists the ProductName if it finds ANY records in the OrderDetails table has Quantity equal to 10:

  ```sql
  SELECT ProductName
  FROM Products
  WHERE ProductID = ANY
  (SELECT ProductID
  FROM OrderDetails
  WHERE Quantity = 10);
  ```

- ### `ALL`: returns a boolean value as a result; returns TRUE if ALL of the subquery values meet the condition; is used with SELECT, WHERE and HAVING statements

  The following SQL statement lists ALL the product names:

  ```sql
  SELECT ALL ProductName
  FROM Products
  WHERE TRUE;
  ```

  The following SQL statement lists the ProductName if ALL the records in the OrderDetails table has Quantity equal to 10:

  ```sql
  SELECT ProductName
  FROM Products
  WHERE ProductID = ALL
  (SELECT ProductID
  FROM OrderDetails
  WHERE Quantity = 10);
  ```

## Select Into

The `SELECT INTO` statement copies data from one table into a new table. The following SQL statement creates a backup copy of Customers:

```sql
SELECT * INTO CustomersBackup2017 FROM Customers;
```

The following SQL statement uses the IN clause to copy the table into a new table in another database:

```sql
SELECT * INTO CustomersBackup2017 IN 'Backup.mdb' FROM Customers;
```

## Insert Into Select

The `INSERT INTO SELECT` statement copies data from one table and inserts it into another table. The `INSERT INTO SELECT` statement requires that the data types in source and target tables matches. The following SQL statement copies "Suppliers" into "Customers":

```sql
INSERT INTO Customers (CustomerName, City, Country)
SELECT SupplierName, City, Country FROM Suppliers;
```

The following SQL statement copies only the German suppliers into "Customers":

```sql
INSERT INTO Customers (CustomerName, City, Country)
SELECT SupplierName, City, Country FROM Suppliers
WHERE Country='Germany';
```

## Case

The `CASE` statement goes through conditions and returns a value when the first condition is met (like an if-then-else statement). So, once a condition is true, it will stop reading and return the result. If no conditions are true, it returns the value in the `ELSE` clause. If there is no `ELSE` part and no conditions are true, it returns NULL. The following SQL goes through conditions and returns a value when the first condition is met:

```sql
SELECT OrderID, Quantity,
CASE
    WHEN Quantity > 30 THEN 'The quantity is greater than 30'
    WHEN Quantity = 30 THEN 'The quantity is 30'
    ELSE 'The quantity is under 30'
END AS QuantityText
FROM OrderDetails;
```

The following SQL will order the customers by City. However, if City is NULL, then order by Country:

```sql
SELECT CustomerName, City, Country
FROM Customers
ORDER BY
(CASE
    WHEN City IS NULL THEN Country
    ELSE City
END);
```

## Null Functions

The Oracle `NVL()` function changes the value to provided value if null:

```sql
SELECT ProductName, UnitPrice * (UnitsInStock + NVL(UnitsOnOrder, 0)) FROM Products;
```

## Stored Procedures

A stored procedure is a prepared SQL code that you can save, so the code can be reused over and over again. So if you have an SQL query that you write over and over again, save it as a stored procedure, and then just call it to execute it. You can also pass parameters to a stored procedure, so that the stored procedure can act based on the parameter value(s) that is passed. The following SQL statement creates a stored procedure named "SelectAllCustomers" that selects all records from the "Customers" table:

```sql
CREATE PROCEDURE SelectAllCustomers
AS
SELECT * FROM Customers
GO;
```

Execute the stored procedure above as follows:

```sql
EXEC SelectAllCustomers;
```

The following SQL statement creates a stored procedure that selects Customers from a particular City with a particular PostalCode from the "Customers" table:

```sql
CREATE PROCEDURE SelectAllCustomers @City nvarchar(30), @PostalCode nvarchar(10)
AS
SELECT * FROM Customers WHERE City = @City AND PostalCode = @PostalCode
GO;
```

Execute the stored procedure above as follows:

```sql
EXEC SelectAllCustomers @City = 'London', @PostalCode = 'WA1 1DP';
```
