Important Darwino Concepts
=======================

As a developer, there are a number of concepts that you must understand. These are:  
-The Platform Object  
-Services and Extensions  
-The JSON Library and data binding  
-The HTTP Client  
-REST Services APIs  
-Darwino Application Objects


- Platform object
	Contained within Darwino-platform is dwo-commons. This is where we find a lot of the things that are common across all the platforms and the activity classes. 
    
	In here is the platform object (Platform.java), which provides access to the platform characteristics and platform extensions. We have access here to many cross-platform characteristics, such as logging, or accessing the console, and accessing managed beans. We can perform a request like Platform.getProperty(“propertyname”), and it will know how to get the requested property regardless of platform. 
    
	It also includes the devMode flag that is used globally.  
    
	In addition to the Platform object, there are a number of important services here, such as the ManagedBeanService, PropertyService, ConsoleService, and the SingletonService. A singleton is an object that is created once. Programmers will create a singleton and put it into a static variable as a class, and they will believe that it is created once and is there. This is not true if the libraries and same classes are loaded and shared, as in J2EE. If your jar files are in shared libraries, or as in Darwino, where there is one deployment shared by all the apps. What you think is a singleton will be changeable by other apps. The singleton service creates a singleton PER RUNNING APP.
    
- Services and extensions
There are two things in the platform: services and extensions. A service is a singleton. For a particular class of service, there will be ONE implementation. If there are multiple implementations, you will get a runtime error.
Services and extensions differ in that an extension can have multiple implementations and they can all work together at the same time, like, for example, a database connection. You can have a connection to postgres and a connection to DB2 and use both of them within the same app. An extension is like a service but it can have multiple instances that are non-exclusive.
	-- Logging - Darwino provides its own logging library for each platform, and dynamically chooses the appropriate one for current platform.  This makes it completely transparent to the developer
	-- Properties
	-- Managed beans


- JSON library and data binding (Jesse is doing the Javadoc for this)
A lot of Darwino is based on top of JSON and particularly JSON store. We use our own library for JSON because there is no standard JSON library in Java. There are libraries available, but they tend to be poor performers, or cumbersome to use, or just heavy-weight. Their dependencies make for heavy mobile app code. The Darwino library is based on top of one written at IBM. It is 100% compatible with the JSON standard, and has been optimized for JSON parsing, and it includes JSON extensions. 

 In standard JSON, we have key/value pairs. Keys must be wrapped in double quotes. JavaScript, though, allows single quotes or key names with no quotes at all. The Darwino JSON parser is permissive and allows those. When it serializes, it does so according to JSON standard, but when it reads, it is permissive.
 
 Also, in JSON you cannot insert commands. Command insertion is available in JavaScript, but not in JSON. The Darwino parser permits commands when reading, but does not, by default, emit them.
Use case: A progress bar. When the client tells the server that it supports commands, the server can emit comments in the JSON that describe activity progress. The client can use those progress comments to display a progress bar.
 
 We can also emit binary JSON in place of text. Darwino does not use this externally, as when writing to a file or to a database, but it can be used when communicating via http. For example, when the client is replicating with the server, it uses a REST API that is based on JSON. If the client sends the server a header saying that the client understands the binary form of JSON, then it can compress the data. Values are compressed, and names can be sent once and subsequently only pointed to. Also, you don’t have to parse the data at all. (Must expand on this topic) 
 
 There is a query language for JSON, so you can apply a query to a JSON document and get a result. (Must expand on this topic)

- HttpClient - Because a lot of things are based on services, we need to connect to those services. We need to connect to them from Java, and not only from the browser. The HttpClient here is very easy to use. It has easy-to-use classes for authentication and for handling JSON. For example, to call a REST service, pass some parameters, and get a result back, just call getAsJSON. It will handle the job of composing the proper URL and processing the result.
 
 The developer doesn’t have to worry about the underlying http client (for Apache, regular JVM client, OkHttp.)
 
 Also handles GZIP, ChunkedPost, multi-part MIME… and see postAsJSON.


- REST service APIs – this is about running services on servers and on mobile devices when you’re in hybrid mode. There's no meat here yet. 

- Darwino application objects (see Using the Studio details)
-- Application – mention the get method, which returns a singleton for the Darwino app. It will throw an exception if it doesn’t exist. If you’d prefer to test it, you can instead call getUnchecked, which will return null if it doesn’t exist. This patter of get/getUnchecked occurs all over the place, like in DarwinoContext.
-- Manifest
-- Context
