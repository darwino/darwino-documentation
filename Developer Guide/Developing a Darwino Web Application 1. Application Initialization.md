# 1	Application initialization

The Darwino application object should be initialized before anything else. In order to create the application, the context listener must be included in the web.xml:
```
<!—Application initialization -->
<listener>
	<listener-class>
		demoApp.app.AppContextListener
	</listener-class>
</listener>
```

The listener is called when the application is started, and again when it is stopped. The listener will create the application object, and destroy it when it is no longer needed. It can also be used to initialize the relational database by creating the tables, assuming that the RDBMS user has the rights to modify the database schema.
```
<context-param>
     <param-name>dwo-auto-deploy-jsonstore</param-name>
     <param-value>true</param-value>
</context-param>
```
The possible values for dwo-auto-deploy-jsonstore are:
- true: If the database is not yet deployed, deploy it. If it is already deployed and not at the latest version, upgrade it.
- false: Do nothing.
- force: Erase what exists and redeploy from scratch. This is mostly for developers who are still making a lot of changes and want to start fresh every time.

If you don't have the authority to update the database schema, there is an option you can set in the JDBC definition level (in the JDBC connector, darwino/jdbc, which defines your connection to the database) to have Darwino not try to create the tables, but rather assume that they exist and just save the JSON definition of the database - the characteristics that are not related to the DDL itself, such as the extracted fields.

In practice, the developer will create their own class, extending AbstractDarwinoContextListener, where they will handle their application’s initialization needs, and refer to that class in the web.xml so that it is called.

####The DarwinoServiceDispatcher
By default, a set of services is created by Darwino, such as the service to access the JSON store. This is done by the service dispatcher.  It is possible to override the dispatcher. It contains a set of methods that can be disabled or added to.
```
Protected void initServicesFactories(HTTPServiceFactories factories) {
  addResourceServiceFactories(factories);
  addLibsServiceFactories(factories);
  addJsonStoreServiceFactories(factories);
  addSocialServiceFactories(factories);
  addApplicationServiceFactories(factories);
}
```
It is also possible to register custom services by using an extension point.
