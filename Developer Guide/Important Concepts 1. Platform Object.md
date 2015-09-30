# Platform object
Contained within Darwino-platform is dwo-commons. This is where we find a lot of the things that are common across all the platforms and the activity classes. 
    
In here is the platform object (Platform.java), which provides access to the platform characteristics and platform extensions. We have access here to many cross-platform characteristics, such as logging, or accessing the console, and accessing managed beans. We can perform a request like Platform.getProperty(“propertyname”), and it will know how to get the requested property regardless of platform. 
    
It also includes the devMode flag that is used globally.  
    
In addition to the Platform object, there are a number of important services here, such as the ManagedBeanService, PropertyService, ConsoleService, and the SingletonService. A singleton is an object that is created once. Programmers will create a singleton and put it into a static variable as a class, and they will believe that it is created once and is there. This is not true if the libraries and same classes are loaded and shared, as in J2EE. If your jar files are in shared libraries, or as in Darwino, where there is one deployment shared by all the apps. What you think is a singleton will be changeable by other apps. The singleton service creates a singleton per running app.
