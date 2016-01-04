Darwino DB API
=======================

# Defining and deploying the database
A Darwino database is actually a set of tables within a relational database. The schema of these tables does not depend on the Darwino application. The schema is defined by Darwino, and the always remains the same for each datanase, only the table names depend on the database name. This is important because in some organizations altering tables’ schema is strictly controlled. They require that a new DDL be submitted for the administrator to apply. With Darwino, no new table definitions are necessary… UNLESS you want to add additional indexes. See [Optimizing the database](Optimizing the database.md) for details on defining additional indexes. 


## Configuring the database using managed beans
   There are two very important points in the Darwino philosophy here.
 
 The first is that Darwino is built in layers, and you pick the the layer you want to use.  It’s better and more effective when you pick the highest-level one.
 
 The second is that there is nothing that is hard-coded.  Everything is provided by extension points. Extension points can rely on managed beans. For example, the connection to the database is defined through beans. But this is not the only way to define your connection. There is an extension point for defining your connection, and one default implementation of the extension point is looking for beans. Everything in Darwino is built upon extensions, and to find the extensions there is the notion of a plugin. A plugin is a class that can be provided by your application or by a library and that add implementation for extensions. Extensions can be contextual to a platform or not. For example: the location where managed beans are found is provided by an extension point, because on a mobile device you won’t find the beans at the same place as on a J2EE server. On the server, this is done via the J2eePlatform.java plugin.
 
 See [Services and Extensions](Important Concepts 2. Services and Extensions.md) for more information on plugins.