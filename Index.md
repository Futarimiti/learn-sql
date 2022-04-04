# Indexing

Indexing is a mechanism to boost query efficiency
by narrowing down the scope of searching.

Indexing is implemented on fields of a table.
An indexing can be implemented on either a single field or a compound field.

### Mechanism

Ordinarily, to find relevant rows, MySQL begins with the first row,
crossing the entire table, all the way down to the bottom;
this costs more as the table grows.

Using the indexing is like using a dictionary:
MySQL will look for a rough location first;
after jumping to that location, MySQL carries out a local search
with a narrower range to find the specific row.
This narrowing in scanning range largely accelerates speed of query.

### Implementing in MySQL

Indexing must be sorted so local scanning can be possible.

In MySQL, indexing is implemented with self-balancing binary search tree, i.e. B-tree.
Data are stored on left-small-right-big basis,
and are retrieved with inorder traversal (LDR).

Every record in a table has its physical storing id on the disc.

Indexing is a single, independent object, and different storage engines store them in different ways;
no matter how is it stored, its B-tree structure is always maintained.

### When should I use indexing?

Consider these 3 factors before using indexing:

*   Size of table
    *   may require testing from client's device
*   Frequency of being scanned
    *   if this field is frequently used in `WHERE` clause, then consider use indexing
*   Frequency of DML updates to the table
    *   which requires the indexing to be sorted again

Use or not usually dependents on the query speed feedback from the client.
It should not be arbitrarily used as indexings are required to be maintained,
which may backfire on the efficiency when there are too many indexings.

Usually search using `UNIQUE` fields, which are automatically created indexing,
can resolve the efficiency problem in most cases.

### Implementing on fields

In MySQL, any `UNIQUE` fields will be automatically created indexing; this includes primary keys.

To manually create indexing for a field, use the command in the following syntax:

```sql
CREATE INDEX index_name
ON table_name(field_name...);
```

To remove an indexing:

```sql
DROP INDEX index_name
ON table_name;
```

To see if a query utilises indexing, use `EXPLAIN` statement:

```sql
EXPLAIN
	SELECT
		*
	FROM
		`EMP`
	WHERE
		`ENAME` = 'KING';
```

When not using indexing, `type` is `ALL` and `ref` is `NULL`;
otherwise `type` field shows `ref` and `ref` field gives which indexing is in use.
`rows` gives total number of rows scanned in this query.

### Failure of indexing

#### Fuzzy search

Indexing may not be 100% utilised in various situations.

Consider the following fuzzy search example:

```sql
SELECT
	*
FROM
	`EMP`
WHERE
	`ENAME` LIKE '%T';
```

Indexing will not be used even if there is one on `ENAME` - there's no way to do so at all.
Hence during fuzzy search, try not to start the pattern with '%'.

#### `OR`

Unless both sides of `OR` has used indexing, indexing will not be used.

A solution is to use `UNION` in place of `OR`.

#### In compound indexing

When creating indexing for compound fields, querying on the field that is not a leftmost
will not trigger indexing search:

```sql
CREATE INDEX EMP_JOB_SAL
ON EMP(JOB, SAL);

EXPLAIN
	SELECT
		*
	FROM
		`EMP`
	WHERE
		`JOB` = 'MANAGER'; # OK
		
EXPLAIN
	SELECT
		*
	FROM
		`EMP`
	WHERE
		`SAL` = 800; # NO INDEXING SEARCH
```

    +----+-------------+-------+------------+------+---------------+-------------+---------+-------+------+----------+--------+
    | id | select_type | table | partitions | type | possible_keys | key         | key_len | ref   | rows | filtered | Extra  |
    +----+-------------+-------+------------+------+---------------+-------------+---------+-------+------+----------+--------+
    | 1  | SIMPLE      | EMP   | <null>     | ref  | EMP_JOB_SAL   | EMP_JOB_SAL | 39      | const | 3    | 100.0    | <null> |
    +----+-------------+-------+------------+------+---------------+-------------+---------+-------+------+----------+--------+

    +----+-------------+-------+------------+------+---------------+--------+---------+--------+------+----------+-------------+
    | id | select_type | table | partitions | type | possible_keys | key    | key_len | ref    | rows | filtered | Extra       |
    +----+-------------+-------+------------+------+---------------+--------+---------+--------+------+----------+-------------+
    | 1  | SIMPLE      | EMP   | <null>     | ALL  | <null>        | <null> | <null>  | <null> | 14   | 10.0     | Using where |
    +----+-------------+-------+------------+------+---------------+--------+---------+--------+------+----------+-------------+

#### Field involved in operations in `WHERE` clause

```sql
CREATE INDEX EMP_SAL
ON EMP(SAL);

EXPLAIN
	SELECT
		*
	FROM
		`EMP`
	WHERE
		`SAL` + 1 = 801;
```

    +----+-------------+-------+------------+------+---------------+--------+---------+--------+------+----------+-------------+
    | id | select_type | table | partitions | type | possible_keys | key    | key_len | ref    | rows | filtered | Extra       |
    +----+-------------+-------+------------+------+---------------+--------+---------+--------+------+----------+-------------+
    | 1  | SIMPLE      | EMP   | <null>     | ALL  | <null>        | <null> | <null>  | <null> | 14   | 100.0    | Using where |
    +----+-------------+-------+------------+------+---------------+--------+---------+--------+------+----------+-------------+

Involving in scalar functions in `WHERE` results in the same.
