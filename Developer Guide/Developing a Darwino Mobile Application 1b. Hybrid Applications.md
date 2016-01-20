##Hybrid applications
The idea is to have true portability regardless of the device where the application is being executed, and to work seamlessly offline and online. As thsi diagram depcts, the architecture of Darwino on the mobile device and on the application server is identical. They are both running an HTTP server; on the server, it is WebSphere or Tomcat or the equivalent, while on the mobile device it is a lightweight server based on NanoHTTPD. Both sides can run the business logic, written in Java and relying on Darwino's runtime libraries.

The hybrid app running on the mobile device instantiates a web component, but the web component is talking to the local HTTP server. The means that the application is truly portable between the web compoent in the hybrid app and the web browser. There are no differences. This allows Darwino to have a single app run in different platforms without any changes.

Note that the database access is abstracted by the runtime libraries. If you're offline, it wil access the local database on the mobile device, and if you're online it will access the remote database located on the application server. This is completely transparent for your application.

This is better than a straight Apache Cordova application, although this framework can be used if desired.


![](<hybrid-architecture.png>)