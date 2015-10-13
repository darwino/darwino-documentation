#Authentication

To use the UserService for user authentication, simply get the UserAuthenticator object using getAuthenticator() with the provider name as the parameter. The UserAuthenticator that is returns provides the authenticate() method for performing the authentication with the username and password. Authenticate() will return the user’s DN if authentication was successful.

When the web application authentication mechanism is being used, the J2EE server returns the user’s principal (a DN). This principal is used by the UserService to create the User object. This User object represents the user throughout Darwino; there is no other user identity.

On the mobile device, the implementation is different due to the lack of the LDAP API. The UserService goes through HTTP on mobile. To support this, the server-side API is available through REST services to CORS. All of the features of the UserService are available through REST. There is a shell Java implementation that encapsulates the REST services to provide easy access to the full array of UserService functions.

Darwino has the ability to cache, on the mobile device, the user information. Furthermore, a developer can provide a list of users whose information should be prepopulated in the cache via a background operation.
