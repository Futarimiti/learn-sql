# Importing data

A data file should ending in `.sql` extension.

You should be `USE`ing a database before importing data to it.

Then, use `SOURCE` statement with a path to the file:

```sql
SOURCE path/to/data/file.sql
```

Do not include special characters or space in the path.

Do not wrap up path in quotation marks.

Do not append a semicolon at the end of statement.
