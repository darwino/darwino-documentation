# Indexes

When thinking about indexes in Darwino, there are two different but related concepts to bear in mind.

- The first of these is an index on a table in the relational database.

 When you're doing SQL queries, or when Darwino is generating and executing SQL queries for you, those queries could benefit from having indexes defined for the keys that they're using. These indexes would help in retrieving data quickly and in sorting the data.

 Databases that natively support JSON can create indexes based on JSON values in the tables, allowing your application to query those JSON values faster.

 Darwino doesn't index all fields by default, because indexes have a cost. Maintaining unnecessary indexes can be a substantial burden. It is left to the developer to create indexes beyond those created by Darwino by default.

 Creating and modifying these indexes requires operating with the authority to modify the database DDL. With that authority, the developer can either use their preferred database utilities manually or utilize Darwino's database customizer, described in [Optimizing the database](Optimizing the database.md). 

- The other concept is that of a Darwino index.
 
 Think of this as the Map component of the MapReduce model in NoSQL. It involves data replication; there is a separate , indexed table in which you can create entries for every document in your application table. You can provide your own key for the index, and you can choose what to use for the values. The key can be different from the key for your source table, and it need not be unique. Both the keys and the values do not even need to be copied verbatim from the source table; they can be calculated from the source data, based on your application's needs.
 
 These indexes have two purposes:
 -- To define keys for documents that are beyond their primary key.
 -- To calculate values from the documents and make those calculated results easily available.
 
 Every time you save a document, the keys and values in these indexes are recalculated. This capability has many uses. For example: if you want to display documents sorted by their sizes. This value is not stored as part of the documents, but you could create an index in which the key is calculated based on the sum of all of the fields and attachments in the document.