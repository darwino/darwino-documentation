Darwino DB API
=======================

# Security
(Question: security coverage is strewn about. Maybe this shouldn't be a seperate chapter here, or this chapter should be a grand overview? It at least needs to be structured better.)

## Database security
Darwino implements multi-level security. You can assign security to the Server object; you can control who can and cannot access the server. At the database level, you can assign an ACL. In the ACL, you can define who can access the database, manage the database, read documents, create documents, delete documents, and edit documents. At the Document level, you can maintain a list of users who can read or read/write the document. 
 
 The same principles apply to both readers/writers, and excluded-readers/excluded-writers. 
 
 1- Entries 
Each entry can be: 
- a person, and then the entry is of the form p:id 
- a group, and then the entry is of the form g:id 
- a role, and then the entry is of the form r:id 
- everybody, *

 An entry can be readonly (reader field) or read/write (writer field). A writer entry is automatically a reader as well. 
If an entry appears in both the readers and writers, then it is a writer.  
 
 2- Security behavior 
If there are no entries attached to a document, then there is no document security 
If there is at least one entry (reader or writer, or both), then there is document security. 
If everybody should be a reader and while writers should be limited, the solution is the following: 
writers entries should contain the limited list 
reader should contain one entry everybody * 

 3- Storing readers/writers 
Readers are stored in the _readers field, while writers are in _writers. 
These fields can directly contain an array of entries (see 1-) or an object containing several arrays, one per property 
ex: 
{ 
_readers: ['p:phil', 'p:dave'], 
_writers: ['p:jesse'] 
} 
or 
{ 
_readers: { 
        field1: ['p:phil', 'p:dave'], 
        field2: ['p:bob'] 
}, 
_writers: ['p:jesse'] 
} 

 4- Helpers 
There is a Java class, SecurityHelper, that can be used to manipulate the these fields. They are used, for example, in the CRUDSEC.java unit test. 
 
 Regarding ereaders and ewriters: Darwino, when composing a SQL query, adds a subquery to exclude what is not allowed to be seen. This incurs a cost. To avoid this when possible, there is a database property indicating whether document security should be enabled. When it is not enabled, generated queries can avoid the step of running the subquery. A result of this is that if the flag is not set, readers and writers on documents will be ignored in all of the database’s stores. Options for this property are: no document security, reader/writer security only, ereader/ewriter security only, and all security features.
