Darwino DB API
=======================


# Cursors and queries
Cursors facilitate the selection of document sets from the database, and the extraction of data from the selected documents. You specify what you want to extract, and then you process the result. When you process the result, there is only the current entry in memory; it doesn’t load everything into memory and iterate through that set. The cursor lets you step through the results one by one.

> Note: A cursor is forward only. You cannot browse the resultset backwards.
>  

A cursor consumes a database connection; thus, the connection has to be released when it's done. To avoid reliance on the intermittent garbage collector, Darwino provides a callback to the cursor. The Cursor calls this cursor handler for every result. For example, when iterating through a result set, you call find(), passing a CursorHandler. The CursorHandler has one method, which is handle(), which handles the CursorEntry. The cursor is allocating the database connection, executing the SQL query, calling the CursorHandler for every result, and then closing and recycling the database connection.
 
 There are methods designed for dealing with the subset of documents that are represented in the collection. For example:
- deleteAllDocuments(int options) – Will delete all of the documents not by iterating through and deleting each one-by-one, but instead will generate a SQL query to do the job in one fell swoop.
- markAllRead(boolean read) and markAllRead(boolean read, String username) will mark the cursors documents as read, either by the current user of by a particular username.

## Query and extraction language
When searching for documents in a database, index, or cursor, there are many options available. For example, searches can be based on key, a range of keys, partialkey, parentid, unid, tags, and ftsearch. Since it is possible to force a unid's value in Darwino, using the unid as a key is a particularly efficient basis for searches.

These methods are fast and efficient, but may not always provide the search term flexibility required by an application. Darwino's query and extraction language allows for queries based on complex criteria.

The JSON query language has three variants; which you use depends on where and how you want to use it:
1. Query documents. In this case, it's a query condition. The result of a query that is applied to a document is true or false. Does the document match the condition or not? Does field "State" equal "New York"? Is the field "Price" greater than $100? The query condition is applied to every document in the database. The cursor will select only the documents for which the condition is true. 
2. Data extraction. A document returned by a query is a piece of JSON. You may want to transform this JSON, for example if it contains a hundred fields and you're interested in only three of them. You don't want to download to a mobile client the entire document if most of it is not needed.
3. Calculating aggregation. When you have a cursor that is sorted by one or more keys, you can categorize based on the sorted values. The categorization groups the documents, and then you can calculate aggregate values for the groups. 

When a cursor runs, it calls the cursor handler with all of the cursor entries. In the cursor entry are the key and the value, accessible via getKey() and getValue(). What these two represent depends on the source of the cursor. A cursor executed on a store will have documents as its result, the key will be the unids of the documents, and the value will be the JSON of the documents. If, instead, the cursor was executed on an index, then the key will be the key of the index, and the value will be either the value that’s stored in the index or the JSON value from the corresponding documents, depending on an option applied to the cursor.

For details on the query language, see [Appendix 3. The Query Language](Appendix 3. The Query Language.md).


## Executing a query
A cursor is created at either the store or the database level. The store’s openCursor() method returns a cursor on which you then apply the selection condition. The Cursor methods return the cursor itself, which means that the methods can be stacked. For example:
```
Cursor c = store.openCursor().ftSearch(“version”).orderByFtRank().range(0,5)

```
This will perform a fulltext search on “version”, order the result by rank, and return the first five entries.

###Cursor Options
It is possible to query by tags. This is done by passing a list of tags and specifying via the TAGS_OR option whether to perform an “and” or an “or” with the tags.
DATA_READMARK adds a flag to every cursor entry indicating whether the entries have been read by the user executing the query.
DATA_WRITEACC adds a flag indicating whether the current user can edit the documents.



###Hierarchies in Cursors
The query engine natively understands Darwino document hierarchies, and the Cursor takes advantage of that by allowing cursor queries to return not only the matching root documents but also their associated response documents. In other words, a cursor query can test for values in the root documents, and then return the matching root documents along with their responses, regardless of whether the responses match the query condition or not. The CursorEntry objects returned by the cursor will have an indentLevel property (an int) that identifies where they lie in the hierarchy, with 0 indicating a root document.

By default, when you query documents you get back a flat list of documents; there is no hierarchy. If you want to include the children of the selected documents, you have to explicitely specify that you want their children as well, up to a given level. By default, the hierarchical level is zero. If you specify hierarchical(1), then you will get the queried documents plus their direct descendents. SPecifying hierarchical(2) would return all of those, plus the grandchildren.

### The range() method
The range() method, given a number to skip and a number to return, will return a subset of a curser’s entries, can be controlled via the Cursor options (by specifying RANGE_ROOT) to apply the skip and limit parameters only to the root documents, and to then return all associated response documents, without regard to the limit parameter.

### Cursor Sorting Options
orderBy(String…fields) sorts cursor results by field. This can be an extracted field (for example, @myfield) or a system field (such as _unid or cuser). If the RDBMS supports JSONQuery, then a JSON reference can be used can be used, such as a JSON field name or a direct path to a JSON field. Optionally, the sort order can be specified by appending a space and the text “asc” or “desc” to each field/path value. For example:
.orderBy(“@state desc”, “unid”) will sort by state descending, then by unid ascending (that being the default).

The fulltext rank can also be used for specifying the order in a cursor, by use of the orderByFtRank() method. This is a short cut to .orderBy("_ftRank"). orderByFtRank() is always descending, with the best best match first.

The ascending() and descending() global options apply to the orderBy() method results, but are overriden by orderBy() when "asc" or "desc" is specified.

If no order is specified, and if there is no index, documents will appear in a cursor ordered by unid. WHen there IS an index, the default order is the index key.

###Browsing the entries:
There are two ways to execute a cursor:
- Call find(), passing a CursorHandler. It will execute the query, returning the entries one-by-one and calling the CursorHandler for each.

```
	final StringBuilder b = new StringBuilder();
	c = store.openCursor().range(0,10);
	b.setLength(0);
	c.find(new CursorHandler() {
	  public boolean handle(CursorEntry e) throws JsonException {  
	    b.append("  "+e.getString("firstName")+" "+e.getString("lastName")+"\n");
	    return true;
	  }
	});
```

- Call findOne(), if you are sure there will be only one entry returned, or if you are interested only in the first. There is no need to pass the callback Cursorhandler.

```
	c = store.openCursor();
	CursorEntry e = c.findOne();

```


There are equivalent methods, different in that they extract the document object, allowing it to be updated and saved back to the database. Extracting a whole document has an extra cost compared to just loading the cursor entries.

The count() method will return the number of entries in the cursor. Behind the scenes, Darwino executes a SELECT count(*) on the table, which can be costly on large data sets.

### Categorization and Aggregation
Categorization is a means to group documents, and, secondarily, to calculate or aggregate on the groups.

Categorization is based on the ORDER BY clause that produced the result set. There can be up to as many categories as there are sorted fields in the ORDER BY. If you have four levels in the sorting, then you can categorize on the first, or the first and second, or the first three, and so on. When categorizing, it isn't required that you start with the first sorted field; you can start with a subsequent sorted field instead. However, once you've started categorizing on multiple fields you cannot skip a sorted field and continue. That is to say, it is possible to categorize on the second, third, and fourth sorted fields, but not on just the second and fourth.

To calculate aggregate data, such as average, minimum, and maximum, pass an aggregate query to the cursor. For example:
```
{Count {$count: "@manufacturer”}, Sum: {$sum: “@released”}, Avg: {$avg: “@released”}, Min: {$min: “@released”}, Max: {$max: “@released”}}
```

In this example, we count how many times the field "@manufacturer" is not null for all of the documents belonging to each category and we return the result as "Count". For "Sum", we calculate the sum of the field "@released" for every document in the category, and return the result as "Sum". We also calculate the average of the "@released" field as "Avg", and we select and return the minimum and maximum values for that field as well.

See [Appendix 3. The Query Language](Appendix 3. The Query Language.md) for details on categorization and aggregation in Darwino.

## Optimizing queries
The query language is robust, with a lot of operators, and is optimized for the underlying database system. It will attempt to use only native database functions when constructing its SQL; if necessary functions not supported by the database, it will still use those that ARE supported for parts of the query. For example, if there is an “AND” in the query, and only one condition is directly supported by the database, the query language will execute that part of the query first and only then iterate through the results one-by-one.

The query language’s API allows the cursor to be checked to determine whether a particular query is supported by the database. It compiles the query and answers whether it can generate SQL for the query, or it can generate only partial SQL, or it cannot generate SQL at all.
