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

As you can see, this is standard J2EE security. It is often enough to do the job, but in some cases it is not sufficient. The web containers are implementing only a small set of authentication methods, and you cannot mix and match them. For example, you cannot combine form-based authentication with 


Security can be done at the web application server level, for example with J2EE’s Container security or WebSphere’s Administration Console. Alternatively, it can be handled via Darwino’s authentication filter.

Darwino’s authentication filter provides basic authentication and form-based authentication, and it can work with a directory implemented as a Darwino database.
