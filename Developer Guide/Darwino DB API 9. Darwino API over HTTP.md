Darwino DB API
=======================

# Darwino API over HTTP
The entire JSON store API is exposed through REST services. Everything is supported except transactions, due to the stateless nature of HTTP. There are wrappers for Java and JavaScript, with more to follow.

You start with the session, and from the session you get access to all of the functions, and it’s either going locally or it’s going remotely through REST services. If you are going to be generating a lot of database accesses, you should do that on the server through custom REST services in order to minimize the number of remote calls. It is good to avoid using the Darwino API to perform a lot of remote database transactions. As a general rule, put the business logic server-side and call it through REST services.
