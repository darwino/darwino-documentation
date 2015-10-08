## Services and extensions
There are two elements in the platform: services and extensions. A service is a singleton. For a particular class of service, there will be ONE implementation. If there are multiple implementations, you will get a runtime error.

Services and extensions differ in that an extension can have multiple implementations and they can all work together at the same time, like, for example, a database connection. You can have a connection to postgres and a connection to DB2 and use both of them within the same app. An extension is like a service but it can have multiple instances that are non-exclusive.

To define extensions, there is the notion of plugins. A plugin is a class that can be provided by libraries or by your application directly, and they can register services and extensions to the platform. 

The Darwino wizard will generate a skeleton plugin for your application that you can use to override or add to the default platform implementation.

## Logging
Darwino provides its own logging library for each platform, and dynamically chooses the appropriate one for current platform. This make it completely transparent to the developer; your logging code will be fully cross-platform.

## Managed Beans

A key component of the Darwino Architecture is the concept of managed beans. This concept is borrowed from, although different from, Spring and JSF. Managed beans are manageable resources – java objects that are instantiated by the platform when needed, and destroyed when they go out of scope.

Darwino uses managed beans primarily as a generic way to configure the platform.

### Configuring Managed Beans
------
Managed beans are provided by extension points. By default, the platform looks for managed beans configured in a file called darwino-beans.xml in the Tomcat server home/conf directory. However, you can define your managed beans location as a custom extension.

The set of managed beans is typically configured using the darwino-beans.xml file, but can also be provided from other sources. Managed beans are configured using the following xml structure:
```xml
<bean type="[defined bean type]" name="[unique bean name]" class="[full class name]" alias "[optional alias names, separated by comma"/>  
	<property name='[property name]'>[property value]</property> //list of properties  
</bean>
```

Managed bean configurations can instantiate multiple sets of bean objects lists simply by using a list tag as follows:

```xml
<bean type="[defined bean type]" name="[unique bean name]" class="[full class name]" alias "[optional alias names, separated by comma"/>  
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


As you can see, this structure is intentionally generic. While the bean type must be defined based upon a definition, the remainder of the definition is completely generic. This allow for any type of object to be defined as a bean, and managed by the platform.

If a call is made to a managed bean, the platform checks to see if the object exists and, if not, it instantiates the object according to this definition file, using the class name and configured property definitions. This leads to a very flexible and generic manner in which Darwino applications can be defined.

### Scope of Managed Beans
------

Managed beans have a defined scope. By default, if you do not provide a scope, the scope will be considered global, which means that the bean will be a singleton object, with a single instance for the entire application. Other scope choices are:

- **None:** A new instance of the bean is created on every call to the bean. The bean object is discarded after every call.  
- **Request:** A new instance of the bean is created on every request, meaning the developer can call the bean, then call multiple methods on the same instance of the bean. Once the bean object is out of scope, it will be discarded,  
- **Session:** A new instance of the bean is created and persisted for each session. Once the session is discarded, the bean object is discarded.  
- **Application:** A new instance of the bean is create for each calling application in a particular class loader.  



