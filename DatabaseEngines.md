# Database engine / storage engine

A database engine is is the underlying software component that a DBMS uses
to create, read, update and delete (CRUD) data from a database.
Different engines lead to different modes of data storage.

The default storage engine for MySQL is InnoDB.
This can be inspected using `SHOW CREATE TABLE table_name` command.
It should return something like:

    +-----------+-----------------------------------------------------------------------------------------------+
    | Table     | Create Table                                                                                  |
    +-----------+-----------------------------------------------------------------------------------------------+
    | t_student | CREATE TABLE `t_student` (                                                                    |
    |		|	`no` int NOT NULL AUTO_INCREMENT,                                                       |
    |		|	`name` varchar(225) DEFAULT NULL,                                                       |
    |		|	`class_no` int DEFAULT NULL,                                                            |
    |		|	PRIMARY KEY (`no`),                                                                     |
    |		|	KEY `class_no` (`class_no`),                                                            |
    |		|	CONSTRAINT `t_student_ibfk_1` FOREIGN KEY (`class_no`) REFERENCES `t_class` (`class_no`)|
    |		|) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci 		| 
    +-----------+-----------------------------------------------------------------------------------------------+

Where the `ENGINE` gives the storage engine in use for this table.
Some other information such as `DEFAULT CHARSET` are also available here.

### Common engines

Command `SHOW ENGINES` gives a full list of storage engines supported by MySQL.
A `\G` can be appended at the end for vertical display.

#### MyISAM

MyISAM uses 3 files to represent a table:

| File | Storage |
|--|--|
| `.frm` | Table structure |
| `.MYD` | Records |
| `.MYI` | Index (facilitates searching) |

Note that for every table, index will be generated for every `PRIMARY KEY` or `UNIQUE` fields.
This facilitates indexing content by narrowing down the scope of searching.

Storing in separate files excels for the space-saving property,
where either these files can be compressed, or convert to read-only table.

See [this link](https://dev.mysql.com/doc/internals/en/myisam-files.html) for more details.

MyISAM does not support transactions and has low safety.

#### InnoDB

InnoDB is a heavyweight engine.

InnoDB supports transactions, including auto-recovery after breakdown of a database.
This property makes InnoDB very safe to use.

However on the other hand, this mechanism makes InnoDB less efficient;
also cannot safe storage like others by compressing files.

Every InnoDB table is represented as a `.frm` file in the database catalogue.
The InnoDB "tablespace", a logical formative form of address, stores indexing and content of the table.
InnoDB also provides a set of transaction log files.

InnoDB also supports foreign keys and referencing, cascading deleting and updating.

#### MEMORY

Used to be called HEAP engine.

MEMORY stores tables in `.frm` files in the database catalogue.
Content and indexing are stored in memory with fixed row length.
This is much more efficient than in a hard drive, making it VERY FAST for queries;
however this also reduces safety, as the data disappear once after shutdown.

Supports table locks mechanism.

Cannot handle `TEXT` or `BLOB` fields. Also does not support transactions.

### Assigning an engine

It is possible to assign a storage engine when creating a table.
This is done by appending `ENGINE` option at the end of `CREATE` clause:

```sql
CREATE TABLE `new_table`
(
	`id` int PRIMARY KEY AUTO_INCREMENT,
	`name` varchar(225) NOT NULL
) ENGINE=engine_name;
```

When not specified, InnoDB will be used as the default engine.

Some other options may be specified at the same time.
