# Query by groups

Where data are classified into groups upon which queries are carried out later.

For example, you may want to find out average wage per job,
where you should first group records by their jobs,
and then apply `AVG` on each group's `SAL`.

### `GROUP BY`

A `GROUP BY` clause states which field(s) to be taken for grouping.

To group records by their jobs, we could write:

```sql
SELECT
    `JOB`,
    AVG(`SAL`) AS 'AVG SAL'
FROM
    `EMP`
GROUP BY
    `JOB`;
```

    +-----------+-------------+
    | JOB       | AVG SAL     |
    +-----------+-------------+
    | CLERK     | 1037.5      |
    | SALESMAN  | 1400.0      |
    | MANAGER   | 2758.333333 |
    | ANALYST   | 3000.0      |
    | PRESIDENT | 5000.0      |
    +-----------+-------------+

You may have noticed that only aggregate functions and grouped fields
are allowed in the `SELECT` clause;
it would be meaningless to add any other fields.

It is possible to group by multiple fields. If we want to find highest
wage in different jobs in different departments, then we'll be grouping
with 2 fields at the same time:

```sql
SELECT
    `DEPTNO`,
    `JOB`,
    MAX(`SAL`) AS 'MAX SAL'
FROM
    `EMP`
GROUP BY
    `DEPTNO` , `JOB`
ORDER BY
    `DEPTNO` , `JOB`;
```

    +--------+-----------+---------+
    | DEPTNO | JOB       | MAX SAL |
    +--------+-----------+---------+
    | 10     | CLERK     | 1300.0  |
    | 10     | MANAGER   | 2450.0  |
    | 10     | PRESIDENT | 5000.0  |
    | 20     | ANALYST   | 3000.0  |
    | 20     | CLERK     | 1100.0  |
    | 20     | MANAGER   | 2975.0  |
    | 30     | CLERK     |  950.0  |
    | 30     | MANAGER   | 2850.0  |
    | 30     | SALESMAN  | 1600.0  |
    +--------+-----------+---------+

Where we can think of 2 fields here union into a single field,
such we have "`10 CLERK`", "`20 ANALYST`", "`30 SALESMAN`", etc. as
different groups; order of two fields does not matter.

### `HAVING`

`HAVING` clause provides a condintion check after execution of `GROUP BY`
where aggregate function is applicable.

It must be used together with `GROUP BY` clause.
