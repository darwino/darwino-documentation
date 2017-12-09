# REST Services
Of course, standard, straight JavaScript capabilities can be used to call REST services (`XMLHttRequest`, `Fetch`, ...). But Darwino makes it both easy to create a service on the server side, and to call it from the client.

## Calling a REST service
Darwino provides some utility in `Fetch.js`. This utility is a thin wrapper on top of the browser `Fetch` function that:

- Seamlessly handles the development server URL and credentials differences
- Treat request with status code not in 200-299 as error and throw an exception
- Have some simple utility functions, `fetchJson` & `fetchText`, that parse the response and return the expected data type in a promise

See: `extras/ServicesRest.jsx`

## Developing a REST service
Such a REST service has to be done in Java on the server side. There are a few classes to define:

### The REST service class
This is the class that implements the actual service. The service() function gets a context and should return a value, or an error, on execution.
One instance of this class will be created per service execution. The instance will be discarded after the service is executed.

see: `contacts-react-shared/../AppInformationRest.java`

### The REST service factory class
This class creates the REST service instance for a particular request, by matching the URL parts. A typical factory will match multiple services with multiple URLs.
A factory has to be registered at the application level, see: `AppBasePlugin.java`

see: `contacts-react-shared/../AppRestServiceFactory.java`
