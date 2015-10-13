#User Service Providers
Directories are made available to the UserService by registering them by name as providers. Registered providers are included when searching via the query() and typeAhead() functions.

It is also possible to get user information from a specific provider. The User object’s getUserData method, given the name of a registered provider, will return a UserData object. The UserData object has a getAttribute() method for retrieving value of the specified attribute, and a getAttributes() method to return all of the User’s attributes.

For retrieving binary user data, there is the getContent() method which returns the binary data of the specified type, such as “photo” or “payload”. Whereas attributes are cached, content data is not; it is always retrieved from the provider when it is requested.

Because it is possible to have multiple providers registered, user data may be spread across multiple directories, and there could even be duplications. For example, the user’s photo could exist in multiple providers’ sources. To accommodate this, the findAttribute() and findContent() methods will search exhaustively across all registered providers, starting with the current user object and stopping when they find the specified data. The developer doesn’t have to be concerned about where the data is actually stored.

Another issue that can result from having multiple registered providers is the need to map a user’s identity across providers. The UserProvider object includes the UserIdentityMapper() method, with converts between a user’s DN and the provider’s username format.

