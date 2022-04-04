# Database administration (DBA)

DBA is the function of managing and maintaining DBMS
with (not limited to) the following responsibilities:

*   Installing, upgrading and configuring database
*   Maintain database security
    *   Involving users, roles, privileges assignment and revoking
        ...

Most instructions can be found in the bootstrap files;
we will focus on importing and exporting databases backups below.

### Backing up

To export a backup for a database,
use terminal utility `mysqldump`:

```sh
mysqldump db_name -u username -p 
```

With sufficient privilege, the database backup will be
sent to stdout;
to write to a file, do:

```sh
mysqldump db_name >> path/to/output/file.sql -u username -p
```

To import a database backup, use `SOURCE` command inside
that database:

```sql
USE database_name;
SOURCE path/to/backup.sql;
```
