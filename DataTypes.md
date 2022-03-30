# Data types in MySQL

MySQL holds miscellaneous data types where we only need to master the common ones:

| Type | Meaning |
| :------------- | :------------- |
| `char` | Fixed-length string |
| `varchar` | Dynamic-length string |
| `int` | |
| `bigint` | Equivalent to `long` in java |
| `float` ||
| `double` ||
| `date` | short date |
| `datetime` | long date |
| `clob` | 'Character large object', stores large text up to 4 GB |
| `blob` | 'Binary large object', stores huge binary data |

### `varchar` and `char`

Both types stores not-so-long strings with length at most 225 characters.
Their difference lies in different memory allocation.

`varchar` is a string with dynamic memory allocation based on string lengths
that minimises memory waste; could be relatively less efficient considering the time of allocation.

Meanwhile `char` is a string with specified, fixed memory allocation, regardless of string lengths.
This may cause memory waste during use; however this also brings greater efficiency
as there is no need to decide on memory allocation.

Decision on `char` or `varchar` is context-dependent.
It would be more suitable to use `char` for string fields with fixed length
such as ID number, or short-handed aliases such as using `m` and `f` for binary gender.
For fields with variable-length string such as name, `varchar` would be preferable.

### Length

Length can be specified with a type.

For `varchar` and `char`, one unit length is one character;
"One character" also applies those non-ASCII symbols.

### `date` and `datetime`

While `date` only includes Y-m-d information, `datetime` also includes h-m-s.
This is helpful for log timestamps, modification time, etc.;
For the others such as birthday, `date` is sufficient enough.
