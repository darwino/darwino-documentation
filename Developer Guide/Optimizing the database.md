#Optimizing the database

It is not often necessary to consider the underlying RDBMS when creating an application in Darwino; the services and API abstract the details away so you can focus on the business logic. Still, when performance is a serious concern you may find a benefit in considering how Darwino works with the tables and their indexes.

When you execute a query in Darwino (see [Appendix 3. The Query Language](Appendix 3. The Query Language.md) for details on queries), the system will generate and execute a SQL statement to satisfy the request. Darwino will do what it can to make that SQL query as efficient as possible, utilizing the indexes that are available. However, if the necessary indexes aren't there, you inadvertently could force a table scan. With small or infrequently-accessed tables this may not be an issue, but there are many cases where it's going to have a noticable effect.

To be sure, your Darwino application will work without any indexes beyond those that Darwino provides by default. Additional indexes come into play only if needed to make the application faster once it is in production.

###  Default indexes
There is one set of relational tables per Darwino database. The names of the tables depend on the database name, and the definition of the tables is static for a given version of Darwino. This allows the tables to be created once by a DBA, and then used as-is even as the application evolves. See [Appendix 2. Mapping between a Darwino DB and a relational database](Appendix 2. Mapping between a Darwino DB and a relational database.md) for details on the Darwino application tables.

Darwino defines a minimal set of RDBMS indexes to assist generic access to the database (get a document by id, get document by UNID, index by key, synchronization...). Because there is a cost involved in creating indexes, Darwino makes no presumptions about your applications needs beyond these basic functions. It is up to the application developer to track the requests being emitted to the database and then add additional indexes if necessary.

> Note: On the database systems that support native JSON access, JSON access indexes can be added. Refer to your RDBMS documentation for details and best practices.

###The database customizer
You can use your RDBMS's admistration tools to create indexes while you're developing and debugging your application. A problem arises once you have deployed the application: you, as developer, may no longer be in control of the database, and you cannot be sure that your indexes will remain.

Fortunately, Darwino has the ability to generate the indexes for you. The JdbcDatabaseCustomizer has a method, getAlterStatements(), that is called when you deploy the database. It takes a set of SQL statements and executes them for you. Those statements can create indexes, stored procedures, triggers... anything that is understandable by your database. Because the database schema is being modified, it is necessary that you have authority to perform database DDL write operations. 

> Note: While you can code logic in Darwino to act when a document is deleted individually, there is no event raised in Darwino when a document is being deleted as part of a group operation. If your needs demand it, you could code a database trigger to do processing when database records are deleted. For example, a trigger could act on a delete, copying the record to a separate table for archiving. Such a trigger could be defined in the database customizer.

The database customizer includes a version number. When the database is loaded by Darwino, that version number is compared against the last-used customization version. If the customizer returns a version that is higher, indicating that there are changes needing to be applied, then Darwino will call the customizer so it can do its job.