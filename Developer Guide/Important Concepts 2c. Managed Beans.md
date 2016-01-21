

## Managed Beans

A key component of the Darwino Architecture is the concept of managed beans. This concept is borrowed from, although different from, Spring and JSF. Managed beans are manageable resources – Java objects that are instantiated by the platform when needed, and destroyed when they go out of scope.

Common managed beans include database connections, user directory connectors, and several services, such as the mail service.

Darwino uses managed beans primarily as a generic way to configure the platform.

### Accessing a managed bean
A managed bean is defined by a type, a name, and, eventually, aliases. For example, when the JSON store runtime looks for a database connection, it searches for a bean of type 'darwino/jsondb', with a specific name. The name generally comes from the Application object which defines the names to use by this application. When multiple names are used, then the runtime searches for a bean with the first name and, if it is not found, it tries the other names in order until it finds a bean that matches.

### Configuring Managed Beans
------
Managed beans are provided by extension points. You can define your managed beans' location as a custom extension. Where managed means are loaded from depends on the DefaultWebBeanExtension class. The class looks for beans in various places.

It first looks using JNDI. It looks for an entry in the path java:/comp/env/darwino-beans. If it finds one, it will either be a text file or a url pointing to a file. Either way, it will interpret the XML found there and load the bean accordingly.

Next it looks to the web application server, following the conventions for the various application servers.

After that, it looks in the classpath. It looks for a file called darwino-beans.xml within the current classpath.

Then it looks in WEB-INF for a darwino-beans.xml file. In addition, it will search there for a darwino-beans file with a name determined by the application's configuration files suffix, such as "bluemix'. The resulting file that Darwino will look for would then, in this case, be "darwino-beans.bluemix.xml". Thus, specifying the suffix determines which darwino-beans files will or will not be loaded.

After that, if there is a system property called "darwino-beans" containing either XML or a URL, then Darwino will load the beans specified within.

Finally, Darwino will look for an environment variable called "darwino-beans", and it wil process it just as it does the similarly-named system variable mentioned above.


Managed beans are configured using the following xml structure:
```xml
<bean type="[defined bean type]" name="[unique bean name]" class="[full class name]"
	alias "[optional alias names, separated by comma"/>  
	<property name='[property name]'>[property value]</property> //list of properties  
</bean>
```

Managed bean configurations can instantiate multiple sets of bean objects lists simply by using a list tag as follows:

```xml
<bean type="[defined bean type]" name="[unique bean name]" class="[full class name]"
	alias "[optional alias names, separated by comma"/>  
	<list name = "[list name]">
		<bean class='[class name]'>
			//property list for instance
		</bean>
		<bean class='[class name]'>
			//property list for instance
		</bean>
		<bean class='[class name]'>
			//property list for instance
		</bean>
		//etc.
	</list>			
</bean>
```

For nested beans, and if the bean class is an inner class of the main bean, the class name can simply be ".InnerClassName". In the example below...

```xml
<bean  class="com.acme.business.Finance" ....>
<property name="account">
  <bean  class="com.acme.business.Finance.Account" ....>
```
...the last statement can be shortened to:
```xml
  <bean  class=".Account" ....>
```

A list can also match a Java array, and Maps can be used as well:
 ```xml
<map name="properties">
	<entry key='....'>...value...</entry>
	<entry key='....'>...value...</entry>
</map>   
```

As you can see, this structure is intentionally generic. While the bean type must be defined based upon a definition, the remainder of the definition is completely generic. This allows for any type of object to be defined as a bean and managed by the platform.

If a call is made to a managed bean, the platform checks to see if the object exists and, if not, it instantiates the object according to this definition file, using the class name and configured property definitions. This leads to Darwino applications being defined in a very flexible and generic manner.

###Property references in managed bean definitions
When the Darwino code that parses managed bean definitions encounters a property name, it looks first to see if it is defined in the file as a local property. If it is not defined locally, it will then look to the platform for a property of that name. Finding it, it will use its value in the managed bean definition.

This allows you to drive the creation of the managed beans from outside the application.

Property references are of the form:
```
${propertyname[=default value]}
```

The goal with all of this is to have a WAR file that is customizable from outside the application, without having to repackage the application.

### Scope of Managed Beans
------

Managed beans have a defined scope. By default, if you do not provide a scope, the scope will be considered global, which means that the bean will be a singleton object, with a single instance for the entire application. Other scope choices are:

- **None:** A new instance of the bean is created on every call to the bean. The bean object is discarded after every call.  
- **Request:** A new instance of the bean is created on every request, meaning the developer can call the bean, then call multiple methods on the same instance of the bean. Once the bean object is out of scope, it will be discarded,  
- **Session:** A new instance of the bean is created and persisted for each session. Once the session is discarded, the bean object is discarded.  
- **Application:** A new instance of the bean is created for each calling application in a particular class loader.  



