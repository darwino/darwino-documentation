Darwino DB API
=======================


# Documents and CRUD operations
It is possible to perform create, read, update, and delete operations on documents.

At the base, the Darwino API is a Java API. There is also a JavaScript API which is a JavaScript flavor of the Java API, and there are the REST services.

You can load and create documents either from the database or from the store. Remember that the UNID is unique per store and the DocID is unique per database.

```
// Create documents
Document d1 = store.newDocument(); d1.save();
Document d2 = store.newDocument(); d2.save();
Document d3 = store.newDocument(); d3.save();

// Read a document
Document doc = store.loadDocument("1000");
s += ">> Document\n";
s += "  Unid: "+doc.getUnid()+"\n";
s += "  Id: "+doc.getDocId()+"\n";
s += "  Json: "+doc.getJsonString(false)+"\n";
_formatText("{0}",s);

// Update a document
JsonObject json = (JsonObject)doc.getJson();
json.put("NewField","This is a new field");
json.put("field1","This is an update field - Previous was: "+json.getString("field1"));
doc.save();

// Delete documents
// Call remove() from the Document object
d1.deleteDocument();
// Call delete() from the Store, using a UNID
store.deleteDocument(d2.getUnid());
// Call delete() from the Database, using a DocID
store.getDatabase().deleteDocumentById(d3.getDocId());
```

When loading an existing document, there are a number of options you can pass.

```
Document doc = store.loadDocument(unid, options); // the unid is a string, and options is an int.
```
Document loading options (these are ORed):
- DOCUMENT_NOREADMARK - This will load the document but not mark it as read. Normally, the read mark is checked when using the loadDocument() method, but not when the document is accessed via a query. Examples of when you might want to leave the read mark untouched include:
 - when it is a background process loading the document, and thus the user is not personally reading the document
 - when there is no solid reason to mark the document read, and you want to save the database write that marking the flag entails
- DOCUMENT_CREATE - By default, when you attempt to load a document that does not exist, you will get an exception. If you pass this flag, then if the document does not exist a document will be created in memory and no exception will be thrown. The new document will not appear in the database until you explicitly save it. This is particularly useful when using a remote connection, as it saves a server connection. It acts as LoadOrCreate().

There are also options available when saving a document:
- SAVE_NOREAD - When a document is being created (and ONLY then), will not mark the document as read. By default, a new document is marked as read by the user who created it.
- SAVE_NOTOUCH - It is possible to specify, at the store level, that parent or sync master documents, and, optionally, their indexes and all progenitor documents, should be marked as modified ("touched") when one of their dependent documents is modified. Saving with the SAVE_NOTOUCH option will prevent the touch actions from occuring.
- SAVE_CHECKCONFLICT - If enabled, the save will not occur and an exception will be raised if the document's last modification date doesn't match what it was when the document was loaded. This would  be the case if the same document has been saved by a different process/user after your code opened it.

Delete document options:
- DELETE_ERASE - When replication is enabled for a database, deleting a document will, by default, create a deletion stub to represent a deleted document so that the document will be deleted in replicas of the database during replication. Using the DELETE_ERASE flag during deletion will delete the document without leaving a deletion stub. Thus, the deletion will not replicate out, and the document will return the next time the database replicates with a copy of the database in which the document exists.
- DELETE_NOTOUCH - Like SAVE_NOTOUCH, this will leave related documents unnotified of the deletion.
- DELETE_CHILDREN - The option will recursively delete all descendents of the document along with the document itself. Since these deletions are performed within a transaction, it's all-or-nothing. You will not be left with a partially-intact document family. Also, they are all done within the same network process, so there's no wasteful back-and-forth that would be required if the deletes were done one at a time.
- DELETE_SYNCSLAVES - This is like DELETE_CHILDREN, but applies when you are deleting a sync master document. It and all of its sync slaves will be deleted.

## Transactions
To do CRUD operations, you get the session and, from that, the database and store, and there you create, read, update, and delete documents. As long as you’re using the Java API locally, you can execute a series of operations within a transaction. At the session level, you can query whether the session supports transactions. If it does, you can start a transaction, perform a set of operations on documents, and then roll back the changes if needed. 

A transaction must be started (startTransaction()) and ended (endTransaction()). To get the transaction committed, one must call commitTransaction() in between start and end. Failing to do so, or explicitly calling abortTransaction(), will result in no changes being committted to the database. 

```
// Simple transaction
session.startTransaction();
try {
	Document d1 = store.newDocument(); 
	id1 = d1.getUnid(); 
    d1.save();
  	session.commitTransaction();
} finally {
	session.endTransaction();
}  
```
Moreover, transactions can be stacked. In order to be committed, all the sub-transactions of the main transaction must be committed, else the entire top transaction will roll back. 

```
session.startTransaction();
try {
	Document d1 = store.newDocument(); 
	id1 = d1.getUnid(); 
    d1.save();
  
    // Aborting a nested transaction will abort the whole transaction!
    session.startTransaction();
    try {
		Document d2 = store.newDocument(); 
		id2 = d2.getUnid(); 
    	d2.save();
		session.abortTransaction();
	} finally {
		session.endTransaction();
	}  
  
    session.commitTransaction();
} finally {
	session.endTransaction();
}  
```

## Access to the documents
There are three ways in Darwino to access documents:
 - The Java API. This API talks directly to the database whenever it is a JDBC-based database, or is SQLite.
 - REST services, which operate on top of the Java API. The set of REST services in Darwino support everything the Java API allows in regard to document operations EXCEPT transactions. Because REST is stateless, transactions, which are stateful, cannot be supported.
 - REST services wrapped for particular languages. Darwino provides two wrappers to assist in REST service work: a Java wrapper and a JavaScript wrapper.
The Java wrapper is the Java API, but instead of being implemented to deal directly with the JDBC driver locally, it deals with the REST services. Nonetheless, it is the exact same API. The only difference is how you get access to the session.
The JavaScript wrapper in intended for use in a JavaScript environment such as a browser or a server-side JavaScript environment like node.js.
In the future there could be other wrappers, just as PHP bindings, Ruby binding, etc…

## Access to the JSON content
 The JSON document in Darwino is more than a JSON object; it has several components:
 - JSON content – typically a JSON object, but it could also be a JSON array. It is unusual for it to be anything other than a JSON object, because only the JSON object supports use of system data that can be of use to Darwino.
 - optionally, attachments
 - metadata – For example, the UNID and the docID, modification date and modification user, as well as tags and other social data, and security-related fields such as READER and EDITOR fields which define which people and groups can access the document. These metadata are not modifiable manually by normal operations. 
 - system data that can be modified – For example, a list of tags. System fields are actually fields in the root of the JSON content object, but their names start with an underscore.
optionally, transient property fields that can contain data we want to pass to document events but never want to store in the document. They are never saved. They can be set and read at the document level, but they are never persistent. A typical use of this is when you want to pass information to an event handler without having this information be part of the document.

### JSON content access methods
Methods are provided for accessing the JSON content in all data types. They all take a String as their sole parameter, and return the value of the requested JSON field, assuming that the content is a JSON object:
 - getString()
 - getInt()
 - getLong()
 - getDouble()
 - getBoolean()
 - getDate()

These methods cannot access hierarchical data, but they are very convenient for accessing fields that are at the root of the document.

 In addition, there is a method for executing JSONPath (XPath for JSON) expressions. JSONPath simplifies the extraction of data from JSON structures. It permits dot notation and bracket notation, and allows wildcard querying of member names and array indices. It is documented [here](http://goessner.net/articles/JsonPath/). JSONPath expressions can be executed in Darwino via the jsonPath method:
 - jsonPath(Object path)

	
##	Managing attachments
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

	


