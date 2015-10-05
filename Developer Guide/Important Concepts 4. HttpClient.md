# HttpClient
Because a lot of activities in a Darwino are based on services, we need a means to connect to those services. We need to connect to them from Java, and not only from the browser. The Darwino HttpClient is very easy to use. It has classes for authentication and for handling JSON. For example, to call a REST service, pass some parameters, and get a result back, all that is required is to call getAsJSON. It will handle the job of composing the proper URL and processing the result.
 
The Darwino HttpClient is built on top of the platform's own client; the developer doesn’t have to worry about the underlying HTTP client (for Apache, regular JVM client, OkHttp).
 
The HttpClient also handles GZIP, ChunkedPost, and multi-part MIME.
