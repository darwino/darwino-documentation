# Running database triggers

Darwino can monitor, asynchronously, the changes that happen in a database. Then, it can execute an action like a notification for every single change. It can be used, for example, to execute some workflow.

The event being detected is called a trigger. The action to execute from a trigger is called an handler.

## Darwino triggers
All the triggers inhertot from the same EventTrigger class. There are multiple, JSON store related, pre-defined trigger classes provided by the platform:

- JsonStoreDocumentQueryTrigger

  Executes a query and triggers an handler for every single document returned by the query. The check is executed on a regular basis through a scheduled task.
- JsonStoreEventTrigger

  Check, on a regular basis, if some changes happened in a database (document created, updated or deleted). It then triggers the handler for every single change

They are more triggers, like for example Notes/Domino triggers, but they are out of scope for this tutorial.

## Create a trigger and associate an handler

The handler class is related to the trigger it self. Here is an example of an handler that checks for database changes:

    public class LogHandler implements JsonStoreChangesTrigger.DocHandler {
	@Override
	public void handle(Document doc) throws JsonException {
	    Platform.log("Handling document, store={0}, id={1},
	        document form{2}",doc.getStore().getId(),
	        doc.getUnid(), doc.get("form"));
        }
    }

Then the handler shoud be associated to a trigger, like this:

    StaticEventBuilder triggerList = new StaticEventBuilder();
    triggerList.add(new JsonStoreChangesTrigger()
	.scheduler("10s")
	.database(AppDatabaseDef.DATABASE_NAME)
	//.store(Database.STORE_DEFAULT)
	.maxEntries(10) // For demo purposes, only process the last 10 docs...
	.handler(new LogHandler())
    );		
    JsonStorePersistenceService svc = new JsonStorePersistenceService()
	.database(AppDatabaseDef.DATABASE_NAME)
	.category("trigger");
    triggers = new EventBuilderFactory(triggerList,svc);
    triggers.install();

As the trigger needs to store what was the last time it checked for changes, it uses a `PersistenceStore` for this purpose. This implementation stores the data in the `local` store (not replicated) of the database. Other `PersistenceStore` implementations can be used if necessary,

See: `LogHandler.java`
See: `AppContextListener.java`

## Other examples
The Darwino discussion database also shows how to use query triggers, calling the IBM Watson APIs when new documents are created.