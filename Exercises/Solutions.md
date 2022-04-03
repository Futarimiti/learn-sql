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

### Find top five employees with highest salaries

Include `ENAME` and `SAL`, in descending order of `SAL`.

```sql
SELECT
	`ENAME`,
	`SAL`
FROM
	`EMP`
ORDER BY
	`SAL` DESC
LIMIT
	0, 5;
```

    +-------+--------+
    | ENAME | SAL    |
    +-------+--------+
    | KING  | 5000.0 |
    | SCOTT | 3000.0 |
    | FORD  | 3000.0 |
    | JONES | 2975.0 |
    | BLAKE | 2850.0 |
    +-------+--------+

### Find employees with top 6-10 salaries

Include `ENAME` and `SAL`, in descending order of `SAL`.

```sql
SELECT
	`ENAME`,
	`SAL`
FROM
	`EMP`
ORDER BY
	`SAL` DESC
LIMIT
	5, 5;
```

    +--------+--------+
    | ENAME  | SAL    |
    +--------+--------+
    | CLARK  | 2450.0 |
    | ALLEN  | 1600.0 |
    | TURNER | 1500.0 |
    | MILLER | 1300.0 |
    | WARD   | 1250.0 |
    +--------+--------+

### Find the last 5 employees hired

Include `ENAME` and `HIREDATE`, with `HIREDATE` becoming earlier;
use default format of date.

```sql
SELECT
	`ENAME`,
	`HIREDATE`
FROM
	`EMP`
ORDER BY
	`HIREDATE` DESC
LIMIT
	0, 5;
```

    +--------+------------+
    | ENAME  | HIREDATE   |
    +--------+------------+
    | ADAMS  | 1987-05-23 |
    | SCOTT  | 1987-04-19 |
    | MILLER | 1982-01-23 |
    | FORD   | 1981-12-03 |
    | JAMES  | 1981-12-03 |
    +--------+------------+

### Find number of employees for each salgrade

Include `GRADE` and `COUNT(*)`.

```sql
SELECT
	`GRADE`,
	COUNT(*)
FROM
(
	SELECT
		`ENAME`,
		`GRADE`
	FROM
		`EMP`
	INNER JOIN
		`SALGRADE`
	ON
		`EMP`.`SAL` BETWEEN `SALGRADE`.`LOSAL` AND `SALGRADE`.`HISAL`
) AS T
GROUP BY
	T.`GRADE`;
```

    +-------+----------+
    | GRADE | COUNT(*) |
    +-------+----------+
    | 1     | 3        |
    | 3     | 2        |
    | 2     | 3        |
    | 4     | 5        |
    | 5     | 1        |
    +-------+----------+

### Q13 skipped;

### Find name of manager for each employee

Include `ENAME` and `MNAME`. Use "no manager" for who do not have
a manager.

```sql
SELECT
	`EMP`.`ENAME`,
	IFNULL(M.`ENAME`, 'no manager') AS 'MNAME'
FROM
	`EMP`
LEFT OUTER JOIN
	`EMP` AS M
ON
	`EMP`.`MGR` = M.`EMPNO`;
```

    +--------+------------+
    | ENAME  | MNAME      |
    +--------+------------+
    | SMITH  | FORD       |
    | ALLEN  | BLAKE      |
    | WARD   | BLAKE      |
    | JONES  | KING       |
    | MARTIN | BLAKE      |
    | BLAKE  | KING       |
    | CLARK  | KING       |
    | SCOTT  | JONES      |
    | KING   | no manager |
    | TURNER | BLAKE      |
    | ADAMS  | SCOTT      |
    | JAMES  | BLAKE      |
    | FORD   | JONES      |
    | MILLER | CLARK      |
    +--------+------------+

### Find employees who has a manager and whose hiredate is earlier than their direct manager

Include `EMPNO`, `ENAME` and `DNAME`.

```sql
SELECT
	`EMPNO`,
	`ENAME`,
	`DNAME`
FROM
(
	SELECT
		`EMP`.`ENAME`,
		`EMP`.`HIREDATE` AS 'E HIREDATE',
		T_MANAGER.`HIREDATE` AS 'M HIREDATE',
		`EMP`.`EMPNO`,
		`DEPT`.`DNAME`
	FROM
		`EMP`
	INNER JOIN
		`EMP` AS T_MANAGER
	ON
		`EMP`.`MGR` = T_MANAGER.`EMPNO`
	INNER JOIN
		`DEPT`
	ON
		`EMP`.`DEPTNO` = `DEPT`.`DEPTNO`
) AS T
WHERE
	T.`E HIREDATE` < T.`M HIREDATE`;
```

    +-------+-------+------------+
    | EMPNO | ENAME | DNAME      |
    +-------+-------+------------+
    | 7369  | SMITH | RESEARCH   |
    | 7499  | ALLEN | SALES      |
    | 7521  | WARD  | SALES      |
    | 7566  | JONES | RESEARCH   |
    | 7698  | BLAKE | SALES      |
    | 7782  | CLARK | ACCOUNTING |
    +-------+-------+------------+

### List all employees in each department

Include `DNAME` and all information (`*`) of every employee.
Order table by `DNAME`.

```sql
SELECT
	`DEPT`.`DNAME`,
	`EMP`.*
FROM
	`EMP`
RIGHT OUTER JOIN
	`DEPT`
ON
	`EMP`.`DEPTNO` = `DEPT`.`DEPTNO`;
```

    Table is too long to copy; I'll paste to here later

### List departments with 5 or more employees

Include `DNAME` and `EMP COUNT`.

```sql
SELECT
	`DEPT`.`DNAME`,
	T.`COUNT(*)` AS 'EMP COUNT'
FROM
(
	SELECT
		`DEPTNO`,
		COUNT(*)
	FROM
		`EMP`
	GROUP BY
		`EMP`.`DEPTNO`
	HAVING
		COUNT(*) >= 5
) AS T
INNER JOIN
	`DEPT`
ON
	`DEPT`.`DEPTNO` = T.`DEPTNO`;
```

    +----------+-----------+
    | DNAME    | EMP COUNT |
    +----------+-----------+
    | RESEARCH | 5         |
    | SALES    | 6         |
    +----------+-----------+

### List all employees with higher salary than Smith

Include all information (`*`) for each employee.

```sql
SELECT
	*
FROM
	`EMP`
WHERE 
	`SAL` > (SELECT `SAL` FROM `EMP` WHERE `ENAME` = 'SMITH')
```

    Table too long

### Find all clerks

Include `ENAME`, `DNAME` for each clerk, and `EMP COUNT` for their department.

```sql
SELECT
	`EMP`.`ENAME`,
	`DEPT`.`DNAME`,
	T.`EMP COUNT`
FROM
	`EMP`
INNER JOIN
	`DEPT`
ON
	`DEPT`.`DEPTNO` = `EMP`.`DEPTNO`
INNER JOIN
(
	SELECT
		`DEPT`.`DNAME`,
		COUNT(*) AS 'EMP COUNT'
	FROM
		`EMP`
	INNER JOIN
		`DEPT`
	ON
		`EMP`.`DEPTNO` = `DEPT`.`DEPTNO`
	GROUP BY
		`EMP`.`DEPTNO`
) AS T
ON
	T.`DNAME` = `DEPT`.`DNAME`
WHERE
	`EMP`.`JOB` = 'CLERK';
```

    +--------+------------+-----------+
    | ENAME  | DNAME      | EMP COUNT |
    +--------+------------+-----------+
    | SMITH  | RESEARCH   | 5         |
    | ADAMS  | RESEARCH   | 5         |
    | JAMES  | SALES      | 6         |
    | MILLER | ACCOUNTING | 3         |
    +--------+------------+-----------+

### Find jobs with minimum salary > 1,500

Include `JOB` and `EMP COUNT` for each job.

```sql
SELECT
	`JOB`,
	COUNT(*)
FROM
	`EMP`
GROUP BY
	`JOB`
HAVING
	MIN(`SAL`) > 1500;
```

    +-----------+----------+
    | JOB       | COUNT(*) |
    +-----------+----------+
    | MANAGER   | 3        |
    | ANALYST   | 2        |
    | PRESIDENT | 1        |
    +-----------+----------+

### Find employees on department `SALES`.

Include `ENAME` for each employee.
Assume you do not know the `DEPTNO` for `SALES`.

```sql
SELECT
	`ENAME`
FROM
	`EMP`
WHERE
	`DEPTNO` = 
	(
		SELECT
			`DEPTNO`
		FROM
			`DEPT`
		WHERE
			`DNAME` = 'SALES'
	);
```

    +--------+
    | ENAME  |
    +--------+
    | ALLEN  |
    | WARD   |
    | MARTIN |
    | BLAKE  |
    | TURNER |
    | JAMES  |
    +--------+

### Find employees with salary higher than average

Include `ENAME`, department name `DEPT`, manager name `MANAGER`
and `SALGRADE` for each employee.

```sql
SELECT
	E.`ENAME`,
	`DEPT`.`DNAME` AS 'DEPARTMENT',
	M.`ENAME` AS 'MANAGER',
	`SALGRADE`.`GRADE` AS 'SALGRADE'
FROM
	`EMP` AS E -- for employees
	LEFT OUTER JOIN
		`EMP` AS M -- for mgrs
		ON
			E.`MGR` = M.`EMPNO`
	INNER JOIN
		`DEPT`
		ON
			E.`DEPTNO` = `DEPT`.`DEPTNO`
	INNER JOIN
		`SALGRADE`
		ON
			E.`SAL` BETWEEN `SALGRADE`.`LOSAL` AND `SALGRADE`.`HISAL`
WHERE
	E.`SAL` > (SELECT AVG(`SAL`) FROM `EMP`);
	
```

    +-------+------------+---------+----------+
    | ENAME | DEPARTMENT | MANAGER | SALGRADE |
    +-------+------------+---------+----------+
    | FORD  | RESEARCH   | JONES   | 4        |
    | SCOTT | RESEARCH   | JONES   | 4        |
    | CLARK | ACCOUNTING | KING    | 4        |
    | BLAKE | SALES      | KING    | 4        |
    | JONES | RESEARCH   | KING    | 4        |
    | KING  | ACCOUNTING | <null>  | 5        |
    +-------+------------+---------+----------+

### Find all employees that are on the same job as Scott

Include `ENAME` and `DNAME`.

```sql
SELECT
	`EMP`.`ENAME`,
	`DEPT`.`DNAME`
FROM
	`EMP`
	INNER JOIN
		`DEPT`
		ON
			`EMP`.`DEPTNO` = `DEPT`.`DEPTNO`
WHERE
	`EMP`.`ENAME` <> 'SCOTT'
	AND
	`EMP`.`JOB` = 
	(
		SELECT
			`JOB`
		FROM
			`EMP`
		WHERE
			`ENAME` = 'SCOTT'
	);
```

    +-------+----------+
    | ENAME | DNAME    |
    +-------+----------+
    | FORD  | RESEARCH |
    +-------+----------+

### Find employees with salary equal to salaries of any employee in department 30

Include `ENAME` and `SAL`.

```sql
SELECT
	`ENAME`,
	`SAL`
FROM
	`EMP`
WHERE
	`SAL` IN
	(
		SELECT DISTINCT
			`SAL`
		FROM
			`EMP`
		WHERE
			`DEPTNO` = 30	
	)
	AND
	`DEPTNO` <> 30;
```

    Empty set

### Find employees with higher salary than the highest salary in department 30.

Include `ENAME`, `SAL` and `DNAME`.

```sql
SELECT
	`ENAME`,
	`SAL`,
	`DEPT`.`DNAME`
FROM
	`EMP`
	INNER JOIN
		`DEPT`
		ON
			`DEPT`.`DEPTNO` = `EMP`.`DEPTNO`
WHERE
	`SAL` >
	(
		SELECT 
			MAX(`SAL`)
		FROM
			`EMP`
		WHERE
			`DEPTNO` = 30
	)
	AND
	`EMP`.`DEPTNO` <> 30;
```

    +-------+--------+------------+
    | ENAME | SAL    | DNAME      |
    +-------+--------+------------+
    | JONES | 2975.0 | RESEARCH   |
    | SCOTT | 3000.0 | RESEARCH   |
    | KING  | 5000.0 | ACCOUNTING |
    | FORD  | 3000.0 | RESEARCH   |
    +-------+--------+------------+

### Q26: skipped due to ambiguous question description

### List `ENAME`, `DEPT` and `SAL` for each employee

```sql
SELECT
	`ENAME`,
	`DEPT`.`DNAME` AS 'DEPT',
	`SAL`
FROM
	`EMP`
INNER JOIN
	`DEPT`
ON
	`EMP`.`DEPTNO` = `DEPT`.`DEPTNO`;
```

### List `*` from `DEPT` and employee count for each department

```sql
SELECT
	`DEPT`.`DEPTNO`,
	`DEPT`.`DNAME`,
	`DEPT`.`LOC`,
	COUNT(`EMP`.`ENAME`) AS 'EMP COUNT'
	-- any of `EMP` element is fine since all of them are null
FROM
	`DEPT`
	LEFT OUTER JOIN
		`EMP`
		ON
			`EMP`.`DEPTNO` = `DEPT`.`DEPTNO`
GROUP BY
	`DEPT`.`DEPTNO`;
```

    +--------+------------+----------+-----------+
    | DEPTNO | DNAME      | LOC      | EMP COUNT |
    +--------+------------+----------+-----------+
    | 10     | ACCOUNTING | NEW YORK | 3         |
    | 20     | RESEARCH   | DALLAS   | 5         |
    | 30     | SALES      | CHICAGO  | 6         |
    | 40     | OPERATIONS | BOSTON   | 0         |
    +--------+------------+----------+-----------+

### Find employees that are on the lowest salary of their job

Include `*` from `EMP`.

```sql
SELECT
	*
FROM
	`EMP`
	INNER JOIN
	(
		SELECT
			`JOB`,
			MIN(`SAL`) AS `MIN SAL`
		FROM
			`EMP`
		GROUP BY
			`JOB`
	) AS T
		ON
			`EMP`.`JOB` = T.`JOB`
			AND
			`EMP`.`SAL` = T.`MIN SAL`;
		
```

### Find minimum salary for employees as managers in each department

Include `DEPTNO` and `MIN SAL`.

```sql
SELECT
	`DEPTNO`,
	MIN(`SAL`)
FROM
(
	SELECT
		*
	FROM
		`EMP`
	WHERE
		`EMPNO` IN
		(
			SELECT
				`MGR`
			FROM
				`EMP`
		)
) AS MANAGERS
GROUP BY
	`DEPTNO`
ORDER BY
	`DEPTNO` ASC;
```

    +--------+------------+
    | DEPTNO | MIN(`SAL`) |
    +--------+------------+
    | 10     | 2450.0     |
    | 20     | 2975.0     |
    | 30     | 2850.0     |
    +--------+------------+

### Find annual salary for each employee

Include `ENAME` and `INCOME`.
Order results in ascending order of `INCOME`.

```sql
SELECT
	`ENAME`,
	12 * (IFNULL(`COMM`, 0) + `SAL`) AS 'INCOME'
FROM
	`EMP`
ORDER BY
	`INCOME` ASC;
```

    +--------+---------+
    | ENAME  | INCOME  |
    +--------+---------+
    | SMITH  |  9600.0 |
    | JAMES  | 11400.0 |
    | ADAMS  | 13200.0 |
    | MILLER | 15600.0 |
    | TURNER | 18000.0 |
    | WARD   | 21000.0 |
    | ALLEN  | 22800.0 |
    | CLARK  | 29400.0 |
    | MARTIN | 31800.0 |
    | BLAKE  | 34200.0 |
    | JONES  | 35700.0 |
    | SCOTT  | 36000.0 |
    | FORD   | 36000.0 |
    | KING   | 60000.0 |
    +--------+---------+

### Find employees whose manager's salary is higher than 3,000

Include `ENAME` and `MGR NAME`.

```sql
SELECT
	`EMP`.`ENAME`,
	T_MANAGER.`ENAME` AS 'MGR NAME'
FROM
	`EMP`
	INNER JOIN
		`EMP` AS T_MANAGER
		ON
			`EMP`.`MGR` = T_MANAGER.`EMPNO`
WHERE
	`EMP`.`MGR` IN
	(
		SELECT
			`EMPNO`
		FROM
			`EMP`
		WHERE
			`EMPNO` IN 
			(
				SELECT
					`MGR`
				FROM
					`EMP`
			)
			AND
			`SAL` > 3000
	);
```

    +-------+----------+
    | ENAME | MGR NAME |
    +-------+----------+
    | JONES | KING     |
    | BLAKE | KING     |
    | CLARK | KING     |
    +-------+----------+

### Find departments with 'S' in the name

Include `DNAME`, salary sum of their employees `SAL SUM` and `EMP COUNT`.

```sql
SELECT
	`DEPT`.`DEPTNO`,
	IFNULL(SUM(`SAL`), 0),
	COUNT(`EMP`.`ENAME`)
FROM
	`DEPT`
	LEFT OUTER JOIN
		`EMP`
		ON
			`DEPT`.`DEPTNO` = `EMP`.`DEPTNO`
WHERE
	`DEPT`.`DNAME` RLIKE '.*[Ss].*'
GROUP BY
	`DEPT`.`DEPTNO`;
```

    +--------+-----------------------+----------------------+
    | DEPTNO | IFNULL(SUM(`SAL`), 0) | COUNT(`EMP`.`ENAME`) |
    +--------+-----------------------+----------------------+
    | 20     | 10875.0               | 5                    |
    | 30     |  9400.0               | 6                    |
    | 40     |     0.0               | 0                    |
    +--------+-----------------------+----------------------+

### Promote 10% for employees with time hired more than 30 years

```sql
UPDATE
	`EMP`
SET
	`SAL` = 1.1 * `SAL`
WHERE
	TIMESTAMPDIFF(YEAR, `HIREDATE`, NOW()) > 30;
```
