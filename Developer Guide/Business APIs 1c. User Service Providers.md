#User Service Providers
Darwino has a user directory which functions as the main user directory (Darwino includes a set of main directory implementations via LDAP: IBM Domino, Tivoli Directory Manager, Oracle Directory, and Microsoft Active Directory, plus a native IBM Domino directory implementation). Every user in this directory is identified by a DN. This DN is fixed; it should not change. From this directory you can extract user data, for example the common name of the organization. On top of this directory, you can have user data providers. They provide data on existing users but from a different data source, such as an external LDAP directory, IBM Connections, or Facebook.

Suppose that your main directory is an LDAP directory, but you want to have the users' pictures coming from IBM Connections. You can write a user data provider that will use information you have in your main directory to locate the user in the IBM Connections directory to extract the data you need.

You can have as many user data providers as you want. A Gravatar implementation that can be used as a secondary directory is included with both Darwino Editions. The Darwino Enterprise Edition also includes a provider for IBM Connections.

Directories are made available to the UserService by registering them by name as providers. Registered providers are included when searching via the query() and typeAhead() functions.

It is also possible to get user information from a specific provider. The User object’s getUserData method, given the name of a registered provider, will return a UserData object. The UserData object has a getAttribute() method for retrieving value of the specified attribute, and a getAttributes() method to return all of the User’s attributes.

For retrieving binary user data, there is the getContent() method which returns the binary data of the specified type, such as “photo” or “payload”. Whereas attributes are cached, content data is not; it is always retrieved from the provider when it is requested.

Because it is possible to have multiple providers registered, user data may be spread across multiple directories, and there could even be duplications. For example, the user’s photo could exist in multiple providers’ sources. To accommodate this, the findAttribute() and findContent() methods will search exhaustively across all registered providers, starting with the current user object and stopping when they find the specified data. The developer doesn’t have to be concerned about where the data is actually stored.

Another issue that can result from having multiple registered providers is the need to map a user’s identity across providers. The UserProvider object includes the UserIdentityMapper() method, with converts between a user’s DN and the provider’s username format.
