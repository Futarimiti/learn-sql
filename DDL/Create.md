# Creating a table

### Syntax

#### Create new table

Use `CREATE TABLE` command to create a new table
giving descriptions including table name, fields name and their types.

Bracket the table desc after the table name.

```sql
CREATE TABLE
	table_name
	(
		field_1 type_1,
		field_2 type_2,
		field_3 type_3,
		...
	);
```

Note that no trailing commas are allowed.

Extra restraints may be added to a field, such as primary key and non-nullability:

```sql
...
ID int NOT NULL PRIMARY KEY,
...
```

If we'd like to create a table of records of movies `t_movie`,
including number `no`, name `name`, description `desc`, playtime `playtime`, length `time`, poster `image`, type `type`
for each movie:

```sql
CREATE TABLE
	t_movie
	(
		`no` bigint NOT NULL PRIMARY KEY,
		`name` varchar(255),
		`desc` LONGTEXT, -- does not recognise clob
		`playtime` date,
		`time` double,
		`image` blob,
		`type` varchar(100)
	);
```

#### Create from a template table

It is possible to create a table as a duplicate of another table.

This speeds up creation of a table if there is already one with similar syntax.

The following `CREATE AS` clause duplicates table `EMP`:

```sql
CREATE TABLE `EMP2` AS (SELECT * FROM `EMP`);
```

This is frequently used as to back up a table.

It is also possible to include part of the columns:

```sql
CREATE TABLE
	`EMP2`
AS
(
	SELECT
		`EMPNO`, `ENAME`
	FROM
		`EMP`
	WHERE
		JOB = 'MANAGER'
);
```

```sql
mysql> SELECT * FROM `EMP2`;
+-------+-------+
| EMPNO | ENAME |
+-------+-------+
| 7566  | JONES |
| 7698  | BLAKE |
| 7782  | CLARK |
+-------+-------+
```

### Naming convention

Table names and field names belong to identifiers.

A table name usually starts with `t_` or `tbl_` to enhance readability.

All these identifiers should be in **fully\_lowercased\_snake\_case**;
their names should represent their meanings.
