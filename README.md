# MySQL Cookbook
* Not self-contained.

## USE
* Used to select a database.
```bash
mysql -u username -p
USE database_name;
```
or
```bash
mysql -u username -D database_name -p
```
* SELECT the current database.
```sql
SELECT database();
```
* SHOW stuff.
```sql
SHOW DATABASES;
SHOW TABLES;
```

## CREATE DATABASE
* Does exactly what it says.
* Optional IF NOT EXISTS clause.
```sql
CREATE DATABASE IF NOT EXISTS database_name;
```

## SHOW DATABASES
* Does exactly what it says.

## DROP DATABASE
* Deletes a database.
* Optional IF EXISTS clause.
```sql
CREATE DATABASE db;
DROP DATABASE IF EXISTS db;
```

## CREATE TABLE
* Does exactly what it says.
* Optional IF NOT EXISTS clause.
* Loads of options. Consult documentation.
```sql
CREATE TABLE table_name(
    column_1 INT AUTO_INCREMENT PRIMARY KEY,
    column_3 VARCHAR(255) NOT NULL UNIQUE DEFAULT 'DEFAULT',
    column_4 TEXT,
    column_5 DECIMAL CHECK(column_5 >= 0)
);
```
* Common datatypes: INT, DECIMAL, VARCHAR(), TEXT, DATE, TIMESTAMP.
* Common limiters: PRIMARY KEY, FOREIGN KEY, AUTO_INCREMENT, UNIQUE, CHECK, DEFAULT.
* Describe the table using ```DESCRIBE table_name;```.

## FOREIGN KEY
* Used to maintain relational integrity between tables.
    * One customer may have many purchases.
    * Each purchose only relates to one customer.
    * FOREIGN keys can help maintain this relationship.
    * FOREIGN KEY takes in a list of columns that match with the list REFERENCES takes in.
```sql
CREATE TABLE customer(
    id INT AUTO_INCREMENT PRIMARY KEY,
    cname VARCHAR(255) NOT NULL
);

CREATE TABLE purchases(
    id INT AUTO_INCREMENT PRIMARY KEY,
    CONSTRAINT fk_constraint
    FOREIGN KEY (customer_id)
        REFERENCES customer(id)
        ON UPDATE CASCADE
        ON DELETE CASCADE
);
```
* Different ON UPDATE/DELETE modifiers.
    * CASCADE, RESTRICT, SET NULL.
    * Default is RESTRICT.
    * CASCADE mimicks the action.
    * RESTRICT prevents the action done on parent.
    * SET NULL sets null upon action.
* Disable FOREIGN KEY checks.
    * Useful when importing data.
    * No need to worry about import order.
```sql
SET foreign_key_checks = 0;
```
* Refer to constraint name when performing ALTER.

## INSERT
* Does exactly what it says.
```sql
INSERT INTO table_name(column_1, column_2, column_3)
VALUES
    (v_1, 'YYYY-MM-DD', DEFAULT),
    (v_3, v_4, v_5);
```
* Can combine SELECT with INSERT.
```sql
INSERT INTO table_name(column_1, column_2, column_3)
SELECT
    column_1,
    column_2,
    column_3
FROM
    another_table
WHERE
    column_1 = 'SOME PREDICATE';

INSERT INTO table_name(column_1, column_2, column_3)
VALUES(
    SELECT COUNT(*) column_1 FROM another_table
);
```
* Can use IGNORE to omit error causing rows.
    * For example, a row may violate UNIQUE constraint.
    * Without IGNORE, whole insert operation is cancelled.
```sql
INSERT IGNORE INTO table_name(column_1)
VALUES(some_value);

SHOW WARNINGS;
```
* If STRICT mode is on, MySQL will try to change error causing data before issuing a warning.

## UPDATE
* Updates data in a table.
```sql
UPDATE IGNORE table_name
SET
    column_1 = 100,
    column_2 = 121,
    column_3 = 144
WHERE
    column_1 = 81;
```

## DELETE
* Delete data from a table.
```sql
DELETE FROM table_name
WHERE
    column_1 = 10;

DELETE FROM table_name
ORDER BY
    column_1
LIMIT 10;
```

## DELETE JOIN
```sql
DELETE t1, t2 FROM table_1 as t1
LEFT JOIN table_2 as t2
    ON t1.id = t2.id;
WHERE
    t2.property IS NULL;
```

## REPLACE
* Replace existing value if key collides. If no key collision or no key is given at all, insert.
```sql
REPLACE INTO table_name(column_1, column_2)
VALUES(1, 1);
```
## SELECT FROM
* To select three columns from a table, use the SELECT FROM pairing.
* MySQL reads FROM before SELECT.
```sql
SELECT 
    column_1, column_2, column_three
FROM
    table_name;
```
* Can use DISTINCT to filter out duplicates.
    * Makes sure the selected columns are unique.
```sql
SELECT DISTINCT
    column_1, column_2, column_three
FROM
    table_name;
```

## ORDER BY
* Sort selected columns.
* ASC (ascending) by default.
* MySQL reads ORDER BY after SELECT FROM pairing.
```sql
SELECT
    column_1, column_2
FROM
    table_name
ORDER BY
    column_1,
    column_2 DESC;
```

## IFNULL()
* Takes in two expressions. If first is NULL, return second.

## FIELD()
* Custom sorting. ORDER BY sorts by value of column entries.
* FIELD([column_name], order_1, order_2, ...) maps column entry to positive integer.
    * Here, the column entry value order_1 is mapped to the number 1.
```sql
SELECT
    column_1, column_strings
FROM
    table_name
ORDER BY
    FIELD(column_strings,
        'string_3',
        'string_2',
        'string_1'
    );
```

## AS
* Can perform arithmetic operations with SELECT FROM pairing.
* Use AS for column/table alias (optional).
* Arithmetic operators are +, -, *, /, DIV (integer division), %, MOD.
```sql
SELECT
    column_1,
    column_2 + column_3 AS total
FROM
    table_name
ORDER BY
    total desc;
```

## WHERE
* Used to filter rows.
* Filters are logical predicates using =, AND, OR, NOT.
* Predicates can evaluate to TRUE, FALSE, or UNKNOWN.
* WHERE is read by MySQL after FROM, but before SELECT.
    * FROM to WHERE to SELECT to ORDER BY.
    * Logical because WHERE may reference non SELECT columns.
* Can use [VALUE] BETWEEN 1 AND 5 as predicate.
    * Range is inclusive.
* LIKE compares strings with wildcards.
    * % For 0 or more characters.
    * _ for one character.
* IN enumerates through a list.
    * [VALUE] IN (v_1, v_2, ...).
* IS NULL predicate to check if NULL.

```sql
SELECT
    column_1, range_values
FROM
    table_name
WHERE
    (column_1 = 'VALUE' OR
    column_1 LIKE '%VALUE') AND
    NOT (range_values BETWEEN 1 AND 5 OR
    range_values IN (1, 2, 3, 4, 5))
ORDER BY
    range_values DESC;
```

## GROUP BY
* Groups rows into summary rows.
```sql
SELECT column_1, COUNT(*) AS cnt
FROM table_name
GROUP BY column_1;
```

## Aggregate Functions
* Functions which take in multiple values and return one.
    * Hence "aggregate."
* Some include AVG, COUNT, SUM, MAX, MIN.
```sql
SELECT AVG(price) as average_price
FROM purchases;
```

## HAVING
* Predicate on groups.
* If no GROUP BY clause present, works same as WHERE.
```sql
SELECT
    productName,
    SUM(price) as total
FROM
    products
GROUP BY
    productName
HAVING
    total >= 100
ORDER BY
    total;
```

## LIMIT
* Limits number of rows displayed.
* LIMIT 10 OFFSET 4 (or LIMIT 10 4).
    * OFFSET is zero-indexed.
    * SQL reads LIMIT after ORDER BY.
```sql
SELECT DISTINCT
    column_1, column_2, column_3
FROM
    table_name
WHERE
    column_1 IS NOT NULL
ORDER BY
    column_1
LIMIT 10 1;
```

## Subqueries
* Query within a query.
    * Inner/outer.
    * Must be in ().

## ALTER TABLE
* Change table stuff.
```sql
ALTER TABLE table_name
ADD column_1 INT AUTO_INCREMENT PRIMARY KEY,
ADD column_2 TEXT;

ALTER TABLE table_name
MODIFY column_1 INT AUTO_INCREMENT PRIMARY KEY;

ALTER TABLE table_name
CHANGE column_1 column_0 INT AUTO_INCREMENT PRIMARY KEY;

ALTER TABLE table_name
DROP COLUMN column_2;

ALTER TABLE table_name
RENAME TO new_name;
```

## INNER JOIN
* Joins two tables based on a join predicate.
    * Two rows combined if predicate holds.
    * Omission if predicate does not hold.
* Put after SELECT FROM pairing.
```sql
SELECT
    t1.column_1,
    t1.column_2,
    t2.column_1,
    t2.column_2
FROM
    table_1 AS t1
INNER JOIN
    table_2 AS t2
    ON t1.column_1 = t2.column_1;
```

## LEFT JOIN
* Joins two tables based on a join predicate.
    * Two rows combined if predicate holds.
    * Else, combination occurs, but values in right row is NULL.
```sql
SELECT
    t1.column_1,
    t1.column_2,
    t2.column_1,
    t2.column_2
FROM
    table_1 AS t1
LEFT JOIN
    table_2 AS t2
    ON t1.column_1 = t2.column_1
WHERE
    t2.column_2 IS NOT NULL;
```
* The last WHERE clause makes the LEFT JOIN same as INNER JOIN.

## RIGHT JOIN
* Same as LEFT JOIN, but right table takes priority.

## CROSS JOIN
* Returns a cartesian product.
```sql
SELECT
    t1.column_1,
    t1.column_2,
    t2.column_1,
    t2.column_2
FROM
    table_1 AS t1
CROSS JOIN table_2 AS t2;
```