# Enabling CORS
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
