#User Information
User information is generally not managed by Darwino; it is stored somewhere else, such as in an LDAP directory or, in the case of Domino, in the NAB. The User service provides access to the external, central directory. There may also be peripheral information about users. For example, the primary user directory may be in Domino, while other information, such as the user’s photograph, is stored in IBM Connections or Facebook. The User Service is architected to simplify working with such distributed user information. One directory is considered the main directory, and additional data can come from zero or more secondary directories.
	

The directories that work with the User Service are implemented as managed beans, and they are full extensible. Darwino provides beans for several LDAP directories and for the IBM Domino directory.

User information takes two forms: information about a user, and a query.

To work with the User Service:

```
Public UserService.getUserService() {
	return Platform.getService(UserService.class);
}
```

The User Service provides a set of function for finding users and retrieving details about users. Because multiple directories may be referenced, there could be multiple IDs for a single user. Nonetheless, there must be only one canonical distinguished name (DN). With this in mind, there are two operations available to find a particular user:

```
public User findUser(String dn) throws UserException;
```
This method returns the User object corresponding to the provided DN.

```
public User findUserByLoginID(String id) throws UserException;
```
These methods find the user that best matches the provided ID. The ID can be a DN, an email address, a short name, a common name, etc…

findUserByLoginID() does not identify a user with certainty; only findUser() can do that.

There are also several functions for returning lists of users:
- findUsers() returns a list of users based on the provided String array of DNs.
- query() takes an LDAP query (allowing ANDs and ORs) and returns a List of all matches across all directories.
- typeAhead() performs a simple “starts with” query and returns a List of all matches across all directories.

Once you have the User object, you can use its methods to query a provider for user details stored there. For example:
-	getDN()
-	getCN()
-	getGroupCount()
-	getGroups()
-	getRoleCount()
-	getRoles()
-	getAttribute()