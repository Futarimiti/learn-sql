# Scalar functions

Scalar function gets its name as it manages one field per time;
it takes in one record and returns one output; given `n` inputs, there will be `n` outputs,
as the function goes on to the next only after the current record is managed.

The opposite of scalar function is aggregate function, which takes in fields from multiple records.

### String

| function | usage |
|--|--|
| `LOWER` | convert to lowercase |
| `UPPER` | convert to uppercase |
| `SUBSTR(string , start , length)` or <br> `SUBSTR(string FROM start FOR length)` | substring |
| `LENGTH` | get length |
| `TRIM` | trim blank characters |
| `STR_TO_DATE` | convert to date |
| `CONCAT` | concatenate |

When using `SUBSTR`, note that strings in MySQL are 1-based.

Example: capitalise first letter of every name and lowercase the rest:

```sql
SELECT
    -- capitalise first char, lowercase rest chars, concat
    CONCAT(
        UPPER(
            SUBSTR(`ENAME` , 1 , 1)
        ),
        LOWER(
            SUBSTR(`ENAME` , 2 , 
                LENGTH(`ENAME`) - 1
            )
        )
    ) AS 'NAME'
FROM
    `EMP`;
```

    +--------+
    | NAME   |
    +--------+
    | Smith  |
    | Allen  |
    | Ward   |
    | Jones  |
    | Martin |
    | Blake  |
    | Clark  |
    | Scott  |
    | King   |
    | Turner |
    | Adams  |
    | James  |
    | Ford   |
    | Miller |
    +--------+

### Date

| function | usage |
|--|--|
| `DATE_FORMAT` | format date to string |

### Numeral

| function | usage |
|-|-|
| `FORMAT` | format numerals |
| `ROUND(num , decimals = 0)` | round |
| `RAND` | random float in 0-1 |

Second optional parameter (default 0) of `ROUND` gives precision of the rounded number:

```sql
SELECT
    ROUND(1234.567 , 2) AS 'PREC 2',
    ROUND(1234.567 , 1) AS 'PREC 1',
    ROUND(1234.567 , 0) AS 'PREC 0',
    ROUND(1234.567 , -1) AS 'PREC -1',
    ROUND(1234.567 , -2) AS 'PREC -2';
```

    +---------+--------+--------+---------+---------+
    | PREC 2  | PREC 1 | PREC 0 | PREC -1 | PREC -2 |
    +---------+--------+--------+---------+---------+
    | 1234.57 | 1234.6 | 1235   | 1230    | 1200    |
    +---------+--------+--------+---------+---------+

### `NULL`

| function | usage |
|--|--|
|`IFNULL(field , replace)`| replace `NULL` with concrete value |

Arithmetic calculations involving `NULL` always yields `NULL`;
to change this, `IFNULL` may be used to convert it to a definite value.

```sql
SELECT
    `ENAME` AS 'NAME',
    (`SAL` + IFNULL(`COMM` , 0)) * 12 AS 'ANNUAL INCOME'
FROM
    EMP;
```
