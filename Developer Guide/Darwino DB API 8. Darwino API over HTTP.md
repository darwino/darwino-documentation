Darwino DB API
=======================

# Darwino API over HTTP
The entire JSON store API is exposed through REST services. Everything is supported except transactions, due to the stateless nature of HTTP. There are wrappers for Java and JavaScript, with more to follow.

You start with the session, and from the session you get access to all of the functions, and it’s either going locally or it’s going remotely through REST services. If you are going to be generating a lot of database accesses, you should do that on the server through custom REST services in order to minimize the number of remote calls. It is good to avoid using the Darwino API to perform a lot of remote database transactions. As a general rule, put the business logic server-side and call it through REST services.

# REST API
All of the features of the JSON store are exposed through REST services, and the REST services are wrapped in the various language binders. The REST services are optimized for performance and designed to be easy to use. They execute in different environments, server-side and in mobile hybrid applications, and can be coded once and run in multiple platforms.

In the Darwino Playground is an [API Explorer](http://playground.darwino.com/playground.nsf/Explorer.xsp); this can be used to experiment with the REST Services’ capabilities. All of the services are covered there, with documentation for all of their parameters.

![](<api_explorer.png>)


The Darwino framework allows the REST services, available by default, to be disabled at the database level, or to be overridden and enhanced. By overriding the service factory, is it possible to permit access dynamically based on the database, store, and current user.

The REST services can be extended to accommodate JavaScript components, jqGrid for example, that expect the JSON they’re consuming to be in a particular format. One way to satisfy such a component would be to transform the JSON client-side, but that is not particularly convenient or efficient.

Using Darwino’s JsonStoreServiceExtension, custom REST services can be defined and the existing REST services’ output can be modified. Being server-side, the Java API can most efficiently render the JSON as needed before it is emitted.
