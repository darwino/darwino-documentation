Darwino DB API
=======================


# Cursors and queries
Cursors facilitate the selection of document sets from the database, and the extraction of data from the selected documents. You specify what you want to extract, and then you process the result. When you process the result, there is only the current document in memory; it doesn’t load everything into memory and iterate through that set. The cursor lets you step through the results one by one. The exception is when it is done through HTTP. Because HTTP is stateless, results are paged and multiple documents are loaded at one time. When it’s local, though, it is able to deal directly with database cursors. This is for performance reasons. 
 
 A cursor consumes a database connection; thus, it has to be recycled when it’s done. To avoid the recycle mess that plagues Domino developers, and to avoid reliance on the intermittent garbage collector, Darwino provides a callback to the cursor. The Cursor calls this cursor handler for every result. For example, when iterating through a result set, you call find(), passing a CursorHandler. The CursorHandler has one method, which is handle(), which handles the CursorEntry. The cursor is allocating the database connection, executing the SQL query, calling the CursorHandler for every result, and then closing and recycling the database connection.
 
 Domino developers can think of a cursor as being like a ViewEntryCollection, with a set of methods for dealing with the subset of documents that are represented in the collection. For example:
- deleteAllDocuments(int options) – Will delete all of the documents not by iterating through and deleting each one-by-one, but instead will generate a SQL query to do the job in one fell swoop.
- markAllRead(Boolean read) and markAllRead(Boolean read, String username) will mark the cursors documents as read, either by the current user of by a particular username.


## Query and extraction language
To calculate aggregate data, such as average, minimum, and maximum, pass an aggregate query to the cursor. For example:
{ Count {$count: @manufacturer”}, Sum: {$sum: “@released”}, Avg: {$avg: “@released”}, Min: {$min: “@released”}, Max: {$max: “@released”}}

When a cursor runs, it calls the cursor handler with all of the cursor entries. In the cursor entry are the key and the value, accessible via getKey() and getValue(). What these two represent depends on the source of the cursor. A cursor executed on a store will have documents as its result, the key will be the unids of the documents, and the value will be the JSON of the documents. If, instead, the cursor was executed on an index, then the key will be the key of the index, and the value will be either the value that’s stored in the index or the JSON value from the corresponding documents, depending on an option applied to the cursor.

(Must provide examples of query options. Where is this best documented?)


## Executing a query
A cursor is created at either the store or the database level. The store’s openCursor() method returns a cursor on which you then apply the selection condition. The Cursor methods return the cursor itself, which means that the methods can be stacked. For example:
Cursor c = index.openCursor().ftSearch(“version”).orderByFtRank().range(0,5)

This will perform a fulltext search on “version”, order the result by rank, and return the first five entries.

###Cursor Options
It is possible to query by tags. This is done by passing a list of tags and specifying via the TAGS_OR option whether to perform an “and” or an “or” with the tags.
DATA_READMARK adds a flag to every cursor entry indicating whether the entries have been read by the user executing the query.
DATA_WRITEACC adds a flag indicating whether the current user can edit the documents.



###Hierarchies in Cursors
The query engine natively understands Darwino document hierarchies, and the Cursor takes advantage of that by allowing cursor queries to apply to only root documents and not the associated response documents. In other words, a cursor query can test for values in the root documents, and then return the matching root documents along with their responses, regardless of whether the responses match the query condition or not. The CursorEntry objects returned by the cursor will have an indentLevel property (an int) that identifies where they lie in the hierarchy, with 0 indicating a root document.

The range() method which, given a number to skip and a number to return, will return a subset of a curser’s entries, can be controlled via the Cursor options (by specifying RANGE_ROOT) to apply the skip and limit parameters only to the root documents, and to then return all associated response documents, without regard to the limit parameter.

### Cursor Sorting Options
orderBy(String…fields) sorts cursor results by field. This can be an extracted field or a system field (for example, _unid or cuser). If the RDBMS supports JSONQuery, then a JSON reference can be used can be used, such as a JSON field name or a direct path to a JSON field. Optionally, the sort order can be specified by appending a space and the text “asc” or “desc” to each field/path value. For example:
.orderBy(“@state desc”, “unid”) will sort by state descending, then by unid ascending (that being the default).

The fulltext rank can also be used for specifying the order in a curser. The orderByFtRank() method.

The ascending() and descending() methods can be applied to both the orderBy() and the orderByFtRank() method results.

### Categorization
In a cursor, categorization is based on the sort order. If no order is specified, and if there is no index, the unid is used. Categorization is completely dynamic, being based on the sort order which is not fixed.

The .categories(int nCat) method takes as its parameter the number of category levels to apply.

It is also possible to request that the documents not be extracted in the cursor; in this case; the result will consist only of the categories.

Another option is to extract categories while skipping the highest level categories. For example, extract two categories, but start at the second-level category, returning levels two and three.

Browsing the entries:
There are two ways to execute a cursor:
- Call find(), passing a Cursorhandler. It will execute the query, returning the entries one-by-one and calling the Cursorhandler for each.
- Call findOne(), if you are sure there will be only one entry returned, or if you are interested only in the first. There is no need to pass the callback Cursorhandler.

The count() method will return the number of entries in the cursor.

## Optimizing queries
The query language is robust, with a lot of operators, and is optimized for the underlying database system. It will attempt to use only native database functions when constructing its SQL; if necessary functions not supported by the database, it will still use those that ARE supported for parts of the query. For example, if there is an “AND” in the query, and only one condition is directly supported by the database, the query language will execute that part of the query first and only then iterate through the results one-by-one.

The query language’s API makes it so the cursor can be queried to determine whether a particular query is supported by the database. It compiles the query and answers whether it can generate SQL for the query, or it can generate partial SQL, or it cannot generate SQL.

(Question: The data extraction language is important. Is there documentation available that would help me do it justice here?)
