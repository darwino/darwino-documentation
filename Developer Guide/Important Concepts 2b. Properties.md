##Properties
It's good to be able to externalize some application properties. You can pass properties to an application and then access these properties through a service. The property service, getProperty() metod, and putProperty() method are the means for workinbg with these externally-defined properties.

The properties are defined in a manner similar to how managed beans are defined, except instead of being in the darwino-beans.xml file, properties are in the darwino.properties file. The properties file can be at the same file system paths as the darwino-beans.xml, and can be available through a JNDI call.

####Property references in web.xml
In Darwino, it's possible to reference properties from within the web.xml file. Here, we see a paramater value being extracted from a named property:

```
<context-param>
	<param-name>dwo-sync-trace</param-name>
    <param-value>${discdb.sync-trace=false}</param-value>
</context-param>
```
Darwino will check the platform for a property called "discdb.sync-trace" and return its value. If it is not found, it will use the default value, which is "false" in this example.

####Property references in managed bean definitions
Similar to how the web.xml is handled, when the Darwino code that parses managed bean definitions encounters a property name, it looks first to see if it is defined in the file as a local property. If it is not defined locally, it will then look to the platform for a property of that name. Finding it, it will use its value in the managed bean definition.

This allows you to drive the creation of the managed beans from outside the application.

The goal with all of this is to have a WAR file that is customizable from outside the application, without having to repackage the application.