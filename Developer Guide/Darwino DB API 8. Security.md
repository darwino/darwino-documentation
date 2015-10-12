Darwino DB API - Security
=======================

# Database security
Darwino implements multi-level security. You can assign security to the Server object; you can control who can and cannot access the server. At the database level, you can assign an ACL. In the ACL, you can define who can access the database, manage the database, read documents, create documents, delete documents, and edit documents.

# Document security
At the Document level, you can maintain a list of users who can read or read/write the document.  Document security is based on a simple set of rules involving fields specifying read-only and read/write access. Entries in these fields can be the names of users, roles, and groups.

There are four types of document security fields:
- reader
- writer
- excluded reader
- excluded writer

The same principles apply to both readers/writers and excluded-readers/excluded-writers. 
 
1- Entries 
Each entry can be: 
- a person 
- a group 
- a role 
- everybody, * 

An entry can be read-only (reader field) or read/write (writer field). A writer entry is automatically a reader as well. 
If an entry appears in both the readers and writers, then it is a writer.  
 
2- Security behavior 
If there are no entries attached to a document, then there is no document security. The user's access to the documents will be determined solely by the higher levels (database and server). 
If there is at least one entry (reader or writer, or both), then there is document security. 
If everybody should be a reader and while writers should be limited, the solution is the following: 
- writers entries should contain the limited list 
- reader should contain one entry: everybody * 

3- Storing readers/writers 
Readers are stored in the _readers field, while writers are in _writers. 
These fields can directly contain an array of entries (see 1-) or an object containing several arrays, one per property. 

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
4- Helpers 
There is a Java class, SecurityHelper, that can be used to manipulate these fields. They are used, for example, in the CRUDSEC.java unit test. 
 
Regarding excluded-readers and excluded-writers: Darwino, when composing a SQL query, adds a subquery to exclude what is not allowed to be seen. This incurs a cost. To avoid this when possible, there is a database property indicating whether document security should be enabled. When it is not enabled, generated queries can avoid the step of running the subquery. A result of this is that if the flag is not set, readers and writers on documents will be ignored in all of the database’s stores. Options for this property are: no document security, reader/writer security only, ereader/ewriter security only, and all security features.
