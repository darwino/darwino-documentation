# Darwino Application filter
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

