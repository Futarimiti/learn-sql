# Truncating a table

`TRUNCATE` clause removes all records from a table quickly and *physically* - records are irreversibly removed
as their actual memory allocation being withdrawn. This action cannot be undone with `ROLLBACK`.

Before `TRUNCATE`ing a table, ask carefully and warn client that there is no way back.

This does bring greater efficiency than `DELETE`; a table of hundreds of billions of records
can be `TRUNCATE`d in a few seconds.

Note that a `TRUNCATE` clause only removes records, not the table or its structure.
Here it comes to difference with `DROP`.

### Syntax

Truncating a table:

```sql
TRUNCATE TABLE table_name;
```

The whole table is truncated. There is no way to truncate with condition.
