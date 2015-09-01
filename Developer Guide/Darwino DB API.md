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
	-- Database definition class
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
