# SQL Cheat Sheet

**Made by:** Vnntnn (67070065)

## Types of SQL Statements
1. **DDL (Data Definition Language)**
    - Create, Alter, Drop, Rename
2. **DML (Data Manipulation Language)**
    - Select, Insert, Delete, Update
3. **Transaction Control**

---

## Data Definition Language (DDL)

### Create Table Object
- Use `CREATE` to create table objects.
- **Example:**
```sql
CREATE TABLE EMP (
     id INT(7),
     last_name VARCHAR(25),
     first_name VARCHAR(25),
     dept_id INT(7)
);
```

### Modify Table Object
- Use `ALTER` to modify table objects:
  - **ADD**
     - **Example:**
     ```sql
     ALTER TABLE TABLENAME
     ADD COLS_NAME DATATYPE[DEFAULT DATA SIZE];
     ```
  - **MODIFY**
     - Modify column:
        - **Example:**
        ```sql
        ALTER TABLE TABLENAME
        MODIFY COLS_NAME DATATYPE[DEFAULT DATA SIZE];
        ```
     - Modify column name:
        ```sql
        ALTER TABLE TABLENAME
        CHANGE OLD_COLS_NAME NEW_COLS_NAME DATATYPE[DEFAULT DATA SIZE];
        ```
     - Alternate way (MySQL v8+):
        ```sql
        ALTER TABLE TABLENAME
        RENAME COLUMN OLD_COLS_NAME TO NEW_COLS_NAME;
        ```
     - Modify table name:
        ```sql
        RENAME TABLE old_table_name TO new_table_name;
        ALTER TABLE old_table_name RENAME new_table_name;
        ```
  - **DROP**
     - Drop column:
        ```sql
        ALTER TABLE TABLENAME
        DROP COLS_NAME;
        ```
     - Drop table:
        ```sql
        DROP TABLE table_name;
        ```
     - Drop table without dropping foreign keys:
        ```sql
        SET FOREIGN_KEY_CHECKS = 0;
        DROP TABLE regions;
        SET FOREIGN_KEY_CHECKS = 1;
        ```

---

## DDL Constraints

### Types of Constraints
1. Primary Key
2. Foreign Key
3. Unique
4. Not Null

- Enforce rules at the table level.
- Prevent deletion if there are dependencies.

### Describe Constraints
- Define constraints at the table or column level (except Not Null).
- View constraints with:
```sql
SELECT TABLE_NAME, COLUMN_NAME, CONSTRAINT_NAME
FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE;
```
or
```sql
SELECT *
FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE
WHERE table_name OR column_name = "Name";
```

### Create and Maintain Constraints
- **Column-level and Table-level Constraints**
  - When creating a table:
     ```sql
     CREATE TABLE table_name (
          col_name datatype[default data size] [col_constraints_type],
          [table_constraints_type] `constraints_name(optional)` (column)
     );
     ```
  - When the table already exists:
     - Use `ALTER` to ADD, MODIFY, or DROP constraints:
        ```sql
        -- Add Constraint
        ALTER TABLE table_name
        ADD [CONSTRAINT constraint_name]
        constraint_type (column);

        -- Add Foreign Key
        ALTER TABLE table_name
        ADD CONSTRAINT constraint_name
        FOREIGN KEY (column_name)
        REFERENCES parent_table(parent_column);

        -- Add Unique
        ALTER TABLE table_name
        ADD CONSTRAINT constraint_name
        UNIQUE (column_name);

        -- Add Not Null
        ALTER TABLE table_name
        MODIFY column_name datatype NOT NULL;

        -- Drop Foreign Key
        ALTER TABLE table_name
        DROP FOREIGN KEY constraint_name;

        -- Drop Primary Key
        ALTER TABLE table_name
        DROP PRIMARY KEY;

        -- Drop Unique
        ALTER TABLE table_name
        DROP INDEX constraint_name;

        -- Drop Not Null
        ALTER TABLE table_name
        MODIFY column_name datatype NULL;
        ```
- **Foreign Key**
  - Add Foreign Key:
     ```sql
     CREATE TABLE Persons (
          PersonID int Primary Key,
          LastName varchar(255),
          FirstName varchar(255),
          Address varchar(255),
          City varchar(255)
     );

     -- Table-level
     CREATE TABLE Orders (
          OrderID int NOT NULL,
          OrderNumber int NOT NULL,
          PersonID int,

          PRIMARY KEY (OrderID),
          FOREIGN KEY (PersonID) REFERENCES
          Persons(PersonID)
          ON DELETE CASCADE
     );

     -- Column-level
     CREATE TABLE Orders (
          OrderID int NOT NULL,
          OrderNumber int NOT NULL,
          PersonID int FOREIGN KEY
          REFERENCES Persons(PersonID)
          ON DELETE CASCADE,

          PRIMARY KEY (OrderID)
     );
     ```

---

## Data Manipulation Language (DML)

### Insert
- Insert into the parent table first, then the child table.
- Specify columns or not:
```sql
INSERT INTO table_name (column1, column2, .... )
VALUES (value1, value2, .....) ;
```

### Update
- Update rows using `UPDATE`:
```sql
UPDATE table
SET column = value [, column = value, ...]
[WHERE condition];
```

### Delete
- Delete rows using `DELETE`:
```sql
DELETE FROM table
[WHERE condition];
```

---

## SQL Select Statement

### SELECT
- Select columns from a table:
```sql
SELECT * | {[DISTINCT] column_name | expression [alias],...}
FROM table_name;
```
- Concatenate strings:
  - `concat('string1','string2','string3',....)`
  - **Example:**
     ```sql
     concat(firstname, ' _ ', lastname)
     ```
- Eliminate duplicates:
```sql
SELECT DISTINCT department_id
FROM employees;
```

### Sorting Rows
- Use `ORDER BY`:
```sql
SELECT *|{[DISTINCT] column_name | expression [alias],...}
FROM table_name
[ORDER BY {column_name, expr} [ASC | DESC]];
```

### WHERE Clause
- Add conditions:
```sql
SELECT *|{[DISTINCT] column_name | expression [alias],...}
FROM table_name
WHERE condition(s);
```

| Operator | Meaning                  |
| -------- | ------------------------ |
| =        | Equal to                 |
| >        | Greater than             |
| >=       | Greater than or equal to |
| <        | Less than                |
| <=       | Less than or equal to    |
| != or <> | Not equal to             |

- Use `LIKE` for string searches:
  - `%` for zero or many unknown characters.
  - `_` for a single unknown character.
  ```sql
  SELECT last_name
  FROM employees
  WHERE last_name LIKE '_o%';
  ```

---

## Sub-query

### Insert
- Insert multiple values from another table:
```sql
INSERT INTO table_name(Col_name)
SELECT Col_name
FROM table_name
WHERE col_name LIKE '_HE%' ESCAPE '%';
```

### Update
- Update using subqueries:
```sql
SET @var := (
     SELECT col_name
     FROM table_name
     WHERE condition
);

UPDATE table_name
SET col_name = @var
WHERE condition;
```

### Delete
- Delete using subqueries:
```sql
DELETE FROM table_name
WHERE col_name = (
     SELECT col_name
     FROM table_name
     WHERE condition
);
```

---

## INNER JOIN and OUTER JOIN

### INNER JOIN
- Return matched rows.
- **Types:**
  1. **Cross Join:**
      ```sql
      SELECT *
      FROM A
      CROSS JOIN B;
      ```
  2. **Equijoin:**
      ```sql
      SELECT *
      FROM Student s, Classroom c
      WHERE s.class_id = c.class_id;
      ```
  3. **Natural Join:**
      ```sql
      SELECT *
      FROM Student
      NATURAL JOIN Classroom;
      ```
  4. **Join with ON Clause:**
      ```sql
      SELECT s.name, c.room
      FROM Student s
      JOIN Classroom c
      ON s.class_id = c.class_id;
      ```
  5. **Join with USING Clause:**
      ```sql
      SELECT s.name, c.room
      FROM Student s
      JOIN Classroom c
      USING (class_id);
      ```
  6. **Self-Join:**
      ```sql
      SELECT e.name AS employee, m.name AS manager
      FROM Employee e
      JOIN Employee m
      ON e.manager_id = m.emp_id;
      ```

### OUTER JOIN
- Return unmatched rows.
- **Types:**
  1. **FULL OUTER JOIN**
  2. **LEFT OUTER JOIN:**
      ```sql
      SELECT s.name, c.room
      FROM Student s
      LEFT OUTER JOIN Classroom c
      ON s.class_id = c.class_id;
      ```
  3. **RIGHT OUTER JOIN:**
      ```sql
      SELECT s.name, c.room
      FROM Student s
      RIGHT OUTER JOIN Classroom c
      ON s.class_id = c.class_id;
      ```

---

## Aggregate Functions

- **Functions:**
  - `AVG()`, `SUM()`, `MIN()`, `MAX()`, `COUNT()`, `STDDEV()`, `VARIANCE()`
- **Example:**
```sql
SELECT AVG(salary), MAX(salary), MIN(salary), SUM(salary)
FROM employees
WHERE job_id LIKE '%REP%';
```

### GROUP BY
- Group data before applying aggregate functions:
```sql
SELECT column, group_function(column)
FROM table
[WHERE condition]
GROUP BY column
[ORDER BY column];
```

### HAVING
- Filter data after applying aggregate functions:
```sql
SELECT column, group_function
FROM table
WHERE condition
GROUP BY column
HAVING group_condition
ORDER BY column;
```
