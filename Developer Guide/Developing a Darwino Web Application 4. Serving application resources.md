# Serving application resources
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

