# Exercises

### Filter employees with maximum salaries for each department

Include `ENAME`, `SAL` and `DEPTNO`.

```sql
SELECT
	`EMP`.`ENAME`,
	temp.`SAL`,
	temp.`DEPTNO`
FROM
	`EMP`
INNER JOIN
(
	SELECT
		MAX(`SAL`) AS 'SAL',
		`DEPTNO`
	FROM
		`EMP`
	GROUP BY
		`DEPTNO`
) temp
ON
	temp.`SAL` = `EMP`.`SAL`;
```

    +-------+--------+--------+
    | ENAME | SAL    | DEPTNO |
    +-------+--------+--------+
    | BLAKE | 2850.0 | 30     |
    | SCOTT | 3000.0 | 20     |
    | KING  | 5000.0 | 10     |
    | FORD  | 3000.0 | 20     |
    +-------+--------+--------+

### Filter employees with salary higher than the average in their department

Include `ENAME` and `SAL`.

```sql
SELECT
	`EMP`.`ENAME`,
	`EMP`.`SAL`
FROM
	`EMP`
INNER JOIN
(
	SELECT
		`EMP`.`DEPTNO`,
		AVG(`EMP`.`SAL`) AS 'AVG DEPT SAL'
	FROM
		`EMP`
	GROUP BY
		`EMP`.`DEPTNO`
) temp
ON
	`EMP`.`DEPTNO` = temp.`DEPTNO` AND `EMP`.`SAL` > temp.`AVG DEPT SAL`;
```

    +-------+--------+
    | ENAME | SAL    |
    +-------+--------+
    | ALLEN | 1600.0 |
    | JONES | 2975.0 |
    | BLAKE | 2850.0 |
    | SCOTT | 3000.0 |
    | KING  | 5000.0 |
    | FORD  | 3000.0 |
    +-------+--------+

### Get average salary grade in each department

Include `DEPTNO` and `AVG(GRADE)`.

```sql
SELECT
	`EMP`.`DEPTNO`,
	AVG(temp.`GRADE`) AS 'AVG(GRADE)'
FROM
	`EMP`
INNER JOIN
(
	SELECT
		`EMP`.`DEPTNO`,
		`SALGRADE`.`GRADE`
	FROM
		`EMP`
	INNER JOIN
		`SALGRADE`
	ON
		`EMP`.`SAL` BETWEEN `SALGRADE`.`LOSAL` AND `SALGRADE`.`HISAL`
) temp
ON
	`EMP`.`DEPTNO` = temp.`DEPTNO`
GROUP BY
	`EMP`.`DEPTNO`;
```

    +--------+------------+
    | DEPTNO | AVG(GRADE) |
    +--------+------------+
    | 20     | 2.8000     |
    | 30     | 2.5000     |
    | 10     | 3.6667     |
    +--------+------------+

### Find maximum salary without using aggregate function `MAX`

Include `SAL`. Give two solutions.

Solution 1: order `SAL` by desc, limit 1

```sql
SELECT
	`EMP`.`SAL`
FROM
	`EMP`
ORDER BY
	`EMP`.`SAL` DESC
LIMIT
	0, 1;
```

Solution 2: self join

```sql
SELECT
	`EMP`.`SAL` AS 'SAL'
FROM
	`EMP`
WHERE
	`EMP`.`SAL` NOT IN
	( 	-- listing salaries that are not the highest
		SELECT DISTINCT
			a.`SAL`
		FROM
			`EMP` a
		INNER JOIN
			`EMP` b
		ON
			a.`SAL` < b.`SAL`
	);
```

    +--------+
    | SAL    |
    +--------+
    | 5000.0 |
    +--------+

### Find `DEPTNO` of the department with the highest average salary

Include `DEPTNO`. Use at least two different solutions.

Solution 1: `LIMIT`

```sql
SELECT
	`DEPTNO`
FROM
(
	SELECT
		`EMP`.`DEPTNO`,
		AVG(`EMP`.`SAL`)
	FROM
		`EMP`
	GROUP BY
		`EMP`.`DEPTNO`
	ORDER BY
		AVG(`EMP`.`SAL`) DESC
	LIMIT
		0, 1
) AS _;
```

Solution 2: `MAX`

```sql
-- find DEPTNO and corres AVG SAL
SELECT
	`EMP`.`DEPTNO`,
	AVG(`EMP`.`SAL`) AS 'AVG SAL'
FROM
	`EMP`
GROUP BY
	`EMP`.`DEPTNO`

-- find the department with highst AVG SAL
SELECT
	MAX(t.`AVG SAL`) AS 'HIGHEST SAL'
FROM
(
	SELECT
		`EMP`.`DEPTNO`,
		AVG(`EMP`.`SAL`) AS 'AVG SAL'
	FROM
		`EMP`
	GROUP BY
		`EMP`.`DEPTNO`
) AS t

-- find the department with this HIGHEST SAL
SELECT
	t.`DEPTNO`
FROM
(
	SELECT
		`EMP`.`DEPTNO`,
		AVG(`EMP`.`SAL`) AS 'AVG SAL'
	FROM
		`EMP`
	GROUP BY
		`EMP`.`DEPTNO`
) AS t
WHERE
	t.`AVG SAL` = (
		SELECT
			MAX(t.`AVG SAL`) AS 'HIGHEST SAL'
		FROM
		(
			SELECT
				`EMP`.`DEPTNO`,
				AVG(`EMP`.`SAL`) AS 'AVG SAL'
			FROM
				`EMP`
			GROUP BY
				`EMP`.`DEPTNO`
		) AS t
	);
```

    +--------+
    | DEPTNO |
    +--------+
    | 10     |
    +--------+

### Find `DNAME` for the department with highest average salary

Include `DNAME`.

```sql
SELECT
	`DNAME`
FROM
(
	SELECT
		T.`DEPTNO`,
		`DEPT`.`DNAME`
	FROM
	(
		SELECT
			AVG(`SAL`) AS 'AVG SAL',
			`DEPTNO`
		FROM
			`EMP`
		GROUP BY
			`DEPTNO`
		ORDER BY
			`AVG SAL` DESC
		LIMIT
			1
	) AS T
	INNER JOIN
		`DEPT`
	ON
		T.`DEPTNO` = `DEPT`.`DEPTNO`
) AS _;
```

    +------------+
    | DNAME      |
    +------------+
    | ACCOUNTING |
    +------------+

### Find `DNAME` for the department with lowest average salary

Include `DNAME`.

```sql
SELECT
	`DEPT`.`DNAME`
FROM
	`DEPT`
INNER JOIN
(
	SELECT
		`DEPTNO`,
		AVG(`SAL`) AS 'AVG SAL'
	FROM
		`EMP`
	GROUP BY
		`DEPTNO`
	ORDER BY
		`AVG SAL` ASC
	LIMIT
		1
) AS T
ON
	T.`DEPTNO` = `DEPT`.`DEPTNO`;
```

    +-------+
    | DNAME |
    +-------+
    | SALES |
    +-------+

### Find managers whose salary is higher than the highest salary amongst employees who are not managers

Include `ENAME` and `SAL` for each manager.

```sql
-- find employees that are not managers
-- O(n^2)
-- NOT IN subquery must not contain NULL; see https://stackoverflow.com/questions/129077/null-values-inside-not-in-clause
SELECT
	*
FROM
	`EMP`
WHERE
	`EMPNO` NOT IN (SELECT IFNULL(`MGR`, '') FROM `EMP`)

-- find the highest sal amongst them `HISAL OF NON-MANAGERS`
-- used `MAX` here, feel free to change to `LIMIT`
-- O(n)
SELECT
	MAX(T.`SAL`) AS 'HISAL OF NON-MANAGERS'
FROM
(
	SELECT
		*
	FROM
		`EMP`
	WHERE
		`EMPNO` NOT IN (SELECT IFNULL(`MGR`, '') FROM `EMP`)
) AS T

-- find employees that are managers
-- O(n^2)

SELECT
	*
FROM
	`EMP`
WHERE
	`EMPNO` IN (SELECT `MGR` FROM `EMP`)

-- select managers with higher salary than `HISAL OF NON-MANAGERS`
-- overall: O(n^2)

SELECT
	`ENAME`,
	`SAl`
FROM
(
	SELECT
		*
	FROM
		`EMP`
	WHERE
		`EMPNO` IN (SELECT `MGR` FROM `EMP`)
) AS T_MANAGERS
WHERE 
	`SAL` > 
	(
		SELECT
			MAX(T.`SAL`)
		FROM
		(
			SELECT
				*
			FROM
				`EMP`
			WHERE
				`EMPNO` NOT IN (SELECT IFNULL(`MGR`, '') FROM `EMP`)
		) AS T
	);
```

    +-------+--------+
    | ENAME | SAL    |
    +-------+--------+
    | JONES | 2975.0 |
    | BLAKE | 2850.0 |
    | CLARK | 2450.0 |
    | SCOTT | 3000.0 |
    | KING  | 5000.0 |
    | FORD  | 3000.0 |
    +-------+--------+
