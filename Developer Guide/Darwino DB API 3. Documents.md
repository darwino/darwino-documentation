Darwino DB API
=======================


# Documents
## CRUD operations
It is possible to perform create, read, update, and delete operations on documents.
	At the base, the Darwino API is a Java API. There is also a JavaScript API which is a JavaScript flavor of the Java API.  To do CRUD operations, you get the session and from that the database and store, and there you create, read, update, and delete documents. As long as you’re using the Java API locally, you can execute a series of operations within a transaction. At the session level, you can query whether the session supports transactions. If it does, you can start a transaction, perform a set of operations on documents, and then roll back the changes if needed. This is familiar to users of many relational databases, but is alien to Domino and mongoDB.

 There are three ways, in Darwino, to access documents:
 - The Java API. This API talks directly to the database whenever it is a JDBC-based database, or is SQLite.
 - REST services, which operate on top of the Java API. The set of REST services in Darwino support everything the Java API allows in regard to document operations EXCEPT transactions. Because REST is stateless, transactions, which are stateful, cannot be supported.
 - REST services wrapped for particular languages. Darwino provides two wrappers to assist in REST service work: a Java wrapper and a JavaScript wrapper.
The Java wrapper is the Java API, but instead of being implemented to deal directly with the JDBC driver locally, it deals with the REST services. Nonetheless, it is the exact same API. The only difference is how you get access to the session.
The JavaScript wrapper in intended for use in a JavaScript environment such as a browser or a server-side JavaScript environment like node.js.
In the future there could be other wrappers, just as PHP bindings, Ruby binding, etc…

## Access to the JSON content
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


##	Document Metadata - System data in the JSON document
(Question: I think this is covered in pieces elsewhere. What should we say specifically here?)

##	Response documents
The parentid field contains the unid of a document’s parent, if there is one. This is how a hierarchy of documents if defined in Darwino. The data integrity of this relationship is not enforced; it is possible to have “orphan” documents– documents with a parentid that is not valid.
	
## Synchronization master document
Darwino implements “functional replication”. This means that selective replication can be based on changes to an ancestor document, as opposed to the current document. The synchronization master for a document is the document that is checked for changes when the replicator is testing for selective replication eligibility. For example, child document might use the root parent document as their synchronization master. Then, when the root document changes, and only when it changes, will the child documents replicate as well. 
    
 It is not necessary for a synchronization master to be an ancestor; other document relationships could benefit from the ability to so specifically define under what circumstances they will replicate as a group.

