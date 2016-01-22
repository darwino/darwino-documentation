Darwino DB API
=======================

# Registering and handling events
At the Server Object level, you can register an ExtensionRegistry. This registry provides a set of functions – currently a set of five:
-	BinaryStore: When Darwino stores attachments, they can be stored either inside the database or apart from the database. The BinaryStore is an interface that facilitates storing the attachments outside of the database by providing a set a CRUD methods. The attachments can be stored, for example, directly in the file system or in a CMS.
-	DocumentEvents: When an operation is being performed on a document, the runtime will call the methods in the DocumentEvents:
 - postNewDocument – called right after a document has been created, so that you can, for example, change document values.
 - postLoadDocument – called right after a document has been loaded.
 - querySaveDocument– called immediately before a document has been saved. It is possible to cancel the save from within this event simply by throwing an exception. The exception can include the reason for the save cancelation.
 - postSaveDocument – called immediately after a document has been saved.
 - queryDeleteDocument – called before a document is deleted, EXCEPT when a group of documents is being deleted. Group deletes are performed directly by a SQL statement, and so, for performance reasons this event is not raised.
 - postDeleteDocument – called after a document delete, except, as with queryDocumentDelete, when a group of documents has been deleted. In such a case, you could add a trigger at the relational database level to, for example, log the deletion in a queue for processing.
 
 Note that these events are also raised when called via HTTP. 
 
 Transient properties set at the document level can be accessed from within these events, despite the fact that these properties are never saved. This can be used, for example, by the calling code to pass information to the save event.

-	SynchronizationEvents: As synchronization is taking place, this set of events will be raised, allowing customization of the synchronization actions. Like the DocumentEvents, code here can manipulate values or cancel the action altogether.
 - queryCreateDocument
 - postCreateDocument
 - queryUpdateDocument
 - postUpdateDocument
 - queryDeleteDocument
 - postDeleteDocument
 
 In addition, there are events related specifically to synchronization conflicts. Code here can customize the action to be taken during conflicts.

 - conflictAction – raised when the runtime has detected a synchronization conflict, this event provides information about the conflict, including what changed in the source document and what is in the target document. Code here will return a ConflictAction, which will be one of the following:
  -- DEFAULT – the default handler should be applied
  -- SOURCE – the source should win
  -- TARGET – the target should win
  -- CUSTOM – call the handleConflict method, where the conflict can be handled by custom business logic. For example, in an HR application where several people interviewing an applicant each have access to a different section of the document. In this case, you would choose to merge the different sections.
  
-	FieldFunction: Functions used when extracting fields from documents or computing indexes are registered here.
 
-	InstanceFactoryImpl: A database can have multiple instances, each with its own security configuration; in other words, there is per-instance security. That instance security is dynamic, based on business logic. 

 When a database is opened for a particular instance, then an instance object is created in memory through an instance factory. Once the instance factory has been implemented and the instance is created based on the database and the instance name, the contribute() method of the instance is the mechanism for adding roles and groups to the user context.

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

 You can register events globally, and at the database level, and at the store level. If an event is registered at the store level, that is one that will be called for documents in that store. If, instead, there is no event registered at the store but there is one registered at the database, then the database registration will be in effect. The most-local (most precise) registration is the one that is used.

 This is also the case for registered field functions.
