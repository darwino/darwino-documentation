##Properties
It's good to be able to externalize some application properties. You can pass properties to an application and then access these properties through a service. The property service, getProperty() method, and putProperty() method are the means for working with these externally-defined properties.

The properties are defined in a manner similar to how [managed beans](Important Concepts 2c. Managed Beans.md) are defined, except instead of being in the darwino-beans.xml file, properties are in the darwino.properties file. The properties file can be at the same file system paths as the darwino-beans.xml, and can be available through a JNDI call.

####Property references in web.xml
It's possible to reference Darwino-related properties, defined within the web.xml file, via the Darwino Application Listener.

Here, we see a paramater value being extracted from a named property:

```
<context-param>
	<param-name>dwo-sync-trace</param-name>
    <param-value>${discdb.sync-trace=false}</param-value>
</context-param>
```
Darwino will check the platform for a property called "discdb.sync-trace" and return its value. If it is not found, it will use the default value, which is "false" in this example.

