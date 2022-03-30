# Deleting records

A `DELETE` clause removes specific records from a table.

### Syntax

```sql
DELETE FROM
	table_name
WHERE
	conditions
```

`WHERE` clause specifies which records to be deleted;
omission results in all records being removed.

### Retrieving

While being `DELETE`d, the actual memory of the record is not eliminated.

This compromises efficiency but makes it possible to restore `DELETE`d records.

Retrieving is done by `ROLLBACK` clause, which will be introduced later.

`TRUNCATE` clause removes a record completely.
