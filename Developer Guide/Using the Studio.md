Using the Studio
=======================
Creating your first Darwino application

The Wizard generates a set of Maven projects (need screenshot). The top project is the container for the other projects

Which projects are generated depends on the options that were selected in the wizard.
- -shared: This project contains the Java code that is shared by all the platforms. 
-- ++AppDatabaseDef.java++ defines the metadata of the JSON store. Since the store is located inside the database as a JSON file, you won’t need this Metadata definition (need to verify and elaborate on this).
The first time replication runs, the tables will optionally be created automatically. It can also check to ensure that the tables are at the required level. If the database and the DATABASE_VERSION are equal, it will proceed. If the table version is higher than expected, an error will be raised. It the table version is lower, you can upgrade the tables (if autodepoly was selected), or raise an error.
-- ++AppDBBusinessLogic.java++ provides the means to handle database events. Examples include the Document events (create, edit, delete) and database replication events.
-- ++AppManifest.java++ defines the options for the Darwino application layer. The options defined here are shared among all platforms. For examples, the getDatabases() method returns the database names used by the application, and getLabel() returns the database label for J2EE.
-- ++AppServiceFactory.java++ - In a Darwino app, all of the business logic is isolated into services that can be exposed as web services. By default it provides you with a set of services, such as the JSON store, but it also allows you to create your own services. It is in AppServiceFactory where custom application services are defined.
 
 The wizard generates a very basic, example skeleton service that is ready to use.
 
 There is also the RestServiceBinder which is where we define which elements match to which services.

- -webui: This project is generated when the wizard is instructed to create a J2EE or hybrid app. It contains the web artifacts that are consumed by the web application. It’s not under “shared” because we can have apps that are not web apps, for example pure backend apps on the server, or native apps for mobile devices.
 
 It is a basic skeleton using web technologies and angular.js. angular.js is not a requirement, but it is recommended. The index.html file that’s generated includes angular code, but you can delete it and use whatever framework you like.
 
 The web resources are packaged under a folder called darwino-inf under src/main/resources (a Maven convention for where to put resources). 
 
 Darwino comes with a custom service that is able to read the resources inside that directory and act as though they were part of your J2EE project and part of your mobile assets.

- -j2ee: This is a J2EE wrapper to this project. It includes the Java in the Java runtime and it includes the web resources in the runtime (must flesh this out).
 
 The pom.xml defines the dependencies of the project. In our case, it shows that we’re including the demo-app-shared and demo-app-webui, thus all the content in both projects will be included.
 
 In the web.xml, we can point to Darwino artifacts, which are either servlets or filters. Several of these are of particular importance. The DarwinoJ2EEFilter, that handle on-the-fly transformation of url requests, allowing us to alter our folder structure without having to modify code to accommodate the changes, and making our urls platform-independent).
 
 Defined in web.xml:
-- DarwinoAppResourcesServlet
-- DarwinoJ2EEFilter: When a request comes in, this is the first filter to be activated. It retrieves the current context and puts it on the stack so it becomes available to your application. It does this for every single URL.
-- DarwinoGlobalPathRewriterFilter serves to translate the $darwino-libs keyword in urls to the correct path, rewriting file system paths on the fly. This makes the url completely platform-independent.
-- Darwino services: for example, built-in like the JSON store, or custom (user-created) services served by the DarwinoServiceDispatcher filter.
 
 In order for the highest level, the Darwino app, to have the context it needs available, at application initialization the com.demo.app.AppContextListener is triggered before anything else.
 
 It provides the application with access to all of its environment information
As a listener, it cannot pass parameters, but the application can have global parameters, so the listener uses global parameters to pass context information
 
 Authentication
The wizard generates code allowing the use of J2EE authentication, but that requires the J2EE server to be connected to your directory of users and every web application server has its own mechanism for doing that; this does not lend itself to true portability. Also, J2EE authentication lacks granularity.
Darwino comes with its own authentication filter than you can choose to use. This allows us to use our own user directory regardless of the application server.
(how does Darwino improve on granularity?)
 
 There are two other files in WEB-INF that are critical in solving the problem of configuring application parameters such as database connections and logging. Rather than storing this configuration within the application itself, we can externalize the configuration. This allows configuration changes to be made without recompilation. It’s up to the developer to decide where these files are stored, but, in keeping with J2EE convention, Darwino stores these files in the WEB-INF directory.
 
 - darwino-beans.xml
  These are Java objects, and, as managed beans, the platform will automatically create instances of these objects when needed.
  Contains sections for: (screenshots could help here)
  -- Database access
  -- IBM Connections endpoint
  -- HttpTracer – allows tracing of all aspects of the communication between client and server
  -- Static directory of users
   
   Each bean has a type and a name, and can have an alias list, which can include the alias “default”. 
(include screen shot of contents of the Darwino-beans.xml file.
 
 - darwino.properties
 There is an API in Darwino to get these property values.

- -mobile: doesn’t run anywhere. Theis project is a container for resources that are common to both mobile platforms (iOS and Android).
AppMobileManifest.java is like AppManifest, but specifically for mobile devices. It adds a set of options that are very specific to mobile devices. The wizard provides only the basic skeleton. The developer puts the common mobile code and resources here.

- -android-hybrid: Depending on wizard choices, you can have an android-hybrid and/or and android native. Typically you’ll choose one and not both. We are not showing android native in the demos.
This project depends on all the others except the J2EE app.
Includes all the android assets, such as the AndroidManifest.xml.
It is exactly like a project you would generate with the Android wizard, except it includes a set of dependencies on the Darwino project.
AndroidApplication.java launches the Darwino application as the first thing it does.
DarwinoApplication.java: two very important objects:
- DarwinoApplication is a singleton that acts as the entry point for all of the objects of the Darwino application, including the manifest, and it enables the triggering of Darwino application actions such as replication.
- DarwinoContext, which is generated by the Darwino runtime. It has a different behavior depending on the platform, but gives access to the same information.
 
 A mobile environment is single-user, while the server environment is multi-user. In both cases, there will be one Darwino application which is exactly the same regardless of the user, however the DarwinoContext on the J2EE server is the context of the current request (“Who is the user for this request? What is the contextual environment–the execution environment–of this request?”). On the server, there is one new DarwinoContext created per request. On the mobile device, none of this applies.
 
 Of course, if you want to just use the JSON API to get this context info, you can, but these objects make the job much easier.
 
 DarwinoServiceDispatcher: this is the class that is used by the HTTP Server to dispatch the services
By default, all services are enabled, but with DarwinoServiceDispatcher we can selectively enable and disable services.
 
 MainActivity –creates the canvas on which the application runs.
(I couldn’t make this out AT ALL. It’s at 1:07 in.)
 
 SplashScreenActivity provides the splash screen. Not much to say about this.

- -robovm-hybrid: This is the iOS version of the app.
We’re writing Java for iOS, but Java is not supported natively by iOS, so we rely on 3rd-party frameworks. We currently support only RoboVM, but we may support more in the future, such as Intel INDE.
 
 Just as we generate an Android wrapper, we generate a RoboVM one. It just like what you’d get if you generated it with a RoboVM widget. The contained classes match those of the android-hybrid, with MainViewController equating to MainActivity.