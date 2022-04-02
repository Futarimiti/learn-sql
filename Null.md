# `NULL` handling

`NULL` is "a special value, or mark, that is used to **indicate the absence of any data value**." (ANSI-92)

### Fair use of `NULL`

In SQL, `NULL` is used as a sentinel value to represent missing data,
where **an actual value exists but is not currently recorded** in the database.
Such `NULL`s should be updated with proper information as soon as possible.

DO NOT use `NULL` in place of zeroes, zero-length strings or other *known* blank values;
use it for unknown/missing information only.

### In comparative operations

Explicit comparisons with `NULL` always result in `UNKNOWN`, an intermediate truth value
(see [Three-valued logic](#Three-valued_logic_\(3VL\))).
For example:

```sql
mysql> SELECT 1 = NULL, 3 > NULL, NULL <> 10, NULL = NULL, NULL <> NULL;
+----------+----------+------------+-------------+--------------+
| 1 = NULL | 3 > NULL | NULL <> 10 | NULL = NULL | NULL <> NULL |
+----------+----------+------------+-------------+--------------+
| <null>   | <null>   | <null>     | <null>      | <null>       |
+----------+----------+------------+-------------+--------------+
```

To check if a value is `NULL`, use postfix operater `IS NULL`.

### Three-valued logic (3VL)

3VL is an logic system implemented by SQL to handle logic operations involving `NULL`.

3VL uses 3 truth values: `TRUE`, `FALSE` plus an *indeterminate* (`UNKNOWN` in SQL).

A truth table for 3VL:
![truth\_table](https://i.stack.imgur.com/h0Wvg.png)

#### `IN` & `NOT IN`

Using `NULL` in together with `IN` or `NOT IN` can produce unexpected results.

The question and the solution comes from
[this Stack overflow page](https://stackoverflow.com/questions/129077/null-values-inside-not-in-clause).

As you can see, `IN` and `NOT IN` are aliased grouped equality/non-equality operations such that:

*   `A IN (B, C, D)` = `A = B OR A = C OR A = D`
*   `A NOT IN (B, C, D) = A <> B AND A <> C AND A <> D`

The `OR` and `AND` difference leads to a surprising effect,
where statement A returns a result but B doesn't in the following code block:

```sql
A: SELECT 'TRUE' WHERE 3 IN (1, 2, 3, NULL)
B: SELECT 'TRUE' WHERE 3 NOT IN (1, 2, NULL)
```

Where the `WHERE` statement for A is equivalent to:

```sql
3 = 1 OR 3 = 2 OR 3 = 3 OR 3 = NULL
= FALSE OR FALSE OR TRUE OR UNKNOWN
= TRUE
```

Hence statement A returns a result.

Meanwhile, evaluating statement B gives:

```sql
3 <> 1 AND 3 <> 2 AND 3 <> NULL
= FALSE AND FALSE AND UNKNOWN
= UNKNOWN
```

In a `WHERE` clause, `UNKNOWN` is treated as not-`TRUE` and hence nothing is returned.

This may be common in some instances;
to solve this, uses function `IFNULL(field, replacement)` to replace a value for `NULL`.
