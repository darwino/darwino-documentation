## Services and extensions
There are two elements in the platform: services and extensions. Services and extensions are created lazy, when they are needed for the first time.

Services and extensions are defined by plugins, or by the Darwino Application object. A plugin is a class that can be provided either by libraries or by your application directly, and they can register services and extensions to the platform. 

##Services
A service is a singleton. For a particular class of service, there will be ONE implementation. If there are multiple implementations, you will get a runtime error.

##Extensions
Services and extensions differ in that an extension can have multiple implementations and they can all work together at the same time, like, for example, a database connection. You can have a connection to Postgres and a connection to DB2 and use both of them within the same app. An extension is like a service but it can have multiple instances that are non-exclusive.

The Darwino wizard will generate a skeleton plugin for your application that you can use to override or add to the default platform implementation.

