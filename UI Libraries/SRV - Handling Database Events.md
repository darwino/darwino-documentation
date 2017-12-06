# Handling database events

An application can hook on the database access an provide its own business logic. The handlers have to be registered in the registry of the database server level (LocalJsonServer). They can apply to any transaction, to transactions specific to a database, or even transaction specific to a store.

Here are the available events:

- When a document is created - queryNew/postNew

  queryNew: to  accept or refuse the document creation
  postNew: to set default values when the document is created
  
- When a document is loaded - queryLoad/postLoad

  queryLoad: to prevent the document from being loaded
  postLoad: to execute an action once the document is loaded

- When a document is saved - querySave/postSave

  querySave: to accept of refuse the document save. The document content can also be updated before it is saved
  postSave: to execute an action once the document is saved

- When a document is deleted - queryDelete/postDelete (only triggered when a document is individually deleted, not with mass deletion, like `deleteAll()`).

queryDelete: to accept of refuse the document deletion
postDelete: to execute an action once the document is deleted
 

		// Add here the database events to register to the JSON store
		registerDocumentEvents(AppDatabaseDef.DATABASE_NAME, Database.STORE_DEFAULT, new DocumentEvents() {
			@Override
			public void queryNewDocument(Store store, String unid) throws JsonException {
				Platform.log("QueryNewDocument, Database={0}, Store={1}, Unid={2}",store.getDatabase().getId(),store.getId(),unid);
			}
