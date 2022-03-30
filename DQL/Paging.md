# Paging

With the help of `LIMIT`, a long query result can be displayed in pages.

Say we have a very long query and we'd like to display this result in pages
with 5 results per page.

That is saying, for each page, these queries will be displayed:

    Page 1 		query 0-4
    Page 2 		query 5-9
    Page 3 		query 10-14
    ...

Hence for different pages, we make a query with the `LIMIT` clause being:

    Page 1 		LIMIT 0, 5
    Page 2 		LIMIT 5, 5
    Page 3 		LIMIT 10, 5
    ...

Where the second parameter for `LIMIT`, the length of query, is fixed as 5.

The first parameter, starting index, can be deduced given page number `n`:

    startIndex = (n - 1) * 5

Where we can change 5 to a variable.
If we'd like to display `pageSize` queries per page, then:

    startIndex = (n - 1) * pageSize

This is basically it. We can apply this in java to query in pages, given `pageNo` and `pageSize`:

```java
void queryInPages(int pageNo , int pageSize)
{
	int startIndex = getStartIndex(pageNo , pageSize);

	// concatenate query request
	String req = String.format("SELECT ... LIMIT %d, %d" , startIndex , pageSize);

	...
}

private static int getStartIndex(int pageNo , int pageSize)
{
	return (pageNo - 1) * pageSize;
}
```
