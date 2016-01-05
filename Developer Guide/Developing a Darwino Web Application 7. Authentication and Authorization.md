# Authentication and Authorization
A Darwino web app is not just serving up the UI; it is also serving data through the REST services, so you want to be sure to properly secure the application.

There are several ways to secure the application. One way is to utilize the web application server's security mechanisms. The web.xml file contains the J2EE specification, and defines how security is handled by the J2EE container.

```
<!-- Enable this to use the J2EE CONTAINER security -->
	<security-role>
		<role-name>user</role-name>
	</security-role>
	<security-constraint>
		<web-resource-collection>
			<web-resource-name>ApplicationRoot</web-resource-name>
			<url-pattern>/*</url-pattern>
		</web-resource-collection>
		<auth-constraint>
			<role-name>user</role-name>
		</auth-constraint>
	</security-constraint>

	<login-config>
		<auth-method>BASIC</auth-method>
		<realm-name>demoapp</realm-name>
	</login-config>
  	  	
```
Above, we see a role named "user" (at the web application server, we would map that name to "users"). That role is then granted access to resources via the url-pattern (in this case, all resources). BASIC authentication is then specified, which will prompt for a username and password. 

As you can see, this is standard J2EE security. It is often enough to do the job, but in some cases it is not sufficient. The web containers are implementing only a small set of authentication methods, and you cannot mix and match them. For example, you cannot combine the very secure form-based authentication for your web application with basic authentication for your REST services, unless you're willing to rely on extensions for your web container.

When you want to use the J2EE container's built-in capability, the J2EE container must be able to directly access the directory in order to authenticate the user. Thus, the directory that you're using must be available to the web application server. Some web application servers have proprietary APIs that give them access to non-standard directories, but this is not a standard – it depends on the particular application server. This is a problem if you want an application that is portable.

Darwino provides a set of J2EE filters to support form-based authentication and basic authentication. Using these filters, you can protect different parts of your application using different mechanisms. These filters are a subset of the features implemented by [Apache Shiro](http://shiro.apache.org/). These filters are fully portable across web application servers.

A side effect of this filter authentication is that the users are unknown to the web application server; to the server they are always anonymous. Darwino has the responsibility for authentication and access control.

Security can be done at the web application server level, for example with J2EE’s Container security or WebSphere’s Administration Console. Alternatively, it can be handled via Darwino’s authentication filter.

Darwino’s authentication filter provides basic authentication and form-based authentication, and it can work with a directory implemented as a Darwino database.

Accessing the current user in Darwino code is done through the DarwinoContext.
