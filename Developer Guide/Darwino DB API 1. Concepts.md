Darwino DB API Concepts
=======================

##Server
Even on a mobile device, you have the Server. The server is the entry point to the database. All database operations starts from there. The Server is, in effect, the database itself; the Server encapsulates the connection to a physical RDBMS.  It points to your DB2, your SQL Server, your PostgreSQL, or to your SQL Lite. When you connect to an RDBMS on the server, you will specify the JDBC path and user password that the runtime can use to access the data.

In a multi-user environment such as on the web server, the same physical JDBC connections will be used across users. The data access security is then managed by the Darwino code.

## Session
The Session object allows different users to share the same server, as in a web application supporting multiple simultaneous users. In a web environment, there is one Session object created per request, and discarded when the request is completed. The Session object carries the user credentials, and is available through the DarwinoContext object.

The app itself will use one database server but multiple Sessions. The Session, created from the user’s ID, is what will be used by the runtime to apply security to the data. This is an important concept because the server connects to the physical relational database with one single user and password, which means that this user and password can access all of the data. The security is then applied by the Darwino runtime through the Session object.

## Database
Then comes the organization of the data.  The data are organized into databases; but we should not confuse that with the relational database file.  Here, it’s a document database.  A database is a set of documents with common characteristics. 

## Stores
The documents inside a database are organized in stores (see below). A database is physically backed by a set of relational tables in the RDBMS. The store is a container for JSON documents. That allows you to put documents into different “buckets” that have different characteristics, such as different indexing strategies. For example, in a CRM application, you may have one document that is a Customer, and another that is a Product. You would not want to apply the same indexes to these documents.  You can specify further customization for a store, such as whether full-text search is enabled.

###Pre-defined stores
In the database definition are four pre-defined stores. These stores are created automatically by Darwino and can be utilized as-is, but they can also have their definitions overridden and customized (by adding indexes or fields, for example). It is always possible to create specific stores for these stores’ purposes; these pre-defined stores exist as a convenience.
- _default: This store can act as a placeholder. There is no index or field extraction for this store by default, but it can be used for quick-and-dirty storage of data.
- _local: This store is identical to the _default store, except that it is never replicated. It is useful for, among other things, storing device-specific data on a mobile device.
- _comments: The social data Comments are stored here by default. Storing comments here instead of inside the documents themselves avoids having the documents marked as modified every time a user adds a comment. It also avoids replication conflicts on the documents when multiple comments are added in a short period of time. Also, because comments can be complex, even including attachments, storing them here avoids making the documents overly complex.
- _design: Not currently used, this is a special store used to store design elements of the application.


## Document
   Then, there is the document.  In Darwino, a document is four things:
- JSON data. It’s not necessarily a JSON object; it could be a JSON array. Most of the time it will be a JSON object; making it a JSON object is a best practice, because doing so is necessary to allow use of features such as tagging, or reader/editor security. These are system fields added at the root of the document, thus implying that the root object is a document.
- Metadata: the UNID, creation date, last modification date, creator name, last modifier name. There are also several replication-related metadata items, but these are generally not of interest to the developer. These include the last replicated time and the sequence ID used in detecting replication conflicts. 
- Potentially, a set of binary attachments. The attachments consist of binary data identified by name, and with an associated MIME type so that consumers can know what to do with the data. A single Darwino document can have multiple attachments, but the attachment name is the key so there cannot be two attachments with the same name in a single document. Attachments are generally stored in the relational database, in a dedicated table, and referenced from the document. Now, this can be customized and the files can be stored elsewhere, like the file system or a content management system. In addition to the attachment name and pointer, Darwino also stores a length and a size, plus some replication-related information.
- A set of social data.  Darwino, by default, it social-enabled. You can rate a document, you can share a document, you can vote for a document, you can tag a document, and you can comment on a document. The social data is ABOUT the document, but it is not stored IN the document. The social data is stored in a separate table, and it is user-based: if five users rate a document, there will be five records in the social table, one per user, all referencing that document. The table is replicated along with the documents.  There is one exception to this separation of the social data from the document’s JSON data: tags. Most of the social data are action based (vote, rate, etc...) while tags are entered by the user, so they are part of the document fields. Tags are stored in both the document and the social table. In the document, the tags are stored in the field called “_tags”, making them easy for an application to edit. When the document is saved, the values are copied to the social table to enable querying. Comments are stored as child documents since they can have several fields as well as their own attachments.
 
####Document Hierarchy
Documents can be organized hierarchically. Every document can have a reference to a parent document. That reference is stored in the document’s “_parentid” field. This field is accessible for read/write through the API. 

The parent document must be in the same database as the child. There is a specific syntax for this field: when the parent is in the same store, then the value is just the parent document UNID. When the parent is in a different store, then the syntax is UNID:STOREID.

> Note: The system does not enforce the validity of the parent. This can lead to documents pointing to a non-existent parent. In this case, they are called "orphan" documents.

#### Synchronization master documents
Darwino implements “functional replication”. This means that selective replication can be based on changes to an ancestor document, as opposed to the current document.

The synchronization master for a document is the document that is checked for changes when the replicator is testing for selective replication eligibility. When a replication formula is applied on a document for selective replication, it actually applies to the sync master if one is defined. When the sync master document changes, and only when it changes, will the child documents replicate as well. This is how a sync master is used to logically group a set of documents together, so that they get replicated as a whole. For example, child documents might use the root parent document as their synchronization master.

Sync master documents are identified using the same convention as parent documents: when the sync master is in the same store, then the value is its UNID. When the sync master is in a different store, then it is UNID:STOREID.

There is an option for the save() method that forces the master document to update when a document referring to it as master is updated. There are options at the Store definition level as well.
    
 It is not necessary for a synchronization master to be an ancestor; other document relationships could benefit from the ability to specifically define under what circumstances they will replicate as a group. For example: a customer and all the documents related to this customer, or all documents related to a particular project.
 

> Note: While typically it would be, a sync master does not have to be an ancestor of the documents that refer to it as their sync master. There are other relationships besides parent/child that can benefit from the sync master technology; for example, all documents associated with a particular project could be linked in this way, whether or not they are hierarchically related.

####Social Data Updates
The Store's setUpdateWithUserData() method specifies whether the index should be updated when a document’s social data, which is stored outside of the document, is changed, even if the document itself has not been changed. An example of this would be if you are tracking ratings for documents and you wish to display the average rating for each document. Rather than recalculate the average every time you query the index, you would store the rating average every time the ratings are changed. By default, the index is not updated when the social data changes.

setUpdateWithUserData() can also be used to store the number of child documents for a parent document. But this is like recalculating the index of a parent document, while a child is updated (parentId). So there is an option to the save() method that forces the parent document, or the sync master, to update as well.

 
####Document security
Darwino implements multi-level security. You can assign security to the Server object; you can control who can and cannot access the server. At the database level, you can assign an ACL. In the ACL, you can define who can access the database, manage the database, read documents, create documents, delete documents, and edit documents. At the Document level, you can maintain a list of users who can read or read/write the document, within the limits defined by the ACL. For example, if a user is granted only Read access to the database via the ACL, they will be limited to reading a given document even if the document-level security is set to allow Edit rights.

##	UNID
   Documents have two identifiers: the UNID and the docID. The UNID is a string provided either by the API when creating the document or, if it’s empty, it is generated automatically by the system. A UNID must be unique per store; this is enforced by a database unique index. When there are two replicas, for example one on the server and one on a mobile device, the UNIDs will match.

>    Note: The parentID is used to identity the document’s parent. It is the UNID of the parent. Sync Master documents are also identified by their UNIDs.

## docID
   The docID is an integer that is unique inside the database. It is generated by the system; you cannot specify it when you create the document, and it cannot be changed. Being an integer makes it much more efficient in database operations than a string such as the UNID. 

   However, the docID is not universal; it won’t be the same in two different replicas. Because of this, you should not store it for programmatic use and try to rely on its always applying to that document. docIDs are used internally because in the database each document maps to a set of relational tables; the relations between these tables are expressed most of the time through the docID because it’s much more efficient than the UNID. It is also easier and faster to programmatically manipulate large lists of integers.
