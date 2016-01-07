#User Authentication

To use the UserService for user authentication, simply get the UserAuthenticator object using getAuthenticator() with the provider name as the parameter. The UserAuthenticator that it returns provides the authenticate() method for performing the authentication with the username and password. Authenticate() will return the user’s DN if authentication was successful.

When the web application authentication mechanism is being used, the J2EE server returns the user’s principal (a DN). This principal is used by the UserService to create the User object. This User object represents the user throughout Darwino; there is no other user identity.

On the mobile device, the implementation is different due to the lack of the LDAP API. Instead, authentication is provided by connecting to the server and storing the current user DN. This is what is done in the "Settings" page.

Darwino has the ability to cache the user information on the mobile device. Furthermore, using custom code a developer can provide a list of users whose information should be prepopulated in the cache via a background operation.
