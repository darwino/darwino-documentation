# Darwino libs and URL rewriting
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

