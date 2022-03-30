# Update a record

An `UPDATE` clause updates particular records in a table.

### Syntax

```sql
UPDATE
	table_name
SET
	field1 = new_value1,
	field2 = new_value2,
	field3 = new_value3,
	...
WHERE
	conditions - apply to which records
```

Conditons in `WHERE` clause specify which records to be updated;
only ones satisfying the conditions will be updated.

Omitting the `WHERE` clause results in all records being updated.
