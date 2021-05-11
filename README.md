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
