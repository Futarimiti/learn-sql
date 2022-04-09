# Database normalisation

Database normalisation is the process of structuring a database, usually a relational database,
in accordance with a series of normal forms in order to reduce data redundancy and improve data integrity.

There are a series of normal forms, and here we'll forcus on
the first three of them.

### First normal form (1NF)

1NF states that:

*   A primary key must be present in the table
*   Every field is atomic
    *   i.e. can be separated into multiple fields

### Second normal form (2NF)

2NF incorporates 1NF, with an additional requirement that
all non-pk fields must be **fully** functionally dependent on
the primary key.

A table that partly depends on a primary key does not satisfy 2NF
and would lead to data redundancy.

To satisfy 2NF, a table is usuaslly expanded to multiple tables
to atomicise primary key, and build their relationship on another
table which uses foreign keys from these tables.

### Third normal form (3NF)

3NF incorporates 2NF, with an additional requirement that
all non-pk field must be **solely** depedent on the primary key
and not depedent on any other non-pk fields.

### One-to-one relations

When separating a table into multiple tables on one-to-one relation,
a **unique foreign key** is frequently used to connect two tables.

### The customer is always right

A non-redundant table design is possible, but may not be
the best choice in terms of query efficiency as joining becomes more frequent.

In real-life develepment, always design the table
satisfying customer's demand;
compromises on redundancy for speed of query are frequent.
