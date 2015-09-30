# HttpClient
Because a lot of things are based on services, we need to connect to those services. We need to connect to them from Java, and not only from the browser. The HttpClient here is very easy to use. It has easy-to-use classes for authentication and for handling JSON. For example, to call a REST service, pass some parameters, and get a result back, just call getAsJSON. It will handle the job of composing the proper URL and processing the result.
 
The developer doesn’t have to worry about the underlying HTTP client (for Apache, regular JVM client, OkHttp.)
 
The HttpClient also handles GZIP, ChunkedPost, and multi-part MIME.
