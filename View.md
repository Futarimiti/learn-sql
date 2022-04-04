# View

Views are like *viewing* data in different angles,
by focusing, simplifying and customising the perception
to a table.

### Usage

A view is like a simplified subquery of a table;
different from creating a table as a copy of another,
updating records on a view will affect the original table.

#### Why don't I just use the original table?

Views can provide advantages over tables,
such as hiding complexity, limiting exposure
and simplifying code.
See this [Stack Overflow question](https://stackoverflow.com/questions/1278521/why-do-you-create-a-view-in-a-database)
for more.

### Syntax

#### Create & delete views

Use `CREATE VIEW` statement to create a view from a query:

```sql
CREATE VIEW
	view_name
AS
	some_subqueries (DQL);
```

Use `DROP VIEW` statement to delete a view:

```sql
DROP VIEW view_name;
```

### CRUD

A view can be queried or updated as like on a table.
