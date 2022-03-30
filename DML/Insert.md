# Insert a record

`INSERT` clause adds a record of data to an existing table.

Successful execution of an `INSERT` clause always add a new record to the table.

### Syntax

To insert a record, you should specify:

*   Insert into *which* table
*   Fill *which* fields...
*   ... with *what* values

The overall syntax of an `INSERT` clause:

```sql
INSERT INTO
	table_name (field1, field2, field3...)
VALUES (value1, value2, value3...);
```

The order of fields and values does not matter as long as
the fields and values must be consistent on number and on type.

The only instance where the fields bracket may be omitted is when **every** field is specified for a record:

```sql
INSERT INTO
	table_name
VALUES (value1, value2, value3... valueN); -- all N fields
```

For the sake of readability, it is strongly recommended to include the fields.

Default value (usually `NULL`) will be used for fields not specified a value.
Error will be thrown for those without a default value such as primary key.
