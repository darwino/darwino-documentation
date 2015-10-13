Darwino DB API
=======================

# Darwino API over HTTP
There’s not a lot to say here that we haven’t covered elsewhere. The entire JSON store API is exposed through REST services. Everything is supported except transactions because of the stateless nature of HTTP. There are wrappers for Java and JavaScript, with more to follow.

You start with the session, and from the session you get access to all of the functions, and it’s either going locally or it’s going remotely through REST services. If you are going to be generating a lot of database accesses, you should do that on the server and through REST services, being careful not to use the Darwino API to perform a lot of database transactions. As a general rule, put the business logic on the server side and call it through REST services.

(This needs to be spelled out better.)
