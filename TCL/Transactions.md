# Transactions

Transaction is five-star concept in DBMS and must be fully mastered.

### Prerequisite terminologies

##### Business logic

A part of a program that demonstrates the real-world business rules that determine how data can be created, stored, and changed.

It is contrasted with the remainder of the software, which might be concerned with lower-level details of managing a database,
or displaying the user interface, system infrastructure, or generally connecting various parts of the program.

For instance, the complete business logic behind a cash transaction where account A transfers £10000 to account B is:

    UPDATE Account A DECREASE £10000;
    UPDATE Account B INCREASE £10000;

This is the most basic unit of the business, where both actions must be either **both** successful or **both** failed.

### Concept of transaction

A transaction is a complete business logic consists of multiple update statements on data;
which, again, must be either **all** successful or **all** failed.

So basically, a transaction is a batch of update statements that must be successfully executed all in once.

#### Transactional statements

Only these 3 DML statements are in relation to transactions:

*   `INSERT`
*   `DELETE`
*   `UPDATE`

... as only these statements manipulates records in a table.

Once there are DML action, the data safety must be concerned.

#### Properties of transactions: ACID

Data validity during transactions is (intended to be) guaranteed by 4 properties with an acronym ACID:
| Property | Explain |
|-|-|
| Atomicity | Transactions are the most basic unit in business |
| Consistency | Operations within a transaction must be either successful or failed ALL at once |
| Isolation | Concurrency control when multiple transactions are focusing on the same table |
| Durability | Committed transactions must be permanent and non-volatile |

Transactions are sequences of operations satisfying these properties.

### Concurrency control (isolation)

#### Isoation levels

There are 4 isolation levels, from lowest to highest:

> Read uncommitted < Read committed < Repeatable read < Serialisable

##### Read uncommitted

At this level, transaction A is allowed to read uncommitted changes from transaction B in the same table.
This is a *dirty read*.

Virtually no table uses this level at all;
even the most relaxed ones start from Read committed.

##### Read committed

At this level, transaction A can only read committed changes from transaction B;
dirty read is not allowed.

Data read in this isolation level is always correct; however the data queried by A will be subject to change
as commited changes made by other transactions on this table will be effective immediately.

##### Repeatable read

When transaction A starts at this level,
it takes snapshots for tables and carry out queries based on them;
this "froze" the table at a certain timestamp;
hence transaction A will always get the same query results even if other transactions make changes to the table.

This remains true throughout the same transaction.
Ending transaction A and starting a new transaction retakes new snapshots for tables.

This makes data "repeatable". However the data read each time may not be an actual representation.

##### Serialisable

Similar to "synchronized" in java, concurrency is completely blocked at this level;
A table of serialisable can only be queried when free of other transactions.

##### Demonstrating

The concurrent transactions can be demonstrated by starting multiple MySQL sessions at the same time for one table.

Try this yourself; this is too verbose to put the logs here.

### Mechanism

To make statements in a transaction succeed or fail all at once, InnoDB provides a set of transaction log files,
documenting transactional activities, something like:

<!-- @keep format -->

    START TRANSACTION
    [insert DML statements here]
    [insert DML statements here]
    [insert DML statements here]
    ...
    END TRANSACTION

<!-- @continue format -->

During a transaction, every DML action will be recorded in the transaction log files.

At the finishing of a transaction, we either `COMMIT` or `ROLLBACK` it:

*   `COMMIT`ing a transaction permanently applies all transactional activities to the database and clear relevant logs;
    it marks a permanent change update to the database, and a completely successful ending for the transaction.
*   Meanwhile, `ROLLBACK`ing a transaction withdraws all actions and clears relevant logs;
    it marks a completely failed ending for the transaction.

### Syntax

Normally MySQL considers each DML statement as one transaction, and automatically commits them after their execution;
hence blunt `ROLLBACK`ing will not come to effect.

This does not fit the actual scenario of development
where a transaction covers a batch of DML statements.
To perform transactions manually, execute the following to start one with manual ending:

```sql
START TRANSACTION;
```

At the end of transaction, finish with either `COMMIT;` or `ROLLBACK;`.

Demonstrating `ROLLBACK`:

```sql
mysql> SELECT * FROM `dept_bak`;
+--------+-------+-----+
| DEPTNO | DNAME | LOC |
+--------+-------+-----+
+--------+-------+-----+

mysql> START TRANSACTION;
Query OK, 0 rows affected

mysql> INSERT INTO `dept_bak` (`DEPTNO`, `DNAME`, `LOC`)
                     ... VALUES
                     ... (10, 'SALES', 'BEIJING'),
                     ... (10, 'SALES', 'BEIJING'),
                     ... (10, 'SALES', 'BEIJING'),
                     ... (10, 'SALES', 'BEIJING');
Query OK, 4 rows affected

mysql> SELECT * FROM `dept_bak`;
+--------+-------+---------+
| DEPTNO | DNAME | LOC     |
+--------+-------+---------+
| 10     | SALES | BEIJING |
| 10     | SALES | BEIJING |
| 10     | SALES | BEIJING |
| 10     | SALES | BEIJING |
+--------+-------+---------+

mysql> ROLLBACK;
Query OK, 0 rows affected

mysql> SELECT * FROM `dept_bak`;
+--------+-------+-----+
| DEPTNO | DNAME | LOC |
+--------+-------+-----+
+--------+-------+-----+
```
