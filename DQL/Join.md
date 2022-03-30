# Join query

Join queries are queries across multiple tables.

For example, a join query can be made combining `ENAME` from table `EMP` and `DNAME` from `DEPT`
based on which department the employee is in.

### Types of join

Here are the different types of the JOINs in SQL:

    .
    ├── Inner join
    │   ├── Equal field join
    │   ├── Non-equal field join
    │   └── Self join
    ├── Outer join
    │   ├── Left outer join
    │   └── Right outer join
    └── Full join

| Type | Return |
| -- | -- |
| (INNER) JOIN | records that have matching values in both tables |
| LEFT (OUTER) JOIN | all records from the left table, and the matched records from the right table |
| RIGHT (OUTER) JOIN | all records from the right table, and the matched records from the left table |
| FULL (OUTER) JOIN | all records when there is a match in either left or right table |

See [this link](https://www.w3schools.com/sql/sql_join.asp) for visualised explanation.

### Syntax

#### Two tables

##### Inner join

###### Equal field join

Back on the combinig query made at the beginning
where we want to combine `ENAME` and `DNAME` based on which department the employee is in.

The department check can be done with `DEPTNO`, which appears in both `EMP` and `DEPT`.
Therefore, we will deliver such query: "we will combine `ENAME` and `DNAME` **on** equal `DEPTNO`".

There are two ways of writing this query with two standards, SQL92 and SQL99:

SQL92:

```sql
SELECT
	`ENAME` AS `EMPLOYEE`,
	`DNAME` AS `DEPARTMENT`
FROM
	`EMP`,
	`DEPT`
WHERE
	`EMP`.`DEPTNO` = `DEPT`.`DEPTNO`;
```

SQL99:

```sql
SELECT
	`ENAME` AS `EMPLOYEE`,
	`DNAME` AS `DEPARTMENT`
FROM
	`EMP`
INNER JOIN -- INNER may be omitted
	`DEPT`
ON
	`EMP`.`DEPTNO` = `DEPT`.`DEPTNO`;
```

    +----------+------------+
    | EMPLOYEE | DEPARTMENT |
    +----------+------------+
    | SMITH    | RESEARCH   |
    | ALLEN    | SALES      |
    | WARD     | SALES      |
    | JONES    | RESEARCH   |
    | MARTIN   | SALES      |
    | BLAKE    | SALES      |
    | CLARK    | ACCOUNTING |
    | SCOTT    | RESEARCH   |
    | KING     | ACCOUNTING |
    | TURNER   | SALES      |
    | ADAMS    | RESEARCH   |
    | JAMES    | SALES      |
    | FORD     | RESEARCH   |
    | MILLER   | ACCOUNTING |
    +----------+------------+

Though both queries return the same result, the SQL99 syntax is strongly recommended for being more structured
by using a separate joining clause, instead of lumping everything in the `WHERE` clause which is only supposed
to filter records based on its data.

Fields from different tables may take the same name. Add table name, or table alias if necessary:

```sql
SELECT
	e.`ENAME` AS `EMPLOYEE`,
	d.`DNAME` AS `DEPARTMENT`
FROM
	`EMP` e
INNER JOIN 
	`DEPT` d
ON
	e.`DEPTNO` = d.DEPTNO`;
```

Specifying table name may speed up query.

###### Non-equal field join

We may want to query the salary grade for each employee.

`EMP` provides `ENAME` and `SAL` for each employee,
while `SALGRADE` provides `LOSAL` and `HISAL` for each `GRADE`.

Therefore, to determine the salary grade of a employee, check which salary range they are in.

```sql
SELECT
	`EMP`.`ENAME`       AS 'NAME',
    `EMP`.`SAL`         AS 'SALARY',
    `SALGRADE`.`GRADE`  AS 'GRADE'
FROM
	`EMP`
INNER JOIN
    `SALGRADE`
ON
    `EMP`.`SAL` BETWEEN `SALGRADE`.`LOSAL` AND `SALGRADE`.`HISAL`
ORDER BY
    `SALGRADE`.`GRADE` ASC,
    `EMP`.`SAL` ASC;
```

    +--------+--------+-------+
    | NAME   | SALARY | GRADE |
    +--------+--------+-------+
    | SMITH  |  800.0 | 1     |
    | JAMES  |  950.0 | 1     |
    | ADAMS  | 1100.0 | 1     |
    | WARD   | 1250.0 | 2     |
    | MARTIN | 1250.0 | 2     |
    | MILLER | 1300.0 | 2     |
    | TURNER | 1500.0 | 3     |
    | ALLEN  | 1600.0 | 3     |
    | CLARK  | 2450.0 | 4     |
    | BLAKE  | 2850.0 | 4     |
    | JONES  | 2975.0 | 4     |
    | SCOTT  | 3000.0 | 4     |
    | FORD   | 3000.0 | 4     |
    | KING   | 5000.0 | 5     |
    +--------+--------+-------+

This query is a non-equal field query as it does not match records based on equal fields.

###### Self connect

Except KING, every employee has a manager.
`EMP` gives no. of each manager, and what if we'd like to know their name?

Here we perform a self connect where we use `MGR` back on the table `EMP` itself.

The syntax may be easier to understand if you think there are two tables of `EMP`, `a` and `b`,
and you link one to another:

```sql
SELECT
    a.`ENAME` AS 'NAME',
    b.`ENAME` AS 'MANAGER'
FROM 
    `EMP` a
INNER JOIN
    `EMP` b
ON
    a.`MGR` = b.`EMPNO`;
```

    +--------+---------+
    | NAME   | MANAGER |
    +--------+---------+
    | SMITH  | FORD    |
    | ALLEN  | BLAKE   |
    | WARD   | BLAKE   |
    | JONES  | KING    |
    | MARTIN | BLAKE   |
    | BLAKE  | KING    |
    | CLARK  | KING    |
    | SCOTT  | JONES   |
    | TURNER | BLAKE   |
    | ADAMS  | SCOTT   |
    | JAMES  | BLAKE   |
    | FORD   | JONES   |
    | MILLER | CLARK   |
    +--------+---------+

You may have found that "KING" has disappeared from the query result.
This is because `MGR` of KING, `NULL`, matches nothing.

##### Outer join

While inner join only selects records satisfying the requirement,
an outer join may select to more than that.

If we inner join departments and employees within:

```sql
SELECT
    `DEPT`.`DNAME` AS 'DEPARTMENT',
    `EMP`.`ENAME` AS 'EMPLOYEE'
FROM
    `DEPT`
INNER JOIN
    `EMP`
ON
    `DEPT`.`DEPTNO` = `EMP`.`DEPTNO`
ORDER BY
    DEPARTMENT ASC,
    EMPLOYEE ASC;
```

    +------------+----------+
    | DEPARTMENT | EMPLOYEE |
    +------------+----------+
    | ACCOUNTING | CLARK    |
    | ACCOUNTING | KING     |
    | ACCOUNTING | MILLER   |
    | RESEARCH   | ADAMS    |
    | RESEARCH   | FORD     |
    | RESEARCH   | JONES    |
    | RESEARCH   | SCOTT    |
    | RESEARCH   | SMITH    |
    | SALES      | ALLEN    |
    | SALES      | BLAKE    |
    | SALES      | JAMES    |
    | SALES      | MARTIN   |
    | SALES      | TURNER   |
    | SALES      | WARD     |
    +------------+----------+

Nobody works under department "OPERATIONS";
this dissatisfies the requirement of the `ON` clause, and "OPERATIONS" is hence excluded from the result table.

There may be requirement to show all departments, including those without employees;
this is where outer queries come to usefulness.

```sql
SELECT
    `DEPT`.`DNAME` AS 'DEPARTMENT',
    `EMP`.`ENAME` AS 'EMPLOYEE'
FROM
    `DEPT`
LEFT OUTER JOIN -- OUTER may be omitted, but compromising readability
    `EMP`
ON
    `DEPT`.`DEPTNO` = `EMP`.`DEPTNO`
ORDER BY
    DEPARTMENT ASC,
    EMPLOYEE ASC;
```

    +------------+----------+
    | DEPARTMENT | EMPLOYEE |
    +------------+----------+
    | ACCOUNTING | CLARK    |
    | ACCOUNTING | KING     |
    | ACCOUNTING | MILLER   |
    | OPERATIONS | <null>   |
    | RESEARCH   | ADAMS    |
    | RESEARCH   | FORD     |
    | RESEARCH   | JONES    |
    | RESEARCH   | SCOTT    |
    | RESEARCH   | SMITH    |
    | SALES      | ALLEN    |
    | SALES      | BLAKE    |
    | SALES      | JAMES    |
    | SALES      | MARTIN   |
    | SALES      | TURNER   |
    | SALES      | WARD     |
    +------------+----------+

We used an left join here, where all records on the LEFT table
are included in the query result, no matter matching the joining requirement
or not;
that is, consider the LEFT table as the main table.
There is no such main table in inner join.

And because of this, there are ALWAYS more records in the outer join than the inner join.

Left and right joins are interchangeable on writing.
Here's an equivalent query of right join:

```sql
SELECT
    `DEPT`.`DNAME` AS 'DEPARTMENT',
    `EMP`.`ENAME` AS 'EMPLOYEE'
FROM
    `EMP`
RIGHT OUTER JOIN
    `DEPT`
ON
    `DEPT`.`DEPTNO` = `EMP`.`DEPTNO`
ORDER BY
    DEPARTMENT ASC,
    EMPLOYEE ASC;
```

If we redo the employees and their manager query but this includes ones with no higher managers,
then we can use outer join as the following form:

```sql
SELECT
    a.`ENAME` AS 'NAME',
    b.`ENAME` AS 'MANAGER'
FROM 
    `EMP` a
LEFT OUTER JOIN
    `EMP` b
ON
    a.`MGR` = b.`EMPNO`;
```

    +--------+---------+
    | NAME   | MANAGER |
    +--------+---------+
    | SMITH  | FORD    |
    | ALLEN  | BLAKE   |
    | WARD   | BLAKE   |
    | JONES  | KING    |
    | MARTIN | BLAKE   |
    | BLAKE  | KING    |
    | CLARK  | KING    |
    | SCOTT  | JONES   |
    | KING   | <null>  |
    | TURNER | BLAKE   |
    | ADAMS  | SCOTT   |
    | JAMES  | BLAKE   |
    | FORD   | JONES   |
    | MILLER | CLARK   |
    +--------+---------+

Where KING is now included, with `MANAGER` as `NULL`.

###### Full join

Full join refers to when both tables are main tables.

#### Multiple tables

In SQL99, joining multiple tables requires use of multiple `JOIN` clauses:

```sql
-- join table a, b, c...:
SELECT
    ...
FROM
    `a`
INNER JOIN
    `b`
ON
    conditions for `a` and `b`...
RIGHT OUTER JOIN
    `c`
ON
    conditions for `a` and `c`...
LEFT OUTER JOIN
    ...
```

Note that mixed joins are allowed.

For example, if we'd like to know department name and salary grade for each employee at the same time:

```sql
SELECT
    `EMP`.`ENAME`       AS  'EMPLOYEE',
    `EMP`.`SAL`         AS  'SALARY',
    `SALGRADE`.`GRADE`  AS  'SALARY GRADE',
    `EMP`.`DEPTNO`      AS  'DEPT NO',
    `DEPT`.`DNAME`      AS  'DEPARTMENT'
FROM
    `EMP`
INNER JOIN
    `SALGRADE`
ON
    `EMP`.`SAL` BETWEEN `SALGRADE`.`LOSAL` AND `SALGRADE`.`HISAL`
INNER JOIN
    `DEPT`
ON
    `DEPT`.`DEPTNO` = `EMP`.`DEPTNO`;
```

    +----------+--------+--------------+---------+------------+
    | EMPLOYEE | SALARY | SALARY GRADE | DEPT NO | DEPARTMENT |
    +----------+--------+--------------+---------+------------+
    | JAMES    |  950.0 | 1            | 30      | SALES      |
    | ADAMS    | 1100.0 | 1            | 20      | RESEARCH   |
    | SMITH    |  800.0 | 1            | 20      | RESEARCH   |
    | MILLER   | 1300.0 | 2            | 10      | ACCOUNTING |
    | MARTIN   | 1250.0 | 2            | 30      | SALES      |
    | WARD     | 1250.0 | 2            | 30      | SALES      |
    | TURNER   | 1500.0 | 3            | 30      | SALES      |
    | ALLEN    | 1600.0 | 3            | 30      | SALES      |
    | FORD     | 3000.0 | 4            | 20      | RESEARCH   |
    | SCOTT    | 3000.0 | 4            | 20      | RESEARCH   |
    | CLARK    | 2450.0 | 4            | 10      | ACCOUNTING |
    | BLAKE    | 2850.0 | 4            | 30      | SALES      |
    | JONES    | 2975.0 | 4            | 20      | RESEARCH   |
    | KING     | 5000.0 | 5            | 10      | ACCOUNTING |
    +----------+--------+--------------+---------+------------+

### Decrease joining frequency

Linking too many tables together may reduce the query effeciency.
During the equal-field query, almost all records need to go through once
while it is normal for a table to contain tens of thousands of records.

Therefore as a efficiency matter, tables should be linked as minimum as possible.
