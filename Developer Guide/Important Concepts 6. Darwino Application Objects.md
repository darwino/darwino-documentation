# Darwino application objects

## Application
The Darwino application is a singleton. There is one and only one running instance. The Application instance is the entry point for all of the application options, including the manifest (the Application's getManifest() method will return the manifest object).

It is through the Application object that we can perform application-wide actions such as triggering replication.

Mention the get method, which returns a singleton for the Darwino app. It will throw an exception if it doesn’t exist. If you’d prefer to test it, you can instead call getUnchecked, which will return null if it doesn’t exist. This pattern of get/getUnchecked occurs all over the place, like in DarwinoContext.

## Manifest
The Application Manifest defines the options for the Darwino application layer. The options defined here are shared among all platforms.

## Context
The Darwino Context provides the application with access to all of its environment information, including the user identity and the properties of the execution environment.

The COntext has a different behavior depending on the platform, but it provides access to the same information.