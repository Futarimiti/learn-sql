# Insert records

`INSERT` clause adds record(s) of data to an existing table.

Successful execution of an `INSERT` clause always add new record(s) to the table.

### Syntax

##### Add one record

To insert a record, you should specify:

*   Insert it into *which* table
*   Fill *which* fields...
*   ... with *what* values for this record

The overall syntax of an `INSERT` clause:

```sql
INSERT INTO
	table_name (field1, field2, field3...)
VALUES
	(value1, value2, value3...);
```

##### Add multiple records

To insert multiple records, you should specify:

*   Insert them into *which* table
*   Fill *which* fields...
*   ... with *what* values for **each** record

Overall syntax:

```sql
INSERT INTO
	table_name (field1, field2, field3...)
VALUES
	(record1_v1, record1_v2, record1_v3...),
	(record2_v1, record2_v2, record2_v3...),
	(record3_v1, record3_v2, record3_v3...),
	...;
```

##### Note

The order of fields and values does not matter as long as
the fields and values must be consistent on number and on type.

The only instance where the fields bracket may be omitted is when **every** field is specified for a record:

```sql
INSERT INTO
	table_name
VALUES
	(value1, value2, value3... valueN); -- all N fields
```

For the sake of readability, it is strongly recommended to include the fields.

Default value (usually `NULL`) will be used for fields not specified a value.
Error will be thrown for those without a default value such as primary key.
