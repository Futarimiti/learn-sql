# Conditional query

Where only data fitting the conditions may be selected.

### Conditional statements

#### `WHERE`

```sql
SELECT ... FROM ... WHERE ...;
```

`WHERE` statement controls **which records** to be shown based on conditions given.

#### `CASE`

`CASE` is used within the `SELECT` statement, with a similar syntax to switches:

```sql
CASE field
    WHEN ... THEN ...
    WHEN ... THEN ...
    ...
    ELSE ... -- optional
END
```

`CASE` statement controls **how fields** are to be shown in different conditions.

Say if I want to promote salaries of all managers and salesmen by 1.1 and 1.5 respectively,
then I would display 1.1x `SAL` for all managers, and 1.5x for all salesmen:

```sql
SELECT
    `ENAME` AS 'NAME',
    `JOB` AS 'JOB',
    `SAL` AS 'ORIGINAL SAL',
    CASE `JOB`
        WHEN 'MANAGER' THEN SAL * 1.1
        WHEN 'SALESMAN' THEN SAL * 1.5
        ELSE SAL -- not including this leads to null in some cells
    END AS 'PROMOTED SAL'
FROM
    `EMP`;
```

    +--------+-----------+--------------+--------------+
    | NAME   | JOB       | ORIGINAL SAL | PROMOTED SAL |
    +--------+-----------+--------------+--------------+
    | SMITH  | CLERK     |  800.0       |  800.0       |
    | ALLEN  | SALESMAN  | 1600.0       | 2400.0       |
    | WARD   | SALESMAN  | 1250.0       | 1875.0       |
    | JONES  | MANAGER   | 2975.0       | 3272.5       |
    | MARTIN | SALESMAN  | 1250.0       | 1875.0       |
    | BLAKE  | MANAGER   | 2850.0       | 3135.0       |
    | CLARK  | MANAGER   | 2450.0       | 2695.0       |
    | SCOTT  | ANALYST   | 3000.0       | 3000.0       |
    | KING   | PRESIDENT | 5000.0       | 5000.0       |
    | TURNER | SALESMAN  | 1500.0       | 2250.0       |
    | ADAMS  | CLERK     | 1100.0       | 1100.0       |
    | JAMES  | CLERK     |  950.0       |  950.0       |
    | FORD   | ANALYST   | 3000.0       | 3000.0       |
    | MILLER | CLERK     | 1300.0       | 1300.0       |
    +--------+-----------+--------------+--------------+

As a DQL, `CASE` does not modify the original data; it only changes query output.

#### `HAVING`

`HAVING` clause permits use of aggregate functions in conditions as it
is executed later than `GROUP BY` clause. This means use of `HAVING` must
used be together with `GROUP BY`.

`WHERE` clause has higher efficiency than `HAVING`;
therefore use `HAVING` only if it is the only option.

### Permitted conditions

#### Arithmetic

| symbol | use |
|:--|--|
| `=` (not `==`) | equals |
| `<>` or `!=` | not equals |
| `<` | smaller |
| `>` | larger |
| `<=` | seq |
| `>=` | leq |
| `BETWEEN AND` | range (closed) |

Example:

```sql
SELECT `EMPNO`, `ENAME`, `SAL` FROM `EMP` WHERE `SAL` >= 3000;
```

would be returning

    +-------+-------+--------+
    | EMPNO | ENAME | SAL    |
    +-------+-------+--------+
    | 7788  | SCOTT | 3000.0 |
    | 7839  | KING  | 5000.0 |
    | 7902  | FORD  | 3000.0 |
    +-------+-------+--------+

#### Logic

MySQL supports logic operators `AND`, `OR`, `NOT` and `IN`.

`AND` is prior than `OR`; brackets may be applied.

`IN` determines if one of the options is in match, similar to use of multiple `OR`;
can be negated with `NOT`.

Be aware that `IN` does not represent a range when using with numbers, such in:

```sql
SAL IN (800 , 5000) -- eqiuvalent to SAL = 800 OR SAL = 5000
```

Example:

```sql
SELECT
    `EMPNO` AS 'NO',
    `ENAME` AS 'NAME',
    `JOB`,
    `SAL`
FROM
    `EMP`
WHERE
    (`JOB` IN ('MANAGER' , 'PRESIDENT')) AND `SAL` >= 2500;
```

Results in

    +------+-------+-----------+--------+
    | NO   | NAME  | JOB       | SAL    |
    +------+-------+-----------+--------+
    | 7566 | JONES | MANAGER   | 2975.0 |
    | 7698 | BLAKE | MANAGER   | 2850.0 |
    | 7839 | KING  | PRESIDENT | 5000.0 |
    +------+-------+-----------+--------+

#### String

| operator | use |
|:--|--|
| `=` | case-insensitive equals |
| `!=` | case-insensitive not equals |
| `LIKE` | case-insensitive matches pattern |
| `BINARY` | use case-sensitive |

The pattern used for string match may include `%` or `_` for fuzzy matching:

| symbol | matches |
|--|--|
| `%` | any number of characters |
| `_` | any single character |

Use backslash for escaping characters: `\%`, `\_`

To find names whose second letter is 'O':

```sql
SELECT
    `ENAME` AS 'NAME'
FROM
    `EMP`
WHERE
    `ENAME` LIKE '_O%';
```

    +-------+
    | NAME  |
    +-------+
    | JONES |
    | FORD  |
    +-------+

We do not care about case-sensitivity here; to do so, put a `BINARY` in front of `` `ENAME` ``:

```sql
BINARY `ENAME` LIKE '%O%';
```

#### `NULL`

`NULL` is a special case; `=` cannot be used on `NULL` as no value will be provided to compare.
Use `IS NULL` instead.
