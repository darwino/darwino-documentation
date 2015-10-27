## Mobile Manifest
Contains the information consumed specifically by the mobile applications.

In the manifest you can define connections to different servers. By default, your application will be connected to one server at a time, but an application might allow the user to choose servers from a list. The manifest is where this list of servers can be defined. To this end, the DarwinoMobileManifest object includes a method called getPredefinedConnections() which, by default, uses a JSON file as its connection source list.

Some of the other mobile-specific options defined here include:

- isLocalDatabase() - whether the application is using a local database
- isWebMode() - does it have web mode enabled for hybrid apps
- isDataSynchronization() - does it synchronize with the server
- isEncryptedByDefault() - should the data be encrypted