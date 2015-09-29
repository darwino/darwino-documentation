# Mapping between a Darwino DB and a relational database

A Darwino database is mapped to a set of relational tables. These tables store all of the documents for all of the stores in all of the instances of the database.

To optimize the performance of the database, one would add indexes depending on the nature of the queries that are being performed. There is an art to this optimization; besinde application-specific factors, there may be considerations related to the underlying database engine… Postgres, DB2, and MySQL could have different optimizations.

(Philippe intends to provide more info on this topic later.)
