# Installing Darwino on a Domino server
Darwino is able to synchronize with a variety of database systems, Domino included. It does this by means of a synchronization service running on the Domino, supported by database adapters that define the field mapping between replicating databases.

There are various support considerations, primarily to do with replication. The darwino.sync service runs on the Domino server to handle inbound replication requests and outbound scheduled replications. It makes Domino appear to the outside world like a Darwino server. It will provide the list of changes since last replication with the requesting server, and then handle the necessary Domino <-> JSON data transformations.

To support this, there is a set of Darwino-enablement plugins (found in the Domino repository in the Darwino space on GutHub. It contains:
-	The NAPI, which is the C API bindings
-	The replicator code
-	Supporting libraries
-	Connections for an XPages application to deal with the replicator and Darwino databases generally

This is installed in basically the same way that an application would be installed. In the Domino server’s Update Site database, choose “Import Local Update Site…” and select the site.xml file. Once the import is complete, restart the HTTP task.
