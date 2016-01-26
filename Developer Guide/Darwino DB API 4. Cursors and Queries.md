Darwino DB API
=======================


# Cursors and queries
Cursors facilitate the selection of document sets from the database, and the extraction of data from the selected documents. You specify what you want to extract, and then you process the result. When you process the result, there is only the current entry in memory; it doesn’t load everything into memory and iterate through that set. The cursor lets you step through the results one by one.

> Note: A cursor is forward only. You cannot browse the resultset backwards.
>  

A cursor consumes a database connection; thus, the connection has to be released when it's done. To avoid reliance on the intermittent garbage collector, Darwino provides a callback to the cursor. The cursor calls this cursor handler for every result. For example, when iterating through a result set, you call find(), passing a CursorHandler. The CursorHandler has one method, which is handle(), which handles the CursorEntry. The cursor is allocating the database connection, executing the SQL query, calling the CursorHandler for every result, and then closing and recycling the database connection.
 
 There are methods designed for dealing with the subset of documents that are represented in the collection. For example:
- deleteAllDocuments(int options) – Will delete all of the documents not by iterating through and deleting each one-by-one, but instead will generate a SQL query to do the job in one fell swoop.
- markAllRead(boolean read) and markAllRead(boolean read, String username) will mark the cursors documents as read, either by the current user of by a particular username.

## Query and extraction language
When searching for documents in a database, index, or cursor, there are many options available. For example, searches can be based on key, a range of keys, partialkey, parentid, unid, tags, and ftsearch. Since it is possible to force a unid's value in Darwino, using the unid as a key is a particularly efficient basis for searches.
> When we use the term "key", it means the unid when the cursor is querying a store, or the key when the cursor is querying an index. 

- Search document by key 
        public Cursor key(Object key) throws JsonException; 
- Search document using a partial key, meaning document with a key starting with a specified string: 
        public Cursor partialKey(Object partialKey) throws JsonException; 
- Search a range of documents. When 'exclude' is not specified, then it means false (the document with the key is included). You typically specify a start and an end, although omitting the start means from the beginning, while omitting the end means up to and including the last document. 
        public Cursor startKey(Object startKey) throws JsonException; 
        public Cursor startKey(Object startKey, boolean excludeStart) throws JsonException; 
        public Cursor endKey(Object endKey) throws JsonException; 
        public Cursor endKey(Object endKey, boolean excludeEnd) throws JsonException; 
- Select by parent UNID 
        public Cursor parentUnid(String parentId) throws JsonException; 
        public Cursor parent(Document parent) throws JsonException; 
- Select by tag 
        public Cursor tags(String... tags) throws JsonException; 
- Select based on the doc id or the UNID 
        public Cursor id(int id) throws JsonException; 
        public Cursor unid(String unid) throws JsonException; 
- Select using a full text search expression 
        public Cursor ftSearch(String search) throws JsonException; 


These methods are fast and efficient, but may not always provide the search term flexibility required by an application. Darwino's query and extraction language allows for queries based on complex criteria.
- Select using the query language         
        public Cursor query(String query) throws JsonException; 

The JSON query language has three variants; which you use depends on where and how you want to use it:
1. Query documents. In this case, it's a query condition. The result of a query that is applied to a document is true or false. Does the document match the condition or not? Does field "State" equal "New York"? Is the field "Price" greater than $100? The query condition is applied to every document in the database. The cursor will select only the documents for which the condition is true. 
2. Data extraction. A document returned by a query is a piece of JSON. You may want to transform this JSON, for example if it contains a hundred fields and you're interested in only three of them. You don't want to download to a mobile client the entire document if most of it is not needed.
3. Calculating aggregation. When you have a cursor that is sorted by one or more keys, you can categorize based on the sorted values. The categorization groups the documents, and then you can calculate aggregate values for the groups. 

When a cursor runs, it calls the cursor handler with all of the cursor entries. In the cursor entry are the key and the value, accessible via getKey() and getValue(). What these two represent depends on the source of the cursor. A cursor executed on a store will have documents as its result, the key will be the unids of the documents, and the value will be the JSON of the documents. If, instead, the cursor was executed on an index, then the key will be the key of the index, and the value will be either the value that’s stored in the index or the JSON value from the corresponding documents, depending on an option applied to the cursor.

For details on the query language, see [Appendix 3. The Query Language](Appendix 3. The Query Language.html).


## Executing a query
A cursor is created at either the store or the database level. The store’s openCursor() method returns a cursor on which you then apply the selection condition. The Cursor methods return the cursor itself, which means that the methods can be stacked. For example:
```
Cursor c = store.openCursor().ftSearch(“version”).orderByFtRank().range(0,5)

```
This will perform a fulltext search on “version”, order the result by rank, and return the first five entries.

###Cursor Options
When executing a cursor, there are options available that control the behavior of the query.
- DATA_DOCUMENT: Instead of returning the value of the column, the query will return the document itself.
- DATA_CATONLY: Only the category rows will be extracted from the query, and not the ones related to documents. If no categories are defined for the query, then an error will be raised.
- DATA_NOVALUE: The 'value' of each row entry will be null, instead of containing a JSON document. This is an optimization that prevents the actual extraction being done when not needed. This is useful if, for example, you're only interested by the document meta-data (unid, ...) or the hierarchy.
- DATA_MODDATES: Returns the creation and last modification dates of every document matched.
- DATA_READMARK: Returns a flag for every matching document that indicates whether it has read by the user executing the query.
- DATA_WRITEACC: Returns a flag for every matching document indicating whether the user executing the query is authorized to edit the document.
- HIERARCHY_SQL: By default, Darwino will utilize the underlying database's ability, if available, to perform recursive queries (CTE, or Common Table Expressions). This option will disable this support, forcing the runtime to perform the queries exhaustively, in the case where the built-in recursive query feature is deemed unreliable or inconsistent in the particular database. This is a potentially expensive option.
- QUERY_NOSQL: The option disables Darwino's default behavior of generating optimized SQL queries, forcing the runtime to do the query manually. Like HIERARCHY_SQL, this is potentially a very expensive choice and should be used only when absolutely necessary.
- RANGE_ROOT: When a query is using skip and limit, this option will cause the query to consider only the root elements when determining the skip and limit values. By default, category and child entries are included when calculating the skip and the limit; this option overrides that default.
- TAGS_OR: By default, when querying by tag and specifying multiple tags, ALL of the tags must match for documents to be selected (the default is an "AND"); when TAGS_OR is specified, then ANY matching tag will allow documents to be selected.



###Document Hierarchies in Cursors
The query engine natively understands Darwino document hierarchies, and the cursor takes advantage of that by allowing cursor queries to return not only the matching root documents but also their associated child documents. In other words, a cursor query can test for values in the root documents, and then return the matching root documents along with their children, regardless of whether the children match the query condition or not. The CursorEntry objects returned by the cursor will have an indentLevel property (an int) that identifies where they lie in the hierarchy, with 0 indicating a root document.

By default, when you query documents you get back a flat list of documents; there is no hierarchy. If you want to include the children of the selected documents, you have to explicitly specify that you want their children as well, up to a given level. By default, the hierarchical level is zero. If you specify hierarchical(1), then you will get the queried documents plus their direct descendents. Specifying hierarchical(2) would return all of those, plus the grandchildren.

### The range() method
The range() method, given a number to skip and a number to return, will return a subset of a cursor’s entries, and can be controlled via the cursor options (by specifying RANGE_ROOT) to apply the skip and limit parameters only to the root documents, and to then return all associated child documents, without regard to the limit parameter.

### Cursor Sorting Options
orderBy(String…fields) sorts cursor results by field. This can be an extracted field (for example, @myfield) or a system field (such as _unid or cuser). If the RDBMS supports JSONQuery, then a JSON reference can be used, such as a JSON field name or a direct path to a JSON field. Optionally, the sort order can be specified by appending a space and the text “asc” or “desc” to each field/path value. For example:
.orderBy(“@state desc”, “unid”) will sort by state descending, then by unid ascending (that being the default).

The fulltext rank can also be used for specifying the order in a cursor, by use of the orderByFtRank() method. This is a short cut to .orderBy("_ftRank"). orderByFtRank() is always descending, with the best best match first.

The ascending() and descending() global options apply to the orderBy() method results, but are overriden by orderBy() when "asc" or "desc" is specified.

If no order is specified, and if there is no index, documents will appear in a cursor ordered by unid. When there IS an index, the default order is the index key.

###Browsing the Entries:
There are two ways to execute a cursor:
- Call find(), passing a CursorHandler. It will execute the query, returning the entries one-by-one and calling the CursorHandler for each.

```
	final StringBuilder b = new StringBuilder();
	c = store.openCursor().range(0,10);
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


There are equivalent methods, different in that they extract the document object, allowing it to be updated and saved back to the database. Extracting a whole document has an extra cost compared to just loading the cursor entries (but it is still faster than the dual steps of getting the entry and then loading the document).

The count() method will return the number of entries in the cursor. Behind the scenes, Darwino executes a SELECT count(*) on the table, which can be costly on large data sets.

The countWithLimit() method behaves similarly, but uses the limit parameter. If the actual count is greater than the specified limit, then the limit is returned.

### Categorization and Aggregation
Categorization is a means to group documents, and, secondarily, to calculate or aggregate on the groups. Categorization organizes documents in groups based on shared key values, determined by the orderBy() of a cursor, in the Darwino API. Categorization is completely dynamic; being based on the sort order, which is not fixed, categorization can be calculated on the fly.

There can be up to as many categories as there are sorted fields. If you have four levels in the sorting, then you can categorize on the first, or the first and second, or the first three, and so on. When categorizing, it isn't required that you start with the first sorted field; you can start with a subsequent sorted field instead. However, once you've started categorizing on multiple fields you cannot skip a sorted field and continue. That is to say, it is possible to categorize on the second, third, and fourth sorted fields, but not on just the second and fourth.

To calculate aggregate data, such as average, minimum, and maximum, pass an aggregate query to the cursor. For example:
```
Cursor c = store.openCursor()
	.query("{Count {$count: "@manufacturer”}, Sum: {$sum: “@released”},
    Avg: {$avg: “@released”}, Min: {$min: “@released”}, Max: {$max: “@released”}});
```

In this example, we count how many times the field "@manufacturer" is not null for all of the documents belonging to each category and we return the result as "Count". For "Sum", we calculate the sum of the field "@released" for every document in the category, and return the result as "Sum". We also calculate the average of the "@released" field as "Avg", and we select and return the minimum and maximum values for that field as well.

Categorization adds entries to the result to define the categories. If an entry has a "category" value of true, then it is not a document... it is a category entry. The "categoryCount" value, a number, contains the level of categorization for the entry in a multi-category result: top level would be categoryLevel of 1, the next level down would be categoryLevel of 2, and so on.

The .categories(int nCat) method takes as its parameter the number of category levels to apply, based on the orderBy() fields.

When categorizing, it is possible to request that the documents not be extracted in the cursor; in this case, the result will consist only of the category entries.

Another option is to extract categories while skipping the highest level categories. For example, extract two categories, but start at the second-level category, returning levels two and three.



## Optimizing queries
The query language is robust, with a lot of operators, and is optimized for the underlying database system. It will attempt to use only native database functions when constructing its SQL; if necessary functions are not supported by the database, it will still use those that ARE supported for parts of the query. For example, if there is an “AND” in the query, and only one condition is directly supported by the database, the query language will execute that part of the query first and only then iterate through the results one-by-one.

The query language’s API allows the cursor to be checked to determine whether a particular query is supported by the database. It compiles the query and answers whether it can generate SQL for the query, or it can generate only partial SQL, or it cannot generate SQL at all.
