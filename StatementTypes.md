# SQL statements types

SQL statements are categorised into 5 types:

### DQL - Data query language

Makes queries on the data.

All statements starting with `SELECT` are DQL, such as:

```sql
SELECT `ENAME` AS 'NAME', `SAL` * 12 AS 'ANNUAL SALARY' FROM `EMP`;
```

Note that DQL does not modify the data itself;
`` `SAL` * 12`` here only format the output.

### DML - Data manipulating language

Adds, deletes or changes data.

| add | delete | change |
| -- | -- | -- |
| INSERT | DELETE | UPDATE |

### DDL - Data description language

Manipulates table structures.

All statements with `CREATE`, `DROP` or `ALTER` are DDL.

Note that DDL changes table structure, not individual data.

### TCL - Transaction control language

Will be introduced later.

Includes `COMMIT` and `ROLLBACK` statements.

### DCL - Data control language

Includes granting and revoking authorisation with `GRANT` and `REVOKE`.
