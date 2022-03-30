# Duplicate elimination

If you would like to view values of a field
regardless of its frequency, put `DISTINCT` after `SELECT`
to eliminate duplicates from the query result:

```sql
SELECT DISTINCT
    `JOB`
FROM
    `EMP`;
```

    +-----------+
    | JOB       |
    +-----------+
    | CLERK     |
    | SALESMAN  |
    | MANAGER   |
    | ANALYST   |
    | PRESIDENT |
    +-----------+

`DISTINCT` can be used for multiple fields, where
the fields combine to become a new field:

```sql
SELECT DISTINCT
    `DEPTNO` , `JOB`
FROM
    `EMP`
ORDER BY
    `DEPTNO` ASC,
    `JOB` ASC;
```

    +--------+-----------+
    | DEPTNO | JOB       |
    +--------+-----------+
    | 10     | CLERK     |
    | 10     | MANAGER   |
    | 10     | PRESIDENT |
    | 20     | ANALYST   |
    | 20     | CLERK     |
    | 20     | MANAGER   |
    | 30     | CLERK     |
    | 30     | MANAGER   |
    | 30     | SALESMAN  |
    +--------+-----------+

`DISTINCT` cannot be used such as in ``SELECT `DEPTNO` , DISTINCT `JOB` ``;
this is considered as a syntax mistake.

The following syntax is possible though:

```sql
SELECT
    COUNT(DISTINCT `JOB`) AS '# OF JOBS'
FROM
    EMP;
```

    +-----------+
    | # OF JOBS |
    +-----------+
    | 5         |
    +-----------+
