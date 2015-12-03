#Introduction#


Welcome Darwino! Darwino is an open platform for rapidly developing social business applications targeting primarily Mobile and the Cloud. Importantly, Darwino allows developers to focus on the application, and not on platform specific issues or wiring to other platforms/data sources. Because of this, Darwino greatly reduces the time needed for you to deliver value to your customers. Darwino's full stack of ready-to-run components and connectors allow you to design applications that run locally on any mobile device, connected to any back end data source, with next to no platform-specific coding. Finally, Darwino leverages the skills of any Java developer - allowing Java developers to move to build enterprise mobile applications with no additional training.

This developer's guide will help you to learn the specifics of Darwino, and to build applications in no time. The following is a high-level overview of Darwino that will get you kick-started.

##Darwino Architecture##

Darwino is specifically designed and oriented around the use of Java, on both the server side and the mobile side. As such, your applications are built in pure Java, using POJOs, while deploying the application to multiple platforms is handled by the Darwino platform. Because of this, many of the components that Java developers are used to remain consistent when developing for Darwino, including:

* The use of a standard RDBMS for data storage (although the Darwino DB JSON Store is much more than a typical RDBMS)
* LDAP directory authentication and authorization
* Extendable platform components using standard Java techniques
* Runs on top of standard JVM Servlet Container
* Add-in for Eclipse developer studio
* Maven compatible/compliant, so you can use whatever development tool you choose
* Write once, run on Web/Mobile/ Hybrid, with automatic porting of application to Android and iOS

##Darwino Library Hierarchy##

Darwino is at its core, a set of reusable and extendable components. Because they are both hierarchical and encapsulated you, as the developer, can choose to use whatever components you wish, and can choose not to implement other ones - of course, there's a few that are required, such as:

**ROOT**

The root library includes all of the libraries required to build a basic Darwino application, and includes all of the libraries that are common and not platform-specific.

**Platform-Specific Libraries**	

Underneath the root, you'll find platform specific libraries for the various platforms that Darwino supports:

* J2EE (Java Web Application Server)
* iOS
* Android	

You simply include the libraries that you need for the platforms you intend to support.

>If you use the Eclipse Darwino Application Wizard, all of the appropriate libraries are imported into your Application Project.

##Required Infrastructure##

**Supported Database Platforms**

* Postgres DB 9.4+
* IBM DB2 10.5 +
* (Mobile) SQLite

**Webserver**

* Any servlet container that is Servlet 3.0+ compliant (e.g., TOMCAT, IBM Websphere, IBM Websphere Liberty)

**Supported Cloud Deployment Platforms**

* IBM BlueMix

**Supported Mobile OS**

* Android 4.4+
* iOS 8.1+










 