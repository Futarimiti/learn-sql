# Creating a table

## Syntax

### Create new table

Use `CREATE TABLE` command to create a new table giving descriptions including table name, fields name and their types.

Bracket the table desc after the table name.

<!-- @keep format -->
```sql
CREATE TABLE table_name
(
	field_1 type_1,
	field_2 type_2,
	field_3 type_3,
	...
);
```
<!-- @continue format -->

Note that no trailing commas are allowed.

Extra [constraints](#constraints) may be added to a field, such as primary key and non-nullability:

<!-- @keep format -->
```sql
ID int NOT NULL PRIMARY KEY,
```
<!-- @continue format -->

If we'd like to create a table of records of movies `t_movie`, including number `no`, name `name`, description `desc`,
playtime `playtime`, length `time`, poster `image`, type `type`
for each movie:

```sql
CREATE TABLE t_movie
(
	`no`       bigint NOT NULL PRIMARY KEY,
	`name`     varchar(255),
	`desc`     LONGTEXT, -- does not recognise clob
	`playtime` date,
	`time`     double,
	`image`    blob,
	`type`     varchar(100)
);
```

#### Constraints

Constraints on fields guarantee validity of values.

Common constraints in MySQL:

| Constraint     | Meaning                       |
|----------------|-------------------------------|
| `NOT NULL`     | Non-nullable                  |
| `UNIQUE`       | No repeating non-null values  |
| `PRIMARY KEY`  | Primary key                   |
| `FOREIGN KEY`  | Foreign key                   |

The constraints may be put right behind particular field as a **field-level** constraint, or associated with multiple
fields as a **table-level** constraint (see `UNIQUE` example below).

##### `UNIQUE` on multiple fields

Suppose we have the following table:

```sql
CREATE TABLE t_user
(
	`id`    int,
	`name`  varchar(225),
	`email` varchar(225)
);
```

The demand is to treat records with `name` and `email` **both** being the same as duplicates. That is saying,
inserting `('Jack', 'mail1')` and `('Jack', 'mail2')` should not be conflicting.

Here we are trying to restrain `UNIQUE` on the union of fields `name` and `email`. To achieve this, we can use `UNIQUE`
as in the following syntax:

```sql
CREATE TABLE t_user
(
	`id`    int,
	`name`  varchar(225),
	`email` varchar(225),
	UNIQUE (`name`, `email`)
);
```

Here the `UNIQUE` constraint does not follow any field and is at table-level.

##### Primary key (PK)

Primary key is the **unique** id of every record.

Every legal table must include *one and only one* primary key.

A primary key should be `NOT NULL` + `UNIQUE`. In MySQL, a `NOT NULL` `UNIQUE` field is automatically considered as
primary key.

Constraint `PRIMARY KEY` can be at both field- and table-level. A **composite** primary key is possible, but not
recommended in actual development:

```sql
CREATE TABLE t_user
(
	`id`    int,
	`name`  varchar(225),
	`email` varchar(225),
	PRIMARY KEY (`id`, `name`)
);
```

Note that we are still having only *one* primary key. A table should always contain one and only one primary key.

Primary keys are commonly of type `int`, `bigint` or `char`.
`varchar` is not recommended as primary key values are often fixed-length numbers.

###### Natural & surrogate primary key

Depending on its nature, a primary key could be either natural or surrogate.

A surrogate primary key is completely irrelevant to the current business, such as serial numbers 1, 2, 3...

A natural primary key is selected from the current business, such as card number and ID number that appears to be unique
on different records.

Comparatively surrogate primary key is more extensively used in the business. This is because primary keys should be
sought to be distinct and does not have to be meaningful. Natual primary keys highly depends on the current business,
which could be easily affected by changes brought to the business; for example, a licence plate number or card number
may be subject to change.

###### Auto primary key

Using `AUTO_INCREMENT` automatically generates surrogate primary key values from 1:

```sql
CREATE TABLE t_user
(
	`id`    int PRIMARY KEY AUTO_INCREMENT,
	`name`  varchar(225),
	`email` varchar(225)
);
```

Inserting records requires `id` entry no more:

```sql
INSERT INTO
	`t_user` (`name`, `email`)
VALUES
	('Jack', 'jack@mil.com'),
	('Emily', 'emm@pmail.com'),
	('Emma', 'fucme@616.com');
```

`id` is automatically generated as:

<!-- @keep format -->
```sql
mysql> SELECT * FROM `t_user`;
+----+-------+---------------+
| id | NAME  | email         |
+----+-------+---------------+
| 1  | Jack  | jack@mil.com  |
| 2  | Emily | emm@pmail.com |
| 3  | Emma  | fucme@616.com |
+----+-------+---------------+
```
<!-- @continue format -->

### Create from a template table

It is possible to create a table as a duplicate of another table.

This speeds up creation of a table if there is already one with similar syntax.

The following `CREATE AS` clause duplicates table `EMP`:

```sql
CREATE TABLE
	`EMP2`
AS
	SELECT
		*
	FROM
		`EMP`;
```

This is frequently used as to back up a table.

It is also possible to include part of the columns:

```sql
CREATE TABLE
	`EMP2`
AS
	SELECT
		`EMPNO`,
		`ENAME`
	FROM
		`EMP`
	WHERE
		JOB = 'MANAGER';
```

<!-- @keep format -->
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
<!-- @continue format -->

### Naming convention

Table names and field names belong to identifiers.

A table name usually starts with `t_` or `tbl_` to enhance readability.

All these identifiers should be in **fully\_lowercase\_snake\_case**; their names should represent their meanings.
