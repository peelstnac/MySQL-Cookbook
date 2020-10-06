# MySQL Cookbook
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
* Use AS for column alias.
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