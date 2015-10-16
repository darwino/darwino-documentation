## Services and extensions
There are two elements in the platform: services and extensions. A service is a singleton. For a particular class of service, there will be ONE implementation. If there are multiple implementations, you will get a runtime error.

##Services
Services and extensions differ in that an extension can have multiple implementations and they can all work together at the same time, like, for example, a database connection. You can have a connection to postgres and a connection to DB2 and use both of them within the same app. An extension is like a service but it can have multiple instances that are non-exclusive.


##Extensions
To define extensions, there is the notion of plugins. A plugin is a class that can be provided by libraries or by your application directly, and they can register services and extensions to the platform. 

The Darwino wizard will generate a skeleton plugin for your application that you can use to override or add to the default platform implementation.

