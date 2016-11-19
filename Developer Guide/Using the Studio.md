Using the Studio - Creating your first Darwino application
==========================================================

##The Darwino Application Wizard
The Darwino application wizard is the first step in creating a Darwino application.

![](<select-wizard.png>)

The Wizard generates a set of Maven projects. The top project is the container for the other projects.

![](<demo-app.png>)

Which projects are generated depends on the options that were selected in the wizard.

- -shared: This project contains the Java code that is shared by all the platforms. 
![](<shared-contents.png>)

 -- AppDatabaseDef.java defines the metadata of the JSON store. This metadata is used when the database is initially created, and then anytime you need to make a change. 
 
 The first time replication runs, the tables will optionally be created automatically. It can also check to ensure that the tables are at the required level. If the database and the DATABASE_VERSION are equal, it will proceed. If the table version is higher than expected, an error will be raised. If the table version is lower, you can upgrade the tables (if autodeploy was selected), or raise an error. 
 
  In the enterprise, it is often the case that the database developer does not have the authority to create and modify tables on the J2EE server; that task is restricted to the database administrator. To accomodate this, Darwino provides the AppDDLGenerator, a class that will create the DDL text file that can then be given to the administrator for processing. The DDL file is created considering the database definition, any customizations, and the database provider. The developer will run the AppDDLGenerator in Eclipse, and it will produce the DDL file in the console.

 -- AppDBBusinessLogic.java provides the means to handle database events. Examples include the Document events (create, edit, delete) and database replication events.

 -- AppManifest.java defines the options for the Darwino application layer. The options defined here are shared among all platforms. For example, the getDatabases() method returns the database names used by the application, and getLabel() returns the database label for J2EE.

 -- AppServiceFactory.java - In a Darwino app, all of the business logic is isolated into services that can be exposed as web services. By default it provides you with a set of services, such as the JSON store, but it also allows you to create your own services. It is in AppServiceFactory where custom application services are defined.
 
 The wizard generates a very basic, example skeleton service that is ready to use.
 
 There is also the RestServiceBinder which maps URLs to different services, in a platform/library independent manner.

- -webui: This project is generated when the wizard is instructed to create a J2EE or hybrid app. It contains the web artifacts that are consumed by the web application. It’s not under “shared” because we can have apps that are not web apps, for example pure backend apps on the server, or native apps for mobile devices.
 
 It is a basic skeleton using web technologies and AngularJS. AngularJS is not a requirement, but it is recommended. The index.html file that’s generated includes AngularJS code, but you can delete it and use whatever framework you like.
 
 The web resources are packaged under a folder called DARWINO-INF under src/main/resources (a Maven convention for where to put resources). 
 
 Darwino comes with a custom service that is able to read the resources inside that directory and act as though they were part of your J2EE project and part of your mobile assets.

- -j2ee: This is a J2EE wrapper to this project. It includes the Java in the Java runtime and it includes the web resources in the runtime.
 
 The pom.xml defines the dependencies of the project. In our case, it shows that we’re including the demo-app-shared and demo-app-webui, thus all the content in both projects will be included.
 
 In the web.xml, we can point to Darwino artifacts, which are either servlets or filters. Several of these are of particular importance. The DarwinoJ2EEFilter handles on-the-fly transformation of url requests, allowing the folder structure to vary without requiring code modifications to accommodate the changes, and making urls platform-independent).
 
 Defined in web.xml:
 - DarwinoAppResourcesServlet
 - DarwinoJ2EEFilter: When a request comes in, this is the first filter to be activated. It retrieves the current context and puts it on the stack so it becomes available to the application. It does this for every URL.
 - DarwinoGlobalPathRewriterFilter translates all of the URLs containing $darwino-xxx/... into /.darwino-xxx/... paths. All the predefined Darwino services are now mapped to /.darwino-<service name>. This makes the url completely platform-independent.
 - Darwino services: for example, built-in services like the JSON store, or custom (user-created) services served by the DarwinoServiceDispatcher filter.
 
 In order for the highest level, the Darwino application, to have the context it requires, at application initialization the com.demo.app.AppContextListener is triggered before anything else. It provides the application with access to all of its environment information. As a listener, it cannot pass parameters, but the application can have global parameters, so the listener uses global parameters to pass context information
 
###Authentication
The wizard generates code allowing the use of J2EE authentication, but this form of authentication requires the J2EE server to be connected to your directory of users, and every web application server has its own mechanism for doing that; this approach does not lend itself to true portability. Also, J2EE authentication lacks granularity.

 Darwino comes with its own authentication filter than you can choose to use. This allows us to use our own user directory regardless of the application server.
 
 There are two other files in WEB-INF that are critical in solving the problem of configuring application parameters such as database connections and logging. Rather than storing this configuration within the application itself, we can externalize the configuration. This allows configuration changes to be made without recompilation. It’s up to the developer to decide where these files are stored, but, in keeping with J2EE convention, Darwino stores these files in the WEB-INF directory.
 
 - darwino-beans.xml
  These are Java objects, and, as managed beans, the platform will automatically create instances of these objects when needed.
  Contains sections for:
  - Database access
  - IBM Connections endpoint
  - HttpTracer – allows tracing of all aspects of the communication between client and server
  - Static directory of users
   
   Each bean has a type and a name, and can have an alias list, which can include the alias “default”. 
 
 - darwino.properties
 There is an API in Darwino to get these property values.

- -mobile: This project doesn’t run anywhere. It is a container for resources that are common to the mobile platforms (currently iOS and Android).
AppMobileManifest.java is like AppManifest, but specifically for mobile devices. It adds a set of options that are very specific to mobile devices. The wizard provides only the basic skeleton. The developer puts the common mobile code and resources here.

- -android-hybrid: Depending on wizard choices, you can have an Android-hybrid and/or and Android native. Typically you’ll choose one and not both. We are not showing Android native in the demos.
This project depends on all the others except the J2EE app. It includes all the Android assets, such as the AndroidManifest.xml. It is exactly like a project generated with the Android wizard, except it includes a set of dependencies on the Darwino project.

 AndroidApplication.java is a core Android SDK object that initializes the Darwino Application object, as well as the context.

 DarwinoApplication.java contains two very important objects:
 -  DarwinoApplication is a singleton that acts as the entry point for all of the objects of the Darwino application, including the manifest, and it enables the triggering of Darwino application actions such as replication.
 - DarwinoContext, which is generated by the Darwino runtime. It has a different behavior depending on the platform, but gives access to the same information.
 
 A mobile environment is single-user, while the server environment is multi-user. In both cases, there will be one Darwino application which is exactly the same regardless of the user, however the DarwinoContext on the J2EE server is the context of the current request (“Who is the user for this request? What is the contextual environment–the execution environment–of this request?”). On the server, there is one new DarwinoContext created per request. On the mobile device, none of this applies.
 
 Of course, if you want to just use the JSON API to get this context info, you can, but these objects make the job much easier.
 
 DarwinoServiceDispatcher: this is the class that is used by the mobile local HTTP Server to dispatch the services. By default, all services are enabled, but with DarwinoServiceDispatcher we can selectively enable and disable services.
 
 MainActivity: This is an Android SDK object. This implementation creates an embedded Android browser.
 
 SplashScreenActivity provides the splash screen.

- -moe-hybrid: This is the iOS version of the app.
We’re writing Java for iOS, but Java is not supported natively by iOS, so we rely on 3rd-party frameworks. We currently support Multi OS Engine (MOE). As of Darwino 1.5, RoboVM is no longer supported as the product as been acquired and ceased by Microsoft.
 
 Just as we generate an Android wrapper, we generate a MOE one. The contained classes match those of the android-hybrid, with MainViewController equating to MainActivity.
