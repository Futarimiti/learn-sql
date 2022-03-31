# Creating a table

## Syntax

### Create new table

Use `CREATE TABLE` command to create a new table giving descriptions including table name, fields name and their types.

Bracket the table desc after the table name.

<!-- @keep format -->

```sql
CREATE TABLE `table_name`
(
	`field_1` type_1,
	`field_2` type_2,
	`field_3` type_3,
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
CREATE TABLE `t_movie`
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
CREATE TABLE `t_user`
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
CREATE TABLE `t_user`
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
CREATE TABLE `t_user`
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
CREATE TABLE `t_user`
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

#### Foreign key (FK)

Imagine we are going to process a school system into DBMS, where each student has a `no(PK)`, `name` and is in a class,
where each class has a `class_name` and a `class_no`.

If we approach to put everything in one table, such as:

| no  | name   | class\_no      | class\_name |
|-----|--------|----------------|-------------|
| 1   | Jack   | 100            | B1          |
| 2   | Lucy   | 100            | B1          |
| 3   | Emily  | 101            | B2          |
| 4   | Emma   | 101            | B2          |

Which is a bad design as `class_no` and `class_name` redundantly appear in the table, causing needless memory waste.

Another approach is to use two tables, `t_student` and `t_class`:

`t_student`:

| no       | name       | class\_no |
|----------|------------|-----------|
| 1        | Jack       | 100       |
| 2        | Lucy       | 100       |
| 3        | Emily      | 101       |
| 4        | Emma       | 101       |

`t_class`:

| class\_no       | class\_name |
|-----------------|-------------|
| 100             | B1          |
| 101             | B2          |

Much better. One more thing to make sure: `class_no` in `t_student` must be from `t_class`, otherwise invalid `class_no`
may be used.

This can be done with foreign constraint, and here we are going to apply that on `t_student.class\_no`. That is
saying: `t_student.class\_no` is a foreign field and its values must be foreign.

##### Foreign tables creating/dropping order

Here as we constrain a foreign key, the table `t_class` becomes parental to `t_student`. This restrains orders when
manipulating these tables:

*   `t_class` must be created **prior** to `t_student`;
*   `t_class` must be `DROP`ed **after** `t_student`, and vice versa for `DELETE` or `TRUNCATE`;
*   When updating class-related records, `t_class` must be updated **prior** to `t_student`.

##### Syntax

Having that in mind, we can now create `t_class` and `t_student`.

A foreign key is defined using `FOREIGN KEY ... REFERENCES ...` syntax:

```sql
CREATE TABLE `t_class`
(
	`class_no`   int PRIMARY KEY,
	`class_name` varchar(225)
);

CREATE TABLE `t_student`
(
	`no`       int PRIMARY KEY AUTO_INCREMENT,
	`name`     varchar(225),
	`class_no` int,
	FOREIGN KEY (`class_no`) REFERENCES `t_class` (`class_no`)
);
```

We herein define `t_student.class_no` as a foreign field of `t_class.class_no`. Now we can fill records in `t_class`
and `t_student`. Pay attention to the execution order:

```sql
INSERT INTO
	`t_class` (`class_no`, `class_name`)
VALUES
	(100, 'B1'),
	(101, 'B2');

INSERT INTO
	`t_student` (`name`, `class_no`)
VALUES
	('Jack', 100),
	('Lucy', 100),
	('Emily', 101),
	('Emma', 101);
```

And we're done. It is illegal to use a `class_no` not included in `t_class`, and an error will be thrown:

<!-- @keep format -->

```sql
mysql> INSERT INTO `t_student` (`name`, `class_no`) VALUES ('Jack', 200);
(1452, 'Cannot add or update a child row: a foreign key constraint fails (`bjpowernode`.`t_student`, CONSTRAINT `t_student_ibfk_1` FOREIGN KEY (`class_no`) REFERENCES `t_class` (`class_no`))')
```

<!-- @continue format -->

Note that this does not mean two tables are joined, but only constrained possible values of `t_student.class_no`
to `t_class.class_no`.

###### Notes

*   A foreign key must reference a `UNIQUE` field. <!-- This includes `PRIMARY KEY`. -->
    This is designed to avoid ambiguous query results when joining tables.
    > If B1 and B2 both take 100 for `class_no`, then which `class_name` should be shown up for Jack when joining `t_student` and `t_class`?

*   Foreign keys are nullable as long as `UNIQUE` condition is met.

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
		`JOB` = 'MANAGER';
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

### Create options

When creating the table, some creating options may be specified.
These commonly include `ENGINE` and `DEFAULT CHARSET`.

To set these options, append them at the end of the `CREATE` clause,
right after the fields bracket:

```sql
CREATE TABLE `new_table`
(
	`id` int PRIMARY KEY AUTO_INCREMENT,
	`name` varchar(225) NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=gbk; -- 允许中文输入
```
