Darwino DB API
=======================

## 1	Concepts
The Darwino database is a NoSQL store for storing JSON documents and related attachments. It is actually a database of JSON documents, and you can attach binary pieces to any document. 
 
 Built on top of existing relational databases, it provides the benefits of a mature, well-established technology. Darwino takes advantage of the latest NoSQL additions, such as native JSON and data sharding. 
 
 Another benefit of using relational databases as the groundwork is that you can use any existing tool that can connect to a relational database to access the data.  You can use BI tools, reporting tools, or other big-data analysis tools, like IBM Watson, and they will directly understand the database format. 
 
 One of the key points of the JSON store is that there is a single implementation that works everywhere: servers (Windows, Linux, OS X, etc…), and mobile devices. The same code runs in both environments. Moreover, these disparate environments can replicate data. 

###Server
   Even on a mobile device, you have the Server. The Server is, in effect, the database itself; the Server encapsulates the connection to a physical RDBMS.  It points to your DB2, your SQL Server, your PostgreSQL, or to your SQL Lite. When you connect to an RDBMS on the server, you will specify the JDBC path and user password that the runtime can use to access the data. 
### Session
   When you want to do this through a Darwino server, you have to do it through a Session object, and a Session object is related to a particular user.  The reason for the Session object is to allow different users to share the same server. The app itself will use one database server but multiple Sessions. The Session, created from the user’s ID, is what will be used by the runtime to apply security to the data. This is an important concept because the server connects to the physical relational database with one single user and password, which means that this user and password can access all of the data. The security is then applied by the Darwino runtime through the Session object.   
### Database
   Then comes the organization of the data.  The data are organized into databases; but we should not confuse that with the relational database file.  Here, it’s a document database.  A database is a set of documents with common characteristics. 
### Stores
The store is a container for JSON documents.  This notion doesn’t exist in IBM Domino; in Domino, you have the NSF, and the NSF is a container for Notes documents. In Darwino, you have one extra level: The database is a set of stores, and each store is a container for documents. That allows you to put documents into different “buckets” that have different characteristics, such as different indexing strategies. For example, in a CRM application, you may have one document that is a Customer, and another that is a Product. You would not want to apply the same indexes to these documents.  You can specify further customization for a store, such as whether full-text search is enabled.

Pre-defined stores
In the database definition are four pre-defined stores. These stores are created automatically by Darwino and can be utilized as-is, but they can also have their definitions overridden and customized (by adding indexes or fields, for example). It is always possible to create specific stores for these stores’ purposes; these pre-defined stores exist as a convenience.
- _default: This store can act as a placeholder. There is no index or field extraction for this store by default, but it can be used for quick-and-dirty storage of data.
- _local: This store is identical to the _default store, except that it is never replicated. It is useful for, among other things, storing device-specific data on a mobile device.
- _comments: The social data Comments are stored here by default. Storing comments here instead of inside the documents themselves avoids having the documents marked as modified every time a user adds a comment. It also avoids replication conflicts on the documents when multiple comments are added in a short period of time. Also, because comments can be complex, even including attachments, storing them here avoids making the documents overly complex.
- _design: Not currently implemented.



### Document
   Then, there is the document.  In Darwino, a document is four things:
-- JSON data. It’s not necessarily a JSON object; it could be a JSON array. Most of the time it will be a JSON object; making it a JSON object is a best practice, because doing so is necessary to allow use of features such as ?????? (inaudible, about 17minutes in.)Reader/writer fields, maybe?
-- metadata.: the UNID, creation date, last modification date, creator name, last modifier name. There are also several replication-related metadata items, but these are generally not of interest to the developer. These include the last replicated time and the sequence ID used in detecting replication conflicts. 
-- A potential set of binary attachments. The attachments consist of binary data identified by name, and with an associated MIME type so that consumers can know what to do with the data. A single Darwino document can have multiple attachments, but the attachment name is the key so there cannot be two attachments with the same name in a single document. (Question: what other data is stored regarding the attachment? Is there a date and size?)
-- A set of social data.  Darwino, by default, it social-enabled. You can rate a document, you can share a document, you can vote for a document, you can tag a document. The social data is ABOUT the document, but it is not stored IN the document. The social data is stored in a separate table, and it is user-based: if five users rate a document, there will be five records in the social table, one per user, all referencing that document. The table is replicated along with the documents.  There is one exception to this separation of the social data from the document’s JSON data: tags. Tags are stored in both the document and the social table. In the document, the tags are stored in the field called “_tags”. When the document is saved, the values are copied to the social table. (Question: How much should we discuss this, given that the API makes it invisible to the programmer? Also, is it just one table per store?) 
 
 Documents can be organized hierarchically. Every document can have a reference to a parent document. That reference is stored in the document’s “_parentid” field. This field is accessible for read/write through the API. 
 
 Database security
Darwino implements multi-level security. You can assign security to the Server object; you can control who can and cannot access the server. At the database level, you can assign an ACL. In the ACL, you can define who can access the database, manage the database, read documents, create documents, delete documents, and edit documents. At the Document level, you can maintain a list of users who can read or read/write the document.

###	UNID
   Documents have two identifiers: the UNID, which is a string provided either by the API when creating the document or, if it’s empty, then it is generated automatically by the system. When you create the document, you can pass the ID or allow it to be generated. A UNID must be unique per store. When there are two replicas, for example one on the server and one on a mobile device, the UNIDs will match. 
    
   (Aside: The parentID is used to identity the document’s parent. It is the UNID of the parent. ) 
### NoteID
   The NoteID is an integer that is unique inside the database. It is generated by the system; you cannot specify it when you create the document, and it cannot be changed. Being an integer makes it much more efficient in database operations than a string such as the UNID. 
    
   However, the NoteID is not universal; it won’t be the same in two different replicas. Because of this, you should not store it for programmatic use and try to rely on its always applying to that document. NoteIDs are used internally because in the database each document maps to a set of relational tables; the relations between these tables are expressed most of the time through the NoteID because it’s much more efficient than the UNID.

##  2	Defining and deploying the database
 A Darwino database is actually a set of tables within a relational database. The schema of these tables does not depend on the Darwino application. You can create tables, for example, at the application level–at the logical level–and the schema will always be the same. (Question: REALLY??? How does this work?) The schema if defined by Darwino. This is important because in some organizations altering tables’ schema is strictly controlled. They require that a new DDL be submitted for the administrator to apply. Darwino make sit so that no new table definitions are necessary… UNLESS you want to add additional indexes.

 The prefix of the table names is the name of the Darwino database. This restricts us to names that are compatible with the rules of the relational database system. The suffix is always an underscore followed by three characters. Let’s take a look (need screen shot):
 - _dsg: The Design table. This includes the lists of fields, stores, indexes, etc… This is “generally” only one record in this table (Question: why “generally”? Are there exceptions?), with the value “DATABASE” in its “type” column. The “name” is empty, and the “json” column contains the definition of the database. When you initialize a Darwino database, it will create this set of tables and store the database definition in that single record in the _dsg table.
In the database definition is a field called “version”. When you initialize the database, the version will be “1”. Every time the database definition changes, increment the number (Question: is the incrementing automatic?). This is important in Darwino because the application is disconnected from the database, so it is possible to have a database design that is not at the level expected by the application. When your code is opening the database, it will open this record from the _dsg table, extract the version value, and do a compare.
If it’s a match, it will open the database, return a handle, and off you go.
In another case, the application may be expecting to work with an higher version of the database. When opening, you specify how to update. One choice is to upgrade the database by running a function that you provide to give the new database definition.
If, on the other hand, the application is expecting a lower level of the database, it will fail. The application will not be able to update the database because it won’t know how.
In AppDatabaseDef.java, the loadDatabase method does the job of checking the version number and returning either null or a handle to the open database. 
If the runtime itself has been updated,  the “tableVersion” in the database definition come into play. It is not managed by the application; it is managed by the runtime. Domino developers can think of it as the ODS.
 - _doc: The document table 
	- docid – autogenerated key value. This is dependent on the database and on the instance in the database.
	instanceid – identifies the instance within the database that “contains” this document
	- storied – along with the docid and instanceid, define the unique primary key of a document.
	- unid – 
	- repid – 
	- pstoreid – a pointer to the store of the parent document
	- parent – a pointer to the parent document
	- smstoreid – pointer to the syncmaster store
	- smunid – pointer to the syncmaster document
	- seqid – don’t care
	- updid – internal flag for replication
	- udate – the last replication time of the document. This is updated automatically 
	- sftdel – soft delete flag, not currently implemented
	- cdate – creation date of the document
	- cuser – the user that created the document
	- mdate – the date of last modification
	- muser – the user that last modified the document
	- rsec – used internally to support reader fields
	- rsed – used internally to support writer fields
	- json – the JSON data of the document
	- sig – a signature for the document, not currently implemented
	- changes – used internally to support replication
	- cdatets – an easily readable and queryable copy of the creation date in timestamp format
	- mdatets – an easily readable and queryable copy of the modification date in timestamp format

	Note: the date fields are stored as integers. They are the Java date converted to a long. They represent the number of milliseconds since the 1/1/70. This is to accommodate the precision required for replication, and the requirement that the dates be completely compatible with all possible relational database systems.

 - _bin table: Used to store binary data associated with documents, but outside of the documents. It is used to support features similar to Domino’s DAOS, but storing the binary data still inside the database instead of in the file system. Here data is stored with a computed key based on the hash of the file’s contents, and can be shared between multiple documents pointing to the same bin record.

 - -dov table: This stores the list of fields extracted from documents. The extracted data is stored in one of four columns, one for each possible data type; they are named ftxt, fnum, fbol, and fdat.

 - _idx: This is where in indexes are stored.

 - _idv: Like the _dov table, but for the index level, because we can store field at the index level. (Question: This sounds interesting. I need some detail on it. Is this for speed of querying and simplicity of coding? How is it done?)

 - _lck: This is for document locking… not currently implemented.

 - _rep: Stores the replication information. It stores the last replication date for one replication profile. The last replication date is stored in the target of a replication. When pushing replication changes, the first step is to ask the target for the last replication time. The target checks this table and returns the value. The source then composes the list of changes and sends that. This is because you want to base the replication on the target’s clock. 

 - _sec: Stores the reader and writer information. For every document, it stores the entry name and whether it’s read-only or read/write.

 - _sed: Like the security table, but for ereaders and ewriters.

 - _stu: This is the deletion stub table, used during replication to convey that a document has been deleted.

 - _tag: Stores the social data tags. It is indexed by the docid, and there is one row for every tag.

 - _usr: The user-related social data, such as the rating and sharing information, as well as whether the document has been read and when it was last read. It also stores the replication time information for this data. 



### Database definition class
 (dbnameDatabaseDef.java)
- setACL() is used to set the access levels of people, groups, and roles (who can read, edit, create documents, etc…). These access rights can be resolved dynamically. In particular, they can be resolved for an Instance.
- setDocumentSecurity(int documentSecurity) determines how readers and writers filed will be handled. Choices include no reader/writer security, reader only, writer only, etc (need full list)
- setInstanceEnabled(boolean instanceEnabled) – are Instances allowed or not.
- setPreventRestAccess(Boolean preventRestAccess) – Darwino provides a set of REST services so that data can be read and written via REST services. Disabling REST services prevents raw REST access to document data, ensuring that all access be through the appropriate business logic. If this is disabled, then even if REST services are deployed, before serving the data Darwino will check this and deny access.
- setReplicaID - internal
- setReplicationEnabled(Boolean replicationEnabled) – If replication is enabled, Darwino records more data to support replication, including deletion stubs. This overhead can be avoided by disabling replication.
- setSoftDeleteEnabled – not implemented
- setTableVersion – internal
- setTimeZone – Darwino stores dates in ISO 8601 format, by default using GMT as the time zone. Setting this value will override that default. Dates will be stored instead using the specified time zone. There is never a loss of certainty; Darwino always stores the values with a time zone; this merely determines with zone is used as the default.

	Stores
	Physically, a store is nothing; it is just a concept. It is actually just a filter on the documents in a database. There is not one table per store; instead stores are implemented as a column value in each document. Every document in a database is identified by its UNID and its storied; together, these two fields define the document’s key. This way of implementing stores limits the actions needed to maintain the database’s DDL.
	Stores can have several options: 
	- setAnonymousSocial(Boolean anonymousSocial) – Enabling this allows tracking of the social activities of the anonymous user; for example, tracking when anonymous reads a document. This defaults to false, since there are few cases where it would be desired.
	- setBinaryStore(String binaryStore) – This is an extension point that determines how attachments in the store will be saved. By default, attachments are stored in the _att table, which has as its primary key the docid and the attachment name, and it has a column called “data” which is a byte array. Some database systems don’t perform well with large numbers of large byte arrays, so this setting exists to allow this default to be overridden, and a different binary store can be used, for example the file system. (In such a case, the binkey (binary key) column could be used to store the file system path that points to the attachment’s storage location.) Domino developers should recognize the similarity to DAOS in this example.
	- setFTSearch(_FtSearch ftSearch) – Specified which 3rd-party full text search engine to use in place of the default, which is the engine that is provided by the host database.
	
	- setFtSearchEnabled(Boolean fulltextEnabled) – Enables full text search of the documents in the store. Darwino uses the full text search engine provided by the host database. This results in maximum performance and low overhead.
It is possible to test, at run time, whether the database supports full text search, so the UI can be adjusted accordingly. (Question: how?)
None of the databases know, now, how to do full text search on the JSON documents. The _fts table contains the names and values of the fields that you wish to fulltext index.
	
	- setLabel(String label)  (Question: what’s setLabel() all about?)
	
	- setPreventRestAccess(Boolean preventRestAccess) – If REST access is enabled at the database level, it can be prevented at the store level.
	
	- setReadMarkEnabled(Boolean readMarkEnabled) – This applied to the social data read marks, and is set to false by default.  When enabled, theh when a document is read by a user who is not anonymous (unless setAnonymousSocial is enabled) reads a document, that document is marked as read by that user. There is an option when doing a query that tells the results to include the read mark for all returned records.
	
	- setTaggingEnabled(Boolean taggingEnabled) – Enabling this allows Darwino to maintain an array of tags for each document. You can search documents a tag or a combination of tags. There is also a well-optimized function at the store level that returns a tag cloud.  

	- The addField() method adds fields to a store’s full text search index. 

	- setFields() has two forms. The simple form takes the name of a field as its parameter and it indexes that field. The other form takes an array of FieldNodes. 
    (Question: when to use addField() vs. setFields()? This whole section is pretty thin; I need more detail.)

	- addQueryField() has five forms. The first takes one parameter, that being that name of the field. It will use that value both as the field name and as the path to the data. The next takes three parameters: the field name, the data type, and a Boolean determining whether the field is multiple. The third adds a specification of the path in the JSON. The fourth form takes a single parameter, this being a callback fieldFunction, which itself has several parameters: the field name, the data type, the multi Boolean, the name of a registered callback function and a JSON path to the data in the JSON document which acts as the parameter to the referenced callback function. The fifth form is like form #4, but uses a Darwino query language statement in place of the function name and parameter. 
 By allowing a callback function or query result, the function allows sophisticated processing to be called when creating the field value, which can then be used in a query.

 INDEXES
	The store.addIndex() method creates an index based on a subset of the data in the JSON documents. Once you add the index, you define the keys and the values to extract fmor the JSON document. 
     
     When you execute a query on the index using the Darwino API, you can choose either to return the values in the index, or the JSON value in the document. 
      
     For example, index.keys(“_unid”) will set the unid as the key.
index.valuesExtract(“\”$\””) will specify the root of the JSON value as the value to extract. This is using the the query language (or “extraction language”) 
 
 When specifying the keys, you can say whether the keys are unique or not. This is done by calling the setUniqueKey(Boolean uniqueKey) method.
setUpdateWithUserData() specifies whether the index should be undated when a document’s social data, which is stored outside of the document, is changed, even if the document itself has not been changed. An example of this would be if you are tracking ratings for documents and you wish to display the average rating for each document. Rather than recalculate the average every time you query the index, you would store the rating average every time the ratings are changed. By default, the index is not updated when the social data changes.


###Configuring the database using managed beans
   There are two very important points in the Darwino philosophy here.
 
 One is that Darwino is built in layers, and you pick the the layer you want to use.  It’s better and more effective when you pick the highest-level one.
 
 The second is that there is nothing that is hard-coded.  Everything is provided by extension points. Extension points can rely on managed beans. For example, the connection to the database is defined through beans. But this is not the only way to define your connection. There is an extension point for defining your connection, and one default implementation of the extension point is looking for beans. Everything in Darwino is built upon extensions, and to find the extensions there is the notion of a plugin. A plugin is a class that can be provided by your application or by a library and that add implementation for extensions. Extensions can be contextual to a platform or not. For example: where managed beans are found is provided by an extension point, because on a mobile device you won’t find the beans at the same place as on a J2EE server. On the server, this is done via the J2eePlatform.java plugin.



## Documents
### CRUD operations
It is possible to perform create, read, update, and delete operations on documents.
	At the base, the Darwino API is a Java API. There is also a JavaScript API which is a JavaScript flavor of the Java API.  To do CRUD operations, you get the session and from that the database and store, and there you create, read, update, and delete documents. As long as you’re using the Java API locally, you can execute a series of operations within a transaction. At the session level, you can query whether the session supports transactions. If it does, you can start a transaction, perform a set of operations on documents, and then roll back the changes if needed. This is familiar to users of many relational databases, but is alien to Domino and mongoDB.

 There are three ways, in Darwino, to access documents:
 - The Java API. This API talks directly to the database whenever it is a JDBC-based database, or is SQLite.
 - REST services, which operate on top of the Java API. The set of REST services in Darwino support everything the Java API allows in regard to document operations EXCEPT transactions. Because REST is stateless, transactions, which are stateful, cannot be supported.
 - REST services wrapped for particular languages. Darwino provides two wrappers to assist in REST service work: a Java wrapper and a JavaScript wrapper.
The Java wrapper is the Java API, but instead of being implemented to deal directly with the JDBC driver locally, it deals with the REST services. Nonetheless, it is the exact same API. The only difference is how you get access to the session.
The JavaScript wrapper in intended for use in a JavaScript environment such as a browser or a server-side JavaScript environment like node.js.
In the future there could be other wrappers, just as PHP bindings, Ruby binding, etc…

 ### Access to the JSON content
 The JSON document in Darwino is more than a JSON object; it has several components:
 - JSON content – typically a JSON object, but it could also be a JSON array. IT is unusual for it to be anything other than a JSON object, because only the JSON object supports use of system data that can be of use to Darwino.
 - optionally, attachments
 - metadata – For example, the unid and the docid, modification date and modification user, etc… These metadata are not modifiable manually by normal operations. 
 - system data that can be modified – For example, a list of tags. System fields are actually fields in the root of the JSON content object, but their names start with an underscore.
optionally, transient property fields that can contain data we want to pass to document events but never want to store in the document. They are never saved. They can be set and read at the document level, but they are never persistent.

 Methods are provided for accessing the JSON content in all data types. They all take a String as their sole parameter, and return the value of the requested JSON field, assuming that the content is a JSON object. These methods cannot access hierarchical data, but they are very convenient for accessing fields that are at the root of the document.
 - getStr(), getInt(), getLong(), getDouble(), getBoolean(), and getDate()

 In addition, there is a method for executing JSONPath (XPath for JSON) expressions. JSONPath simplifies the extraction of data from JSON structures. It permits dot notation and bracket notation, and allows wildcard querying of member names and array indices. It is documented [here](http://goessner.net/articles/JsonPath/). JSONPath expressions can be executed in Darwino via the jsonPath method:
 - jsonPath(Object path)

	
###	Managing attachments
Every document can have a set of attachments. The methods for working with attachments are at the document level. Working with attachments is optimized; the attachments are loaded only when needed. When you create or update an attachment, nothing actually happens until you save the document. If the document save is part of a global transaction, then the work is postponed until the transaction is executed.

Document methods for Attachments:
- getAttachmentCount() returns the number of Attachments
- getAttachments() returns an array of Attachments
- getAttachment() returns the Attachment
- attachmentExists() – returns a boolean 
- createAttachment(String name, Content content) – populates the content of the Attachment with the specified Content object, which can be Base64Content, ByteArrayContent, ByteBufferContent, EmptyContent, FileContent, InputStreamContent, or TextContent.
- deleteAllAttachments() – removes all Attachments

Attachment methods:
- getName()
- getLength()
- getMimeType() returns the MIME type of the content. If it wasn’t set when it was created, the system will base the returned value on the extension of the attachment’s filename. If there is no interpretable extension, it will assume binary.
- update(Content content) updates the content of the attachment
- getContent() returns the content of the attachment as a Content object.
- getInputStream() returns the content of the attachment as an InputStream
There are three “readAs” methods intended for convenience, but are not meant to be used with large attachments. They are not as efficient as working with an InputStream:
- readAsBase64() 
- readAsString()
- readAsString(String encoding)  

The Content object has four methods:
- getMimeType()
- getLength()
- createInputStream()
- copyTo(OutputStream os)
A Content object is an accessor to the data; it is not the data itself. This means that when creating attachments, the Content object does not actually load the data into memory until the document is being saved. Until then, it merely points to the data. This is an important performance consideration.


##	Document Meta-data - System data in the JSON document
##	Response documents
The parentid field contains the unid of a document’s parent, if there is one. This is how a hierarchy of documents if defined in Darwino. The data integrity of this relationship is not enforced; it is possible to have “orphan” documents– documents with a parentid that is not valid.
	
### Synchronization master document
Darwino implements “functional replication”. This means that selective replication can be based on changes to an ancestor document, as opposed to the current document. The synchronization master for a document is the document that is checked for changes when the replicator is testing for selective replication eligibility. For example, child document might use the root parent document as their synchronization master. Then, when the root document changes, and only when it changes, will the child documents replicate as well. 
    
 It is not necessary for a synchronization master to be an ancestor; other document relationships could benefit from the ability to so specifically define under what circumstances they will replicate as a group. (Expound on this.)

    


## Cursors and queries
Cursors facilitate the selection of document sets from the database, and the extraction of data from the selected documents. You specify what you want to extract, and then you process the result. When you process the result, there is only the current document in memory; it doesn’t load everything into memory and iterate through that set. The cursor lets you step through the results one by one. The exception is when it is done through HTTP. Because HTTP is stateless, results are paged and multiple documents are loaded at one time. When it’s local, though, it is able to deal directly with database cursors. This is for performance reasons. 
 
 A cursor consumes a database connection; thus, it has to be recycled when it’s done. To avoid the recycle mess that plagues Domino developers, and to avoid reliance on the intermittent garbage collector, Darwino provides a callback to the cursor. The Cursor calls this cursor handler for every result. For example, when iterating through a result set, you call find(), passing a CursorHandler. The CursorHandler has one method, which is handle(), which handles the CursorEntry. The cursor is allocating the database connection, executing the SQL query, calling the CursorHandler for every result, and then closing and recycling the database connection.
 
 Domino developers can think of a cursor as being like a ViewEntryCollection, with a set of methods for dealing with the subset of documents that are represented in the collection. For example:
- deleteAllDocuments(int options) – Will delete all of the documents not by iterating through and deleting each one-by-one, but instead will generate a SQL query to do the job in one fell swoop.
- markAllRead(Boolean read) and markAllRead(Boolean read, String username) will mark the cursors documents as read, either by the current user of by a particular username.


### Query and extraction language
To calculate aggregate data, such as average, minimum, and maximum, pass an aggregate query to the cursor. For example:
{ Count {$count: @manufacturer”}, Sum: {$sum: “@released”}, Avg: {$avg: “@released”}, Min: {$min: “@released”}, Max: {$max: “@released”}}

When a cursor runs, it calls the cursor handler with all of the cursor entries. In the cursor entry are the key and the value, accessible via getKey() and getValue(). What these two represent depends on the source of the cursor. A cursor executed on a store will have documents as its result, the key will be the unids of the documents, and the value will be the JSON of the documents. If, instead, the cursor was executed on an index, then the key will be the key of the index, and the value will be either the value that’s stored in the index or the JSON value from the corresponding documents, depending on an option applied to the cursor.

(Must provide examples of query options. Where is this best documented?)


### Executing a query
A cursor is created at either the store or the database level. The store’s openCursor() method returns a cursor on which you then apply the selection condition. The Cursor methods return the cursor itself, which means that the methods can be stacked. For example:
Cursor c = index.openCursor().ftSearch(“version”).orderByFtRank().range(0,5)

This will perform a fulltext search on “version”, order the result by rank, and return the first five entries.

####Cursor Options
It is possible to query by tags. This is done by passing a list of tags and specifying via the TAGS_OR option whether to perform an “and” or an “or” with the tags.
DATA_READMARK adds a flag to every cursor entry indicating whether the entries have been read by the user executing the query.
DATA_WRITEACC adds a flag indicating whether the current user can edit the documents.



####Hierarchies in Cursors
The query engine natively understands Darwino document hierarchies, and the Cursor takes advantage of that by allowing cursor queries to apply to only root documents and not the associated response documents. In other words, a cursor query can test for values in the root documents, and then return the matching root documents along with their responses, regardless of whether the responses match the query condition or not. The CursorEntry objects returned by the cursor will have an indentLevel property (an int) that identifies where they lie in the hierarchy, with 0 indicating a root document.

The range() method which, given a number to skip and a number to return, will return a subset of a curser’s entries, can be controlled via the Cursor options (by specifying RANGE_ROOT) to apply the skip and limit parameters only to the root documents, and to then return all associated response documents, without regard to the limit parameter.

#### Cursor Sorting Options
orderBy(String…fields) sorts cursor results by field. This can be an extracted field or a system field (for example, _unid or cuser). If the RDBMS supports JSONQuery, then a JSON reference can be used can be used, such as a JSON field name or a direct path to a JSON field. Optionally, the sort order can be specified by appending a space and the text “asc” or “desc” to each field/path value. For example:
.orderBy(“@state desc”, “unid”) will sort by state descending, then by unid ascending (that being the default).

The fulltext rank can also be used for specifying the order in a curser. The orderByFtRank() method.

The ascending() and descending() methods can be applied to both the orderBy() and the orderByFtRank() method results.

#### Categorization
In a cursor, categorization is based on the sort order. If no order is specified, and if there is no index, the unid is used. Categorization is completely dynamic, being based on the sort order which is not fixed.

The .categories(int nCat) method takes as its parameter the number of category levels to apply.

It is also possible to request that the documents not be extracted in the cursor; in this case; the result will consist only of the categories.

Another option is to extract categories while skipping the highest level categories. For example, extract two categories, but start at the second-level category, returning levels two and three.

Browsing the entries:
There are two ways to execute a cursor:
- Call find(), passing a Cursorhandler. It will execute the query, returning the entries one-by-one and calling the Cursorhandler for each.
- Call findOne(), if you are sure there will be only one entry returned, or if you are interested only in the first. There is no need to pass the callback Cursorhandler.

The count() method will return the number of entries in the cursor.

### Optimizing queries
The query language is robust, with a lot of operators, and is optimized for the underlying database system. It will attempt to use only native database functions when constructing its SQL; if necessary functions not supported by the database, it will still use those that ARE supported for parts of the query. For example, if there is an “AND” in the query, and only one condition is directly supported by the database, the query language will execute that part of the query first and only then iterate through the results one-by-one.

The query language’s API makes it so the cursor can be queried to determine whether a particular query is supported by the database. It compiles the query and answers whether it can generate SQL for the query, or it can generate partial SQL, or it cannot generate SQL.

(Question: The data extraction language is important. Is there documentation available that would help me do it justice here?)

## 5	Accessing and storing social data
There is a set of social data that can be associated with any document. There are three kinds of social data:
-	Comments: Darwino creates a default store for the comment data. Keeping comments out of the documents themselves prevents unnecessary updated to the documents which then must replicate and could result in conflicts.
-	Tags: The tags are stored as an array in the _tags field in the document. The tags can be queried, and the store can return a list of tags that can be used, for example, to create a tag cloud.
(Question: Private tags are not yet implemented. Should we include them here?)
Private tags, visible only to their creator, can be stored as read-protected response documents associated with the document being tagged. Because they are read-protected, they will be private to that user. Because the store’s list of tags respects this security, only the private tag’s creator will see such a private tag in a tag cloud.
-	User-dependent values, which are handled at the store level. These don’t require that the document be loaded for them to be applied, and they are stored externally to the documents in order to avoid unnecessary document modification, replication conflicts, and excessive data in the documents (there can be a lot of ratings and read flags):
--	Ratings: With the rate() method, a document is assigned an integer value associated with a user. Appropriately, only one rating per document per user is stored. There are three methods for retrieving rating data:
 - getRate() – Given a unid and userName, returns that user’s rating for the document
 - getRateAvg() – Given a unid, returns the average rating from all users for the document
 - getRateSum() - Given a unid, returns the sum of all ratings for the document, for example to count votes where ratings would be defined by the application to range, say, from -1 to 1.

 -- Sharing: With the share method, a unid and username sets a Boolean flag indicating whether the document has been shared by that user. It is similar in concept to a Facebook “like”. Two methods exist to interrogate shares:
 - isShared() – Returns a Boolean indicating whether the document is shared by a specified username
 - getShareCount() – returns the number of shares by all users as an integer

 -- Read: While not specifically social, the read flag is functionally similar to the other social data. At the store level, there is an option to enable the auto-flagging of documents as being read when they are loaded. (Question: Is this correct? What is that option?) This applied only to documents that are actually loaded; their being included is a query result is not sufficient to mark them as read.
There are three methods  in the store to enable manipulation and querying of the read flags:
 -- isRead() returns the read value for the specified document and username
 -- markRead() - Given a unid, a Boolean, and a username, sets the read flag for that document and user.
 -- getReadCount() – returns the number of reads for the specified document.

There are two ways to access this social data. The methods at the store level require the unid and the username, in addition to any flags being set, to identify the document. The same methods are available in the Document object, where they do not require those two identifying parameters. Which set of methods you choose will depend on the context. If you have the document loaded, then use the Document methods, if just for simplicity. If you’re in a view, use the Store methods since they will be significantly more efficient (they will not require loading the documents).

 This social data can be used in extracted fields in the documents, making it easy to created indexes based on their values for querying and sorting.




## 6	Data synchronization (extract this to its own chapter – maybe to just before the Domino piece)

## 7	Registering and handling events
At the server level, you can register an ExtensionRegistry. This registry provides a set of functions – currently a set of five:
-	BinaryStore: When Darwino stores attachments, they can be stored inside the database, or apart from the database. The BinaryStore is an interface that facilitates storing the attachments outside of the database by providing a set a CRUD methods.
-	DocumentEvents: When an operation is being performed on a document, the runtime will call the methods in the DocumentEvents:
--	postNewDocument – called right after a document has been created, so that you can, for example, change document values.
--	postLoadDocument – called right after a document has been loaded.
--	querySaveDocument– called immediately before a document has been saved. It is possible to cancel the save from within this event simply by throwing an exception. The exception can include the reason for the save cancellation.
--	postSaveDocument – called immediately after a document has been saved.
--	queryDeleteDocument – called before a document is deleted, EXCEPT when a group of documents is being deleted. Group deletes are performed directly by a SQL statement, and for performance reasons, and so this event is not raised.
--	postDeleteDocument – called after a document delete, except, as with queryDocumentDelete, when a group of documents has been deleted.
 
 Note that these events are also raised when called via HTTP. 
 
 Transient properties set at the document level can be accessed from within these events, despite the fact that these properties are never saved.

-	SynchronizationEvents: As synchronization is taking place, this set of events will be raised, allowing customization of the synchronization actions. Like the DocumentEvents, code here can manipulate values or cancel the action altogether.
--	queryCreateDocument
--	postCreateDocument
--	queryUpdateDocument
--	postUpdateDocument
--	queryDeleteDocument
--	postDeleteDocument

 In addition, there are events related specifically to synchronization conflicts. Code here can customize the action to be taken during conflicts.

 conflictAction – raised when the runtime has detected a synchronization conflict, this event provides information about the conflict, including what changed in the source document and what is in the target document. Code here will return an ConflictAction, which will be one of the following: 
 --	DEFAULT – the default handler should be applied
 --	SOURCE – the source should win
 --	TARGET – the target should win
 --	CUSTOM – call the handleConflict method, where the conflict can be handled by custom business logic. For example, in an HR application where several people interviewing an applicant each have access to a different section of the document. In this case, you would choose to merge the different sections.
-	FieldFunction: Functions used when extracting fields from documents or computing indexes are registered here.
-	InstanceFactoryImpl: A database can have multiple instances, each with its own security configuration; in other words, there is per-instance security. That instance security is dynamic, based on business logic.

 By default, a database has just one instance; the instance factory is the means to create more. Once the instance factory has been implemented and the instance is created based on the database and the instance name, the contribute() method of the instance is the mechanism for adding roles and groups to the user context.

 For example, the ACL of a database, and the reader/writer fields in the documents, may specify that only the members of a particular group may have access to the data. It is the job of the instance’s contribute() method to add to the current user their list of roles and groups; this list is determined dynamically depending on business logic, and that logic is free to make use of any directories and database data available to it. 

 There is a default implementation of the ExtensionRegistry called DefaultExtensionRegistry. Use this to associate particular document event handlers with specific database stores. Once you create an instance of the DefaultExtensionRegistry, its registerDocumentEvents() method can be used to define specific cases of the document events. By specifying the database and store, you define which document events you want to override; for example here you would code your custom querySaveDocument event.
```
Public class AppDBBusinessLogic extends DefaultExtensionRegistry {
	registerDocumentEvents(“<My Database Id>”, “<My Store Id>”, new DocumentEvents() {
		@Override
		public void querySaveDocument(Document doc) throws JsonException {
		}
	});
}
```

 You can register events globally, and at the at the database level, and at the store level. If an event is registered at the store level, that is one that will be called for documents in that store. If, instead, there is no event registered at the store but there is one registered at the database, then the database registration will be in effect. The most-local (most precise) registration is the one that is used.

 This is also the case for registered field functions.



## 8	Security (security coverage is strewn about. Maybe this shouldn't be a seperate chapter here, or this chapter should be a grand overview)
 Database security
Darwino implements multi-level security. You can assign security to the Server object; you can control who can and cannot access the server. At the database level, you can assign an ACL. In the ACL, you can define who can access the database, manage the database, read documents, create documents, delete documents, and edit documents. At the Document level, you can maintain a list of users who can read or read/write the document. 
 
 The same principles apply to both readers/writers, and excluded-readers/excluded-writers. 
 
 1- Entries 
Each entry can be: 
- a person, and then the entry is of the form p:id 
- a group, and then the entry is of the form g:id 
- a role, and then the entry is of the form r:id 
- everybody, *

 An entry can be readonly (reader field) or read/write (writer field). A writer entry is automatically a reader as well. 
If an entry appears in both the readers and writers, then it is a writer.  
 
 2- Security behavior 
If there are no entries attached to a document, then there is no document security 
If there is at least one entry (reader or writer, or both), then there is document security. 
If everybody should be a reader and while writers should be limited, the solution is the following: 
writers entries should contain the limited list 
reader should contain one entry everybody * 

 3- Storing readers/writers 
Readers are stored in the _readers field, while writers are in _writers. 
These fields can directly contain an array of entries (see 1-) or an object containing several arrays, one per property 
ex: 
{ 
_readers: ['p:phil', 'p:dave'], 
_writers: ['p:jesse'] 
} 
or 
{ 
_readers: { 
        field1: ['p:phil', 'p:dave'], 
        field2: ['p:bob'] 
}, 
_writers: ['p:jesse'] 
} 

 4- Helpers 
There is a Java class, SecurityHelper, that can be used to manipulate the these fields. They are used, for example, in the CRUDSEC.java unit test. 
 
 Regarding ereaders and ewriters: Darwino, when composing a SQL query, adds a subquery to exclude what is not allowed to be seen. This incurs a cost. To avoid this when possible, there is a database property indicating whether document security should be enabled. When it is not enabled, generated queries can avoid the step of running the subquery. A result of this is that if the flag is not set, readers and writers on documents will be ignored in all of the database’s stores. Options for this property are: no document security, reader/writer security only, ereader/ewriter security only, and all security features.


##9 REST API
All of the features of the JSON store are exposed through REST services, and the REST services are wrapped in the various language binders. The REST services are optimized for performance and designed to be easy to use. They execute in different environments, server-side and in the mobile device, and can be coded once and run in multiple platforms.

In the Darwino Playground is an API Explorer; this can be used to experiment with the REST Services’ capabilities. All of the services are covered there, with documentation for all of their parameters.

(SCREEN SHOT)

The Darwino framework allows the REST services, available by default, to be disabled at the database level, or to be overridden and enhanced. By overriding the service factory, is it possible to permit access dynamically based on the database, store, and current user. There is also a DocumentContentFilter interface for the REST services that allows dynamic filtering of the document data that is being produced, typically for security purposes. Along with that is a feature of the API that allows reconciliation of filtered documents upon save, so that if a section was filtered for presentation, that filtered data is not lost from the document when saving.

The REST services can be extended to accommodate JavaScript components, jqGrid for example, that expect the JSON they’re consuming to be in a particular format. One way to satisfy such a component would be to transform the JSON client-side, but that is not particularly convenient or efficient. Using Darwino’s JsonStoreServiceExtension, custom REST services can be defined and the existing REST services’ output can be modified. Being server-side, the Java API can most efficiently render the JSON as needed before it is emitted.


## 10 Darwino API over HTTP
