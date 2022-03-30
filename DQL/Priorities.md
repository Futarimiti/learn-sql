# Statement priorities

Suppose a DQL statement with every possible clauses,
the keywords should written in the order of:

```sql
SELECT
    ...
FROM
    ...
WHERE
    ...
GROUP BY
    ...
HAVING
    ...
ORDER BY
    ...
```

Its executing priority will be
`FROM -> WHERE -> GROUP BY -> HAVING -> SELECT -> ORDER BY`;
`ORDER BY` is always executed at last.
