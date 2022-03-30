# Literal table

Instead of column name, you can put a literal after `SELECT`.
Doing so will return it as a table:

```sql
SELECT
    'abc' AS 'letter',
    123 AS 'number';
```

    +--------+--------+
    | letter | number |
    +--------+--------+
    | abc    | 123    |
    +--------+--------+

Note that a number will always be interpretted as a literal as field names cannot start with numeral.

You may also "select" it "from" a table.
Doing so makes it repeat a number of times, depending on structure of the table:

```sql
SELECT
    'abc' AS 'letter',
    123 AS 'number',
    `ENAME`
FROM
    EMP;
```

    +--------+--------+--------+
    | letter | number | ENAME  |
    +--------+--------+--------+
    | abc    | 123    | SMITH  |
    | abc    | 123    | ALLEN  |
    | abc    | 123    | WARD   |
    | abc    | 123    | JONES  |
    | abc    | 123    | MARTIN |
    | abc    | 123    | BLAKE  |
    | abc    | 123    | CLARK  |
    | abc    | 123    | SCOTT  |
    | abc    | 123    | KING   |
    | abc    | 123    | TURNER |
    | abc    | 123    | ADAMS  |
    | abc    | 123    | JAMES  |
    | abc    | 123    | FORD   |
    | abc    | 123    | MILLER |
    +--------+--------+--------+

This can be explained by interpretting column names as variables and literals as values.
