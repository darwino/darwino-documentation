# Enabling CORS
To have Darwino implement the CORS (Cross-Origin Resource Sharing) standard, enable this filter. This provides support for cross-site access controls.

```
<filter>
    <filter-name>Cors</filter-name>
    <filter-class>com.darwino.j2ee.servlet.cors.CORSFilter</filter-class>
    <init-param>
  	<param-name>cors.allowed.methods</param-name>
  	<param-value>GET,POST,PUT,DELETE,HEAD,OPTIONS</param-value>
    </init-param>    
    </filter>
    <filter-mapping>
	<filter-name>Cors</filter-name>
	<url-pattern>/*</url-pattern>
	<dispatcher>REQUEST</dispatcher>
	<dispatcher>FORWARD</dispatcher>
    </filter-mapping>
```

This CORS filter is based on eBay's implementation documented [here](https://github.com/eBay/cors-filter).