Darwino DB API
=======================

# Concepts
The Darwino database is a NoSQL store for storing JSON documents and related attachments. It is actually a database of JSON documents, and you can attach binary pieces to any document.

Built on top of existing relational databases, it provides the benefits of a mature, well-established technology. Is it fully transactional, and it scales to very large datasets.

Darwino takes advantage of the latest NoSQL additions, such as native JSON and data sharding. 
 
Another benefit of using relational databases as the groundwork is that you can use any existing tool that can connect to a relational database to access the data.  You can use BI tools, reporting tools, or other big-data analysis tools, like IBM Watson, and they will directly understand the database format. 
 
One of the key points of the JSON store is that there is a single implementation that works everywhere: servers (Windows, Linux, OS X, etc…), and mobile devices. The same code runs in both environments. Moreover, these disparate environments can replicate data. 

##Server
Even on a mobile device, you have the Server. The server is the entry point to the database. All database operations starts from there. The Server is, in effect, the database itself; the Server encapsulates the connection to a physical RDBMS.  It points to your DB2, your SQL Server, your PostgreSQL, or to your SQL Lite. When you connect to an RDBMS on the server, you will specify the JDBC path and user password that the runtime can use to access the data.

## Session
When you want to do this through a Darwino server, you have to do it through a Session object, and a Session object is related to a particular user.  The reason for the Session object is to allow different users to share the same server, as in a web application supporting multiple simultaneous users.

The app itself will use one database server but multiple Sessions. The Session, created from the user’s ID, is what will be used by the runtime to apply security to the data. This is an important concept because the server connects to the physical relational database with one single user and password, which means that this user and password can access all of the data. The security is then applied by the Darwino runtime through the Session object.

## Database
Then comes the organization of the data.  The data are organized into databases; but we should not confuse that with the relational database file.  Here, it’s a document database.  A database is a set of documents with common characteristics. 
## Stores
The store is a container for JSON documents. That allows you to put documents into different “buckets” that have different characteristics, such as different indexing strategies. For example, in a CRM application, you may have one document that is a Customer, and another that is a Product. You would not want to apply the same indexes to these documents.  You can specify further customization for a store, such as whether full-text search is enabled.

###Pre-defined stores
In the database definition are four pre-defined stores. These stores are created automatically by Darwino and can be utilized as-is, but they can also have their definitions overridden and customized (by adding indexes or fields, for example). It is always possible to create specific stores for these stores’ purposes; these pre-defined stores exist as a convenience.
- _default: This store can act as a placeholder. There is no index or field extraction for this store by default, but it can be used for quick-and-dirty storage of data.
- _local: This store is identical to the _default store, except that it is never replicated. It is useful for, among other things, storing device-specific data on a mobile device.
- _comments: The social data Comments are stored here by default. Storing comments here instead of inside the documents themselves avoids having the documents marked as modified every time a user adds a comment. It also avoids replication conflicts on the documents when multiple comments are added in a short period of time. Also, because comments can be complex, even including attachments, storing them here avoids making the documents overly complex.
- _design: Not currently used, this is a special store used to store design elements of the application.


## Document
   Then, there is the document.  In Darwino, a document is four things:
- JSON data. It’s not necessarily a JSON object; it could be a JSON array. Most of the time it will be a JSON object; making it a JSON object is a best practice, because doing so is necessary to allow use of features such as tagging, or reader/editor security. These are system fields added at the root of the document, thus implying that the root object is a document.
- metadata.: the UNID, creation date, last modification date, creator name, last modifier name. There are also several replication-related metadata items, but these are generally not of interest to the developer. These include the last replicated time and the sequence ID used in detecting replication conflicts. 
- Potentially, a set of binary attachments. The attachments consist of binary data identified by name, and with an associated MIME type so that consumers can know what to do with the data. A single Darwino document can have multiple attachments, but the attachment name is the key so there cannot be two attachments with the same name in a single document. Attachments are generally stored in the relational database, in a dedicated table, and referenced from the document. Now, this can be customized and the files can be stored elsewhere, like the file system or a content management system. In addition to the attachment name and pointer, Darwino also stores a length and a size, plus some replication-related information, and an ETag used is to minimize downloads.
- A set of social data.  Darwino, by default, it social-enabled. You can rate a document, you can share a document, you can vote for a document, you can tag a document. The social data is ABOUT the document, but it is not stored IN the document. The social data is stored in a separate table, and it is user-based: if five users rate a document, there will be five records in the social table, one per user, all referencing that document. The table is replicated along with the documents.  There is one exception to this separation of the social data from the document’s JSON data: tags. Tags are stored in both the document and the social table. In the document, the tags are stored in the field called “_tags”, making them easy for an application to edit. When the document is saved, the values are copied to the social table.
 
####Document Hierarchy
Documents can be organized hierarchically. Every document can have a reference to a parent document. That reference is stored in the document’s “_parentid” field. This field is accessible for read/write through the API. 

The parent document must be in the same database as the child. There is a specific syntax for this field: when the parent is in the same store, then the value is just the parent document UNID. When the parent is in a different store, then the syntax is UNID:STOREID.

> Note: The system does not enforce the validity of the parent. This can lead to documents pointing to a non-existent parent. In this case, they are called "orphan" documents.

Darwino also has the concept of a "sync master" document. If a document has a sync master defined, then when that sync master is modified, and only when the sync master is modified, the child document will be subject to replication along with the sync master.

> Note: While typically it would be, a sync master does not have to be an ancestor of the documents that refer to it as their sync master. There are other relationships besides parent/child that can benefit from the sync master technology; for example, all documents associated with a particular project could be linked in thso way, whether or not they are hierarchically related.

 
####Document security
Darwino implements multi-level security. You can assign security to the Server object; you can control who can and cannot access the server. At the database level, you can assign an ACL. In the ACL, you can define who can access the database, manage the database, read documents, create documents, delete documents, and edit documents. At the Document level, you can maintain a list of users who can read or read/write the document.

##	UNID
   Documents have two identifiers: the UNID and the docID. The UNID is a string provided either by the API when creating the document or, if it’s empty, then it is generated automatically by the system. When you create the document, you can pass the ID or allow it to be generated. A UNID must be unique per store; this is enforced by a database unique index. When there are two replicas, for example one on the server and one on a mobile device, the UNIDs will match.

>    Note: The parentID is used to identity the document’s parent. It is the UNID of the parent.

## docID
   The docID is an integer that is unique inside the database. It is generated by the system; you cannot specify it when you create the document, and it cannot be changed. Being an integer makes it much more efficient in database operations than a string such as the UNID. 

   However, the docID is not universal; it won’t be the same in two different replicas. Because of this, you should not store it for programmatic use and try to rely on its always applying to that document. docID are used internally because in the database each document maps to a set of relational tables; the relations between these tables are expressed most of the time through the docID because it’s much more efficient than the UNID.
