# Darwino application objects

Darwino runtime objects exist for every Darwino application and are created appropriatly by the runtime.
They are organized in a class hierarchy based on the runtime platforms. The highest class in the hierarchy represents the features commons to all platforms, while the deepest ones are specific to the runtime platform.

Typically, the hierarchy looks like:

- Darwino
    - DarwinoJ2EE
    - DarwinoMobile
        - DarwinoIOS
        - DarwinoAndroid
        
## Application
The Darwino application is a singleton. There is one and only one running instance. The Application instance is the entry point for all of the application options, including the manifest (the Application's getManifest() method will return the manifest object).

It is through the Application object that we can perform application-wide actions such as triggering replication.

At any time, the developer can get access to the current application by calling DarwinoApplication.get(). If you know the platform you're running in, and if you want to get access to platform specific features, then you can access the specific application by casting the application object to the platform specific class, or by calling get() on this class (ex: DarwinoJ2EEApplication.get())