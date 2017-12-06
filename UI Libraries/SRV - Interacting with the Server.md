# Interacting with the server
The Darwino UI library provides a few built-in capabilities to interact with the server side.

## Darwino service URLs
Darwino has a very convenient capability that make the use of service URLs a lot easier.
By default, when a service path starts with `.darwino-`, it can also be called using `$darwino-`. In the later case, the URL is transformed back to `/.darwino-`, regardless of the relative position of `$darwino-`. This means that `$darwino-` can be used without worrying about the current browser URL.
On other words, `/$darwino-`, `/x/y/$darwino-`, `/a/b/c/$darwino-` are all equivalent when calling the server, and reach the exact same service.

## Resources and data servers
In a normal production mode, the UI elements (JS files...) and the data will be served by the same JEE application. But, during development, while the data will still be served by the J2EE server, we'd like to use a Webpack development server. Even if the 2 servers run on the same machine, they will be using a different port and thus will not share the authentication, while AJAXA calls will faces cross domain issues.

When developing using TOMCAT, the JEE serverport is 8080 while the Webpack server port is 8008 (defined in `webpack.config.js`)

### Authentication
The Webpack dev server does not authenticate users, while the real JEE server might. To make it work, the user will have to manually authenticate in another browser tab. Under the hood, the Darwino library ensures that his authentication is used by setting the request credentials to "include" (see dev.js) when it detects the development server.

### Cross domain requests
To support this mode, the CORS filter have to be enabled in the JEE `web.xml` (this is the default when using the Darwino Studio wizard):

    <!-- CORS should be applied before the authentication else the OPTIONS preflight can be blocked -->
    <filter>
    	<filter-name>Cors</filter-name>
    	<filter-class>com.darwino.j2ee.servlet.cors.CORSFilter</filter-class>
        <init-param>
      		<param-name>cors.allowed.methods</param-name>
      		<param-value>GET,POST,PUT,DELETE,HEAD,OPTIONS</param-value>
    	</init-param>    
	</filter>
	<filter-mapping>
    	<filter-name>Cors</filter-name>
    	<url-pattern>/*</url-pattern>
    	<dispatcher>REQUEST</dispatcher>
    	<dispatcher>FORWARD</dispatcher>
    </filter-mapping>

Moreover, as the JEE path is different, that path must be initialized using the DEV_OPTIONS class. The Darwino runtime will then query that class for the URL. The initialization happens very early, in `client.jsx`:

    // App rendering
    import {DEV_OPTIONS,initDevOptions} from '@darwino/darwino';

    // Redux dev tools
    if(process.env.NODE_ENV!="production") {
        initDevOptions("http://localhost:8080/contacts-react/")
        ...
    }
