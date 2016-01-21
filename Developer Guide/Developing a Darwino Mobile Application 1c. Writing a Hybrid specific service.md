##Writing a Hybrid specific service
A hybrid app can be extended by providing services. The services will be available whenever the app is running on the server or locally. It is also possible to create services that are very specific to a hybrid app. For example, accessing the camera is something that makes sense only on a mobile device. To enable this sort of feature, it is possible to create actions that can be triggered from the web app and execute in the native code of the app. 

The JavaScript API contains an object called darwino.hybrid that enables this action mechanism. Its isHybrid() function returns a Boolean indicating whether or not the code is running in a hybrid app. isHybridAndroid() and isHybridIos() do a similar but more specific evaluation. These functions are always available, without regard to whether the code is running in a mobile or a web app.
	
####exec()
The exec() function is the equivalent of the shell() function found in a variety of other languages; it allows the calling of external functions. In this case, “external” means device-native activities. Exec() provides the bridge between the HTML side of the app and the native code.

exec() has four arguments: a verb, a set of arguments to be passed to the verb, a callback, and a Boolean specifying whether it should run asynchronously or not.

It is possible to create custom actions, and there is a set of predefined actions, including:
-	switchToLocal
-	switchToRemote
-	switchToWeb
-	synchronizeData
-	startApplication
-	openSettings

Registering actions is done in AndroidHybridActions, which itself is registered as an extension in AndroidPlugin, via the registerCommands() method. The equivalent methods exist for iOS, and other OS-specific implementations can be provided.

This makes it possible to register a command with a name. For example, to create a command that takes a picture and attaches it to a document, implement the execute method in the AppCommand class using the context to pass the necessary parameters, such as the docID. Because multiple processes may execute commands simultaneously, instance variables shoudn’t be used for storing data; commands should be called with their own local context.

####RPC callbacks
In addition to exec(), it is possible to implement an RPC callback. RPC functions execute synchronously, and they can return a value. Unlike the commands, these functions are only available to hybrid apps.

####JavaScript functions
As with registering commands, it is possible to register JavaScript functions. This is done via the registerFunctions() method in the JavaScriptFunctionExtension class. These functions are the ones called by the RPC mechanism described above.

