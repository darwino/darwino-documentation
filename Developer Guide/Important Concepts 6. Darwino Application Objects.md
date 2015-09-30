# Darwino application objects
## Application
Mention the get method, which returns a singleton for the Darwino app. It will throw an exception if it doesn’t exist. If you’d prefer to test it, you can instead call getUnchecked, which will return null if it doesn’t exist. This patter of get/getUnchecked occurs all over the place, like in DarwinoContext.
## Manifest
The Application Manifest defines the options for the Darwino application layer. The options defined here are shared among all platforms.
## Context
The Application Context provides the application with access to all of its environment information, including the user identity and the properties of the execution environment.