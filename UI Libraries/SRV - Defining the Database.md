# Defining the Database

A Darwino JSON database can feature options to define the list of stores and their associated options (ex: extracted fields, full text search...)

If a database can be deployed without a definition for these options, it is advised to use one. When the database definition evolves, the Darwino runtime will automatically 'restructure' the database based on the new definition. It also ensures that the deployed database matches the version expected by the application. If it does not, then it can either upgrade the deployed version or generate an error.

The database definition file is also deployed on te mobile devices.

See: `AppDatabaseDef`
