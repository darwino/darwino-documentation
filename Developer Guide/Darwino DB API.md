Darwino DB API
=======================

- 1	Concepts
	-- Server, Session, Database, Store, Indexes
	-- UNID, DocId
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
- 9	REST API
- 10	Darwino API over HTTP
