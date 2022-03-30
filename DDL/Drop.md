# Dropping a table

### Syntax

1.  Use command `DROP TABLE` to drop a specified table.

```sql
DROP TABLE
	t_student;
```

This throws an error when the table does not exist;
to suppress, append `IF EXISTS`:

```sql
DROP TABLE IF EXISTS
	t_student;
```
