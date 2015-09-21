Developing a Darwino J2EE Web Application
=======================
To support a Darwino J2EE application, you need to have an application server such a Tomcat or WebSphere that supports the Servlet API. 

# 1	Application initialization
Darwino provides a broad set of features and support services to a web application. It is possible to create an application that makes use of Darwino DB without using the other services that Darwino provides, such as creating connections and handling replication, but by using the pre-built services in the full Darwino package, the programmer will avoid a lot of unnecessary work.

The Darwino application object should be initialized before anything else. In order to create the application, the context listener must be included in the web.xml:
```
<!—Application initialization -->
<listener>
	<listener-class>
		DWOTPL_PACKAGENAME.app.AppContextListener
	</listener-class>
</listener>
```

The listener is called when the application is started, and again when it is stopped. The listener will create the application object, and destroy it when it is no longer needed.

In practice, the developer will create their own class, extending AbstractDarwinoContextListener, where they will handle their application’s initialization needs, and refer to that class in the web.xml so that it is called.

The DarwinoServiceDispatcher
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


# 2	Darwino Application filter
 The Darwino context should be created when a request comes in, and it should be deleted when the request is satisfied. In order to provide the context for an application request, a J2EE filter specified in the web.xml is called. This filter must be specified immediately after authentication (if Darwino is handling authentication) in the web.xml file so that it is executed before any others. Only in this way can subsequent filters have access to the context created here.

```
<!-- Filter for creating the Darwino Application, Context and DB session -->
<!—The filter must be first so all the other filters can access the app -->
<filter>
	<filter-name>DarwinoApplication</filter-name>
	<filter-class>com.darwino.j2ee.application.DarwinoJ2EEFilter</filter-class>
	<--
	<init-param>
		<param-name>varName</param-name>
		<param-value>__newsdatabase_session</param-value>
	</init-param>
	-->
</filter>
<filter-mapping>
	<filter-name>DarwinoApplication</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>
```

The filter is executed before all else when a request comes, and last when a request is handled. Because this is first in line, it can do processing before the request is even seen by the servlet, and then again after the request is processed.

The filter mapping exists to allow the filter to be executed conditionally.

# 3	Darwino libs and URL rewriting
The DarwinoRewriting filter transforms some urls such as “$darwino-libs” into an actual path. This remapping enables platform independence without requiring code changes to accommodate different platform configurations.

This filter can also perform HTML rewriting. When the HTML is served to the client, it can be transformed. This is useful in CDN scenarios, where the urls being sent to the client may need to be modified to point to alternate locations.


```
<filter-name>DarwinoRewriting</filter-name>
<filter-class>com.darwino.j2ee.servlet.resources.DarwinoGlobalRewriterFilter</filter-class>
</filter>
<filter-mapping>
	<filter-name>DarwinoRewriting</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>
```



# 4	Serving application resources
In order to determine which requests should be handled by Darwino services, this filter analyzes the incoming requests, looking to see if there are internal services to handle them, and delegates them to Darwino if appropriate and passes them along if they are not.

```
<filter-name>DarwinoServices</filter-name>
<filter-class>DWOTPL_PAGEAGENAME.app.DarwinoServiceDispatcher</filter-class>
</filter>
<filter-mapping>
	<filter-name>DarwinoServices</filter-name>	
	<url-pattern>/*</url-pattern>
	<!—DarwinoRewriting can trigger a forward -->
	<dispatcher>REQUEST</dispatcher>
	<dispatcher>FORWARD</dispatcher>
</filter-mapping>
```


# 5	Enabling GZIP compression
Not all web servers implement GZIP. You can use this filter to have Darwino process GZIP requests and return GZIP content.

```
<filter-name>GZipFilter</filter-name>
<filter-class>com.darwino.j2ee.servlet.gzip.GZipServletFilter</filter-class>
</filter>
<filter-mapping>
	<filter-name>GZipFilter </filter-name>	
	<url-pattern>/*</url-pattern>
	<dispatcher>REQUEST</dispatcher>
	<dispatcher>FORWARD</dispatcher>
</filter-mapping>
```


# 6	Enabling CORS
To have Darwino implement the CORS (Cross-Origin Resource Sharing) standard for the JSON store, enable this filter. This provides support for cross-site access controls.

```
<filter-name>Cors</filter-name>
<filter-class>com.darwino.j2ee.servlet.cors.CORSFilter</filter-class>
</filter>
<filter-mapping>
	<filter-name>Cors</filter-name>	
	<url-pattern>jsonstore/*</url-pattern>
	<dispatcher>REQUEST</dispatcher>
	<dispatcher>FORWARD</dispatcher>
</filter-mapping>
```

# 7	Authentication and authorization
Security can be done at the web application level, for example with J2EE’s Container security or WebSphere’s Administration Console. Alternatively, it can be handled via Darwino’s authentication filter.

Darwino’s authentication filter provides basic authentication and form-based authentication, and it can work with a directory implemented as a Darwino database.

(Provide examples)


# 8	New Domino stuff will go here

