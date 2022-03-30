# Aggregate functions

An aggregate function takes multiple features of a field and returns one output.

Also known as group function as data should be grouped first before use.
If not, the whole table is treated as a group.

Aggregate functions ignore `NULL` features automatically.

Aggregate functions cannot be directly used within a `WHERE` clause
due to the order of evaluation of clauses.
It can be used within either `SELECT` or `HAVING` clause.

### Built-in aggregate functions

Built-in aggregate functions mainly do number manipulations.

| function | usage |
|-|-|
| `MAX` | find maximum |
| `MIN` | find minimum |
| `SUM` | sum up |
| `AVG` | find average |
| `COUNT` | get # of non-null features on this field |
| `STD`<br>`STDDEV`<br>`STDEV_POP` | find population standard deviation |
| `STDDEV_SAMP` | find sample standard deviation |
| `VAR_POP`<br>`VARIANCE` | find population standard variance |
| `VAR_SAMP` | find sample standard variance |

```sql
SELECT
	MAX(`SAL`) AS 'MAX SAL',
    MIN(`SAL`) AS 'MIN SAL',
    AVG(`SAL`) AS 'AVG SAL',
    STD(`SAL`) AS 'STD'
FROM
    `EMP`;
```

    +---------+---------+-------------+--------------------+
    | MAX SAL | MIN SAL | AVG SAL     | STD                |
    +---------+---------+-------------+--------------------+
    | 5000.0  | 800.0   | 2073.214286 | 1139.4886182952814 |
    +---------+---------+-------------+--------------------+

Again, `NULL` features are automatically ignored:

```sql
SELECT
    MAX(`COMM`) AS 'MAX COMM',
    MIN(`COMM`) AS 'MIN COMM',
    AVG(`COMM`) AS 'AVG COMM',
    STD(`COMM`) AS 'STD'
FROM
    `EMP`;
```

    +----------+----------+----------+-------------------+
    | MAX COMM | MIN COMM | AVG COMM | STD               |
    +----------+----------+----------+-------------------+
    | 1400.0   | 0.0      | 550.0    | 522.0153254455275 |
    +----------+----------+----------+-------------------+

Non-zero difference between `COUNT(*)` and `COUNT(field)` implies presence
of `NULL` in one or more of its features:

```sql
SELECT
    COUNT(*) - COUNT(`COMM`) AS 'DIFF'
FROM
    `EMP`;
```

    +------+
    | DIFF |
    +------+
    | 10   |
    +------+

### In conditional sense

`WHERE` is prior to `GROUP BY` in executing order, therefore
it is invalid to use aggregate functions directly in `WHERE` clause
(ERROR 1111), upon which grouping is not done yet.

`HAVING` clause are open to aggregate functions for conditional check on group.

This could solve most of the problems, but surprisingly fails on some basic conditions.

An example is selecting records whose `SAL` is higher than average,
where it is impossible to find the grouping field.

For this particular case, we can nest a subquery to find the average
first, and then apply to `WHERE`:

```sql
SELECT
    `ENAME` AS 'NAME',
    `SAL`
FROM
    `EMP`
WHERE
    `SAL` > (SELECT AVG(`SAL`) FROM `EMP`);
```

    +-------+--------+
    | NAME  | SAL    |
    +-------+--------+
    | JONES | 2975.0 |
    | BLAKE | 2850.0 |
    | CLARK | 2450.0 |
    | SCOTT | 3000.0 |
    | KING  | 5000.0 |
    | FORD  | 3000.0 |
    +-------+--------+
