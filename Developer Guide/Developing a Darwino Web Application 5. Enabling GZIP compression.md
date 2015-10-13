# Enabling GZIP compression
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

