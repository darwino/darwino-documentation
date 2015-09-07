Darwino DB API
=======================

- 1	Concepts
The Darwino database is a NoSQL store for storing JSON documents and related attachments. It is actually a database of JSON documents, and you can attach binary pieces to any document. 
 
 Built on top of existing relational databases, it provides the benefits of a mature, well-established technology. Darwino takes advantage of the latest NoSQL additions, such as native JSON and data sharding. 
 
 Another benefit of using relational databases as the groundwork is that you can use any existing tool that can connect to a relational database to access the data.  You can use BI tools, reporting tools, or other big-data analysis tools, like IBM Watson, and they will directly understand the database format. 
 
 One of the key points of the JSON store is that there is a single implementation that works everywhere: servers (Windows, Linux, OS X, etc…), and mobile devices. The same code runs in both environments. Moreover, these disparate environments can replicate data. 

	-- Server, Session, Database, Store, Indexes
    There are two concepts here. One of the concepts is the Server.  Even on a mobile device, you have the Server. The Server is, in effect, the database itself; the Server encapsulates the connection to a physical RDBMS.  It points to your DB2, your SQL Server, your PostgreSQL, or to your SQL Lite. When you connect to an RDBMS on the server, you will specify the JDBC path and user password that the runtime can use to access the data. 
    
    When you want to do this through a Darwino server, you have to do it through a Session object, and a Session object is related to a particular user.  The reason for the Session object is to allow different users to share the same server. The app itself will use one database server but multiple Sessions. The Session, created from the user’s ID, is what will be used by the runtime to apply security to the data. This is an important concept because the server connects to the physical relational database with one single user and password, which means that this user and password can access all of the data. The security is then applied by the Darwino runtime through the Session object.   
    
    Then comes the organization of the data.  The data are organized into databases; but we should not confuse that with the relational database file.  Here, it’s a document database.  A database is a set of stores with common characteristics. The store is a container for JSON documents.  This notion doesn’t exist in IBM Domino; in Domino, you have the NSF, and the NSF is a container for Notes documents. In Darwino, you have one extra level: The database is a set of stores, and each store is a container for documents. That allows you to put documents into different “buckets” that have different characteristics, such as different indexing strategies. For example, in a CRM application, you may have one document that is a Customer, and another that is a Product. You would not want to apply the same indexes to these documents.  You can specify further customization for a store, such as whether full-text search is enabled.
     
     Then, there is the document.  In Darwino, a document is four things:
-- JSON data. It’s not necessarily a JSON object; it could be a JSON array. Most of the time it will be a JSON object; making it a JSON object is a best practice, because doing so is necessary to allow use of features such as ?????? (inaudible, about 17minutes in.)Reader/writer fields, maybe?
-- metadata.: the UNID, creation date, last modification date, creator name, last modifier name. There are also several replication-related metadata items, but these are generally not of interest to the developer. These include the last replicated time and the sequence ID used in detecting replication conflicts. 
-- A potential set of binary attachments. The attachments consist of binary data identified by name, and with an associated MIME type so that consumers can know what to do with the data. A single Darwino document can have multiple attachments, but the attachment name is the key so there cannot be two attachments with the same name in a single document. (Question: what other data is stored regarding the attachment? Is there a date and size?)
-- A set of social data.  Darwino, by default, it social-enabled. You can rate a document, you can share a document, you can vote for a document, you can tag a document. The social data is ABOUT the document, but it is not stored IN the document. The social data is stored in a separate table, and it is user-based: if five users rate a document, there will be five records in the social table, one per user, all referencing that document. The table is replicated along with the documents.  There is one exception to this separation of the social data from the document’s JSON data: tags. Tags are stored in both the document and the social table. In the document, the tags are stored in the field called “_tags”. When the document is saved, the values are copied to the social table. (Question: How much should we discuss this, given that the API makes it invisible to the programmer? Also, is it just one table per store?) 
 
 Documents can be organized hierarchically. Every document can have a reference to a parent document. That reference is stored in the document’s “_parentid” field. This field is accessible for read/write through the API. 
 
 Database security
Darwino implements multi-level security. You can assign security to the Server object; you can control who can and cannot access the server. At the database level, you can assign an ACL. In the ACL, you can define who can access the database, manage the database, read documents, create documents, delete documents, and edit documents. At the Document level, you can maintain a list of users who can read or read/write the document.

	-- UNID, DocId
    Documents have two identifiers: the UNID, which is a string provided either by the API when creating the document or, if it’s empty, then it is generated automatically by the system. When you create the document, you can pass the ID or allow it to be generated. A UNID must be unique per store. When there are two replicas, for example one on the server and one on a mobile device, the UNIDs will match. 
    
    (Aside: The parentID is used to identity the document’s parent. It is the UNID of the parent. ) 
    
    The NoteID is an integer that is unique inside the database. It is generated by the system; you cannot specify it when you create the document, and it cannot be changed. Being an integer makes it much more efficient in database operations than a string such as the UNID. 
    
    However, the NoteID is not universal; it won’t be the same in two different replicas. Because of this, you should not store it for programmatic use and try to rely on its always applying to that document. NoteIDs are used internally because in the database each document maps to a set of relational tables; the relations between these tables are expressed most of the time through the NoteID because it’s much more efficient than the UNID.

- 2	Defining and deploying the database
 A Darwino database is actually a set of tables within a relational database. The schema of these tables does not depend on the Darwino application. You can create tables, for example, at the application level–at the logical level–and the schema will always be the same. (Question: REALLY??? How does this work?) The schema if defined by Darwino. This is important because in some organizations altering tables’ schema is strictly controlled. They require that a new DDL be submitted for the administrator to apply. Darwino make sit so that no new table definitions are necessary… UNLESS you want to add additional indexes.

 The prefix of the table names is the name of the Darwino database. This restricts us to names that are compatible with the rules of the relational database system. The suffix is always an underscore followed by three characters. Let’s take a look (need screen shot):
 - _dsg: The Design table. This includes the lists of fields, stores, indexes, etc… This is “generally” only one record in this table (Question: why “generally”? Are there exceptions?), with the value “DATABASE” in its “type” column. The “name” is empty, and the “json” column contains the definition of the database. When you initialize a Darwino database, it will create this set of tables and store the database definition in that single record in the _dsg table.
In the database definition is a field called “version”. When you initialize the database, the version will be “1”. Every time the database definition changes, increment the number (Question: is the incrementing automatic?). This is important in Darwino because the application is disconnected from the database, so it is possible to have a database design that is not at the level expected by the application. When your code is opening the database, it will open this record from the _dsg table, extract the version value, and do a compare.
If it’s a match, it will open the database, return a handle, and off you go.
In another case, the application may be expecting to work with an higher version of the database. When opening, you specify how to update. One choice is to upgrade the database by running a function that you provide to give the new database definition.
If, on the other hand, the application is expecting a lower level of the database, it will fail. The application will not be able to update the database because it won’t know how.
In AppDatabaseDef.java, the loadDatabase method does the job of checking the version number and returning either null or a handle to the open database. 
If the runtime itself has been updated,  the “tableVersion” in the database definition come into play. It is not managed by the application; it is managed by the runtime. Domino developers can think of it as the ODS.
 - _doc: The docmuent table 
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



-- Database definition class
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


-- Configuring the database using managed beans
    There are two very important points in the Darwino philosophy here.
 
 One is that Darwino is built in layers, and you pick the the layer you want to use.  It’s better and more effective when you pick the highest-level one.
 
 The second is that there is nothing that is hard-coded.  Everything is provided by extension points. Extension points can rely on managed beans. For example, the connection to the database is defined through beans. But this is not the only way to define your connection. There is an extension point for defining your connection, and one default implementation of the extension point is looking for beans. Everything in Darwino is built upon extensions, and to find the extensions there is the notion of a plugin. A plugin is a class that can be provided by your application or by a library and that add implementation for extensions. Extensions can be contextual to a platform or not. For example: where managed beans are found is provided by an extension point, because on a mobile device you won’t find the beans at the same place as on a J2EE server. On the server, this is done via the J2eePlatform.java plugin.



- 3	Documents
	-- CRUD operations
	-- Access to the JSON content
	-- Managing attachments
	-- Document Metadata/System data in the JSON document
	-- Response documents
	-- Synchronization master document
- 4	Cursors and queries
	-- Query and extraction language
	-- Executing a query
	-- Optimizing queries
- 5	Accessing and storing social data
- 6	Data synchronization
- 7	Registering and handling events
- 8	Security 
- Database security
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

- 
- 9	REST API
- 10	Darwino API over HTTP
