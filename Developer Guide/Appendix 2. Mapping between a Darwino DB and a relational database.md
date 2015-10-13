# Mapping between a Darwino DB and a relational database

A Darwino database is mapped to a set of relational tables. These tables store all of the documents for all of the stores in all of the instances of the database.

To optimize the performance of the database, one would add indexes depending on the nature of the queries that are being performed. There is an art to this optimization; besinde application-specific factors, there may be considerations related to the underlying database engine… Postgres, DB2, and MySQL could have different optimizations.

There is one set of relational tables per Darwino database. If the names of the tables depend on the database name, the definition of the tables is static for a given version of Darwino. This allows the tables to be created once by a DBA, and then used as-is, even when the application evolves.

For performance reasons, indexes on columns can be added. As the platform doesn't know most of the queries executed by the application, it predefines a minimal set of indexes to speed up the generic access to the database (get a document by ID, index by key, synchronization...). But it is up to the application developer to track the requests being emitted by the database and then add additional indexes as required.

On the database systems that support native JSON access, JSON access indexes can also be added. Please refer to your database system documentation for best practices.
