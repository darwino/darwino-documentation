Darwino DB API
=======================

# REST API
All of the features of the JSON store are exposed through REST services, and the REST services are wrapped in the various language binders. The REST services are optimized for performance and designed to be easy to use. They execute in different environments, server-side and in the mobile device, and can be coded once and run in multiple platforms.

In the Darwino Playground is an API Explorer; this can be used to experiment with the REST Services’ capabilities. All of the services are covered there, with documentation for all of their parameters.

(I think some code samples would help here.)

(SCREEN SHOT)

The Darwino framework allows the REST services, available by default, to be disabled at the database level, or to be overridden and enhanced. By overriding the service factory, is it possible to permit access dynamically based on the database, store, and current user. There is also a DocumentContentFilter interface for the REST services that allows dynamic filtering of the document data that is being produced, typically for security purposes. Along with that is a feature of the API that allows reconciliation of filtered documents upon save, so that if a section was filtered for presentation, that filtered data is not lost from the document when saving.

The REST services can be extended to accommodate JavaScript components, jqGrid for example, that expect the JSON they’re consuming to be in a particular format. One way to satisfy such a component would be to transform the JSON client-side, but that is not particularly convenient or efficient. Using Darwino’s JsonStoreServiceExtension, custom REST services can be defined and the existing REST services’ output can be modified. Being server-side, the Java API can most efficiently render the JSON as needed before it is emitted.
