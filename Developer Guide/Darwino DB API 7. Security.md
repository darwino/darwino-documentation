Darwino DB API - Security
=======================

# Database security
Darwino implements multi-level security. We have the notion of the user, which is mapped by the User class. A user has a CN, a DN, a list of groups, and a list of roles. The directory is used to authenticate the user and to get the list of groups. Generally, the groups come from the directory, while the roles are very specific to the application. Mapping between roles and groups can be done within a particular application.

At the server level, you can control who can and cannot access the server, based on user ID, group, or role.

At the application level, you can add dynamic roles to each user. The JSON store will trust the roles that are defined for the user in the User object.

With the Darwino Enterprise Edition, we have the notion of instances. An instance can contribute roles and groups to a particular user. For example, in a multi-tenant application running in IBM Connections with Communities, one tenant will be one Community. You can define the readers of a tenant as being all of those who are members of a Community, and the editors could be all of those who are owners of the Community. This will change for each Community, because the Communities each have their own lists of members and owners. In other words, you have an extension point that allows the Instance Manager to augment a user with roles and groups–dynamically–for a particular instance. The UserContext in the JSON store is the User plus what has been contributed by the Instance Manager.

At the database level, you can assign an ACL. In the ACL, you can define who, among those who have been allowed server access, can access the database, manage the database, read documents, create documents, delete documents, edit documents, and update someone else's social data. When defining the database ACL, you are defining access rights to the database based on user IDs, groups, or roles.

# Document security
At the Document level, you can maintain a list of users who can read or read/write the document.  Document security is based on a simple set of rules involving fields specifying read-only and read/write access. Entries in these fields can be the names of users, roles, and groups.

There are four types of document security fields:
- reader
- writer
- excluded reader
- excluded writer

The same principles apply to both readers/writers and excluded-readers/excluded-writers. 
 
###Entries 
Each entry can be: 
- a person 
- a group 
- a role 
- everybody, * 

An entry can be read-only (reader field) or read/write (writer field). A writer entry is automatically a reader as well. 
If an entry appears in both the readers and writers, then it is a writer.  
 
###Security behavior 
If there are no entries attached to a document, then there is no document security. The user's access to the documents will be determined solely by the higher levels (database and server). 
If there is at least one entry (reader or writer, or both), then there is document security. 
If everybody should be a reader and while writers should be limited, the solution is the following: 
- writers entries should contain the limited list 
- reader should contain one entry: everybody * 

###Storing readers/writers 
Readers are stored in the _readers field, while writers are in _writers. 
These fields can directly contain an array of entries (see "1 - Entries") or an object containing several arrays, one per property. 

For example: 
```
{ 
_readers: ['carol', 'ted'], 
_writers: ['alice'] 
} 
or 
{ 
_readers: { 
        field1: ['carol', 'ted'], 
        field2: ['bob'] 
}, 
_writers: ['alice'] 
} 

```

Having sub-objects is the preferred method, as it allows a finer-grained management of the entries. For example, a workflow engine can add a field containing the participants for the current step, and this can be removed after the step is completed.

###Helpers 
There is a Java class, SecurityHelper, that can be used to manipulate these fields.
 
When document security is enabled, Darwino, when composing a SQL query, adds a subquery to exclude what is not allowed to be seen. This incurs a cost. To avoid this when possible, there is a database property indicating whether document security should be enabled. When it is not enabled, generated queries can avoid the step of running the subquery. A result of this is that if the flag is not set, readers and writers on documents will be ignored in all of the database’s stores. Options for this property are: no document security, reader/writer security only, ereader/ewriter security only, and all security features.

###REST Services Restriction
Even though a particular user has access to a database, you can restrict their access via REST services. For example, in your application you may want to expose a business API; you may want to manipulate and return objects. Physically, they are stored as documents. You might want to expose your objects through REST services, while preventing direct access via the default REST services serving documents; you only want people to go through your API. In this case, you could choose to prevent access to this particular database via REST services. 

###Dynamic Filtering
There is a DocumentContentFilter interface for the REST services that allows dynamic filtering of the document data that is being produced, typically for security purposes. Along with that is a feature of the API that allows reconciliation of filtered documents upon save, so that if a section was filtered for presentation, that filtered data is not lost from the document when saving.
