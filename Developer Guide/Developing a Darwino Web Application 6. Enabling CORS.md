# Enabling CORS
To have Darwino implement the CORS (Cross-Origin Resource Sharing) standard, enable this filter. This provides support for cross-site access controls.

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

This CORS filter is based on eBay's implementation documented [here](https://github.com/eBay/cors-filter).