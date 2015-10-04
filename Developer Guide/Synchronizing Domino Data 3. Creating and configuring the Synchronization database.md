# Creating and configuring the Synchronization database
In the repository is a darwinosync.ntf file. The Darwino replicator expects there to be a database based on this template, and named darwinosync.nsf, in the root of the Domino data directory. The default title for this database is “Darwino Sync Admin”.

There is no proper Notes UI for this database; it is intended to be maintained via its XPages UI. This database serves several purposes:
-	It acts as a repository for the replication history stubs. When Darwino replicates with Domino, a stub document will be created here to record the adapter name, foreign server identity, database, and replication time. These documents are keyed by server name, allowing the database to be replicated among Domino servers without confusion as to the identity of the replicating Domino servers’ identities.
-	It holds the replication adapter definitions. Adapters map the incoming Darwino field definitions to Domino fields. Beyond mapping fields one-to-one, it defines the rules for transforming data from one database system to another. For example, JSON lacks the concept of a date, and so dates coming into Domino need to be recognized as such and stored in a Notes DateTime field. It is also possible to do arbitrary transformations.
-	It defines the replication schedule used by the Darwino replicator service when replicating out from Domino to Darwino. For outbound replication, the service is controlled by Replication Schedule documents that will be familiar to anyone who ever configured replication in a Domino Connection document.

(We should have a screen shot of a Replication Schedule document, but only after the UI is settled.)

