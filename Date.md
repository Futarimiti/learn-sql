# Date

### Formatting date

MySQL provide scalar function `DATE_FORMAT` to format `date` from `varchar` type;
reversely `STR_TO_DATE` converts a `date` type to `varchar` with pattern specified.

A date pattern string uses %-specifiers to represent a date, including:

| Format specifier | Representation |
| -- | -- |
| `%Y` | Year |
| `%m` | Month |
| `%d` | Day |
| `%h` | Hour |
| `%i` | Minute |
| `%s` | Second |

The default `date` format pattern in MySQL is `%Y-%m-%d`;
for `datetime`, the default display pattern is `%Y-%m-%d %h:%i:%s`.

##### `DATE_FORMAT`

`DATE_FORMAT` formats a date as a string; usually used when presenting a date from query.

`DATE_FORMAT` takes 2 parameters:

*   Date
*   Desired date pattern

Example use:

```sql
DATE_FORMAT(birthday , '%d-%m-%Y')
```

When unused, the date will be displayed in form of '%Y-%m-%d' as the default display style for MySQL.

##### `STR_TO_DATE`

`STR_TO_DATE` takes 2 parameters:

*   String of date `s`
*   Format pattern of `s`

Example use:

```sql
STR_TO_DATE('01-10-1990' , '%d-%m-%Y')
```

This function may be omitted if the date string is in format of '%Y-%m-%d'.

### Current time

Built-in function `NOW` returns the current system time in `datetime`.
It is applicable to `date` as well.
