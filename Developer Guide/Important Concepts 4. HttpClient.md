# HttpClient
Because a lot of activities in a Darwino are based on services, we need a means to connect to those services. We need to connect to them from Java, and not only from the browser. The Darwino HttpClient is very easy to use. It has classes for authentication and for handling JSON. For example, to call a REST service, pass some parameters, and get a result back, all that is required is to call getAsJson(). It will handle the job of composing the proper URL and processing the result.

```
String url = "http://localhost/playground.nsf/playground/$darwino-jstore";
HttpClient c = ((HttpClientService)Platform.getService(HttpClientService.class))
  					.createHttpClient(url);

// Call the information service and interpret the result as JSON
//    <base-url>
Object r = c.getAsJson(StringArray.EMPTY_ARRAY);                      
_formatText("JSON Store information: {0}",r);

// Call the user service and interpret the result as JSON
//    <base-url>/user
Object r2 = c.getAsJson(new String[]{"user"});                      
_formatText("JSON Store User: {0}",r2);

```
The Darwino HttpClient is built on top of the platform's own client; the developer doesn’t have to worry about the underlying HTTP client (for Apache, regular JVM client, OkHttp).
 
The HttpClient also handles GZIP, ChunkedPost, and multi-part MIME.
