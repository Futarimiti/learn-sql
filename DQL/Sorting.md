# Sorting

A query may be in a certain order by sorting one or more of its fields in order.

### Use

Use `ORDER BY` statement to do so:

```sql
SELECT
	`ENAME` AS 'NAME',
	`SAL`
FROM
	`EMP`
ORDER BY
	`SAL`;
```

Unordered and ordered queries:

    +--------+--------+ 		+--------+--------+
    | NAME   | SAL    | 		| NAME   | SAL    |
    +--------+--------+ 		+--------+--------+
    | SMITH  |  800.0 | 		| SMITH  |  800.0 |
    | ALLEN  | 1600.0 | 		| JAMES  |  950.0 |
    | WARD   | 1250.0 | 		| ADAMS  | 1100.0 |
    | JONES  | 2975.0 | 		| WARD   | 1250.0 |
    | MARTIN | 1250.0 | 		| MARTIN | 1250.0 |
    | BLAKE  | 2850.0 | 		| MILLER | 1300.0 |
    | CLARK  | 2450.0 | 		| TURNER | 1500.0 |
    | SCOTT  | 3000.0 | 		| ALLEN  | 1600.0 |
    | KING   | 5000.0 | 		| CLARK  | 2450.0 |
    | TURNER | 1500.0 | 		| BLAKE  | 2850.0 |
    | ADAMS  | 1100.0 | 		| JONES  | 2975.0 |
    | JAMES  |  950.0 | 		| SCOTT  | 3000.0 |
    | FORD   | 3000.0 | 		| FORD   | 3000.0 |
    | MILLER | 1300.0 | 		| KING   | 5000.0 |
    +--------+--------+ 		+--------+--------+

The order is ascent by default; the option, `ASC` and `DESC`, can be explicitly stated:

```sql
ORDER BY `SAL` DESC;
```

Do note that `DESC` may also be used as a shorthand form of `DESCRIBE`.

Sorting may be applied to multiple fields, where if the primary field tied then the secondary rule will be applied.

Example: sort by `SAL` first, and then `NAME` in alphabetic order in case of tie.

To do so, use comma to separate two sorting rules:

```sql
ORDER BY `SAL` ASC, `ENAME` ASC; -- SAL: primary; ENAME: secondary
```

The order can also be made using colunm # (1-based) of the queried table.

In this case where the second colunm is `SAL`, `ORDER BY 2` would be having the same effect as ``ORDER BY `SAL` ``.

However, this non-robust syntax should not be formally used as order of columns is not fixed.
