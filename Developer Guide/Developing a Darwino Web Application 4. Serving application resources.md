# Serving application resources
In order to determine which requests should be handled by Darwino services, this filter analyzes the incoming requests, looking to see if there are internal services to handle them, and delegates them to Darwino if appropriate and passes them along if they are not.

This service is for serving 'static' resources, like HTML, CSS, JavaScript, etc... It ensures that it works on all the platforms, including J2EE and mobile. It serves the resources located in the platform specific directories (ex: web app for J2EE, assets for Android...) but also the ones packaged in the jar files under /DARWINO-INF/resources. (META-INF/resources cannot be used on Android).

Also, depending on the execution mode (development vs. production, as defined at the Platform object level), it can choose to load the minified version of the files or the full commented one. The minified versions have a ".min" inserted to their path, like myfile.min.js or mytheme.min.css.

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

