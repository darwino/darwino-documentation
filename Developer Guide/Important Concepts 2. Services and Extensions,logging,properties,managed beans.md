## Services and extensions
There are two elements in the platform: services and extensions. A service is a singleton. For a particular class of service, there will be ONE implementation. If there are multiple implementations, you will get a runtime error.

Services and extensions differ in that an extension can have multiple implementations and they can all work together at the same time, like, for example, a database connection. You can have a connection to postgres and a connection to DB2 and use both of them within the same app. An extension is like a service but it can have multiple instances that are non-exclusive.

## Logging
Darwino provides its own logging library for each platform, and dynamically chooses the appropriate one for current platform.  This makes it completely transparent to the developer.