# Darwino application objects

## Application
The Darwino application is a singleton. There is one and only one running instance. The Application instance is the entry point for all of the application options, including the manifest (the Application's getManifest() method will return the manifest object).

It is through the Application object that we can perform application-wide actions such as triggering replication.
