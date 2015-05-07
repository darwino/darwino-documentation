The demo applications consist of two main groups of projects: the News demo and the Domino Discussion demo.

News
----

The news application contains five runnable applications: a J2EE/Tomcat application and native/hybrid pairs of applications for Android and iOS.

The J2EE application is named `dwo-demo-news-j2ee`. To run it using a configured Tomcat server inside Eclipse, right-click on the project and choose Run As &rarr; Run On Server:

![](runas-runonserver.png)

This will launch the app in Tomcat and open up the default (JSF-based) landing page for the application.




Customizing Connection Settings
-------------------------------

It connects to PostgreSQL for its data storage. By default, it uses the port and credentials specified on the [[Preparting the Development Environment]] page. If needed, this can be modified by changing either the `src/main/resources/darwino_default.properties` file in the `dwo-demo-commons` project or by creating a copy of this file named `src/main/resources/darwino.properties` in the project itself.