# 	Developing for Android
When creating a hybrid app for Android, the wizard generates the classes required by the Android SDK, including AndroidApplication.java, AndroidHybridActions.java, and SplashScreenActivity.java. It also generates the same classes used by the J2EE applications. When we are in mobile or web mode, many of the same principles apply, but they have different implementations.

On mobile the DarwinoHttpServer class is overridden, resulting in the DarwinoServiceDispatcher. In this class, you can define all of the services that you want to make available through the local HTTP Server. By default, its initServicesFactories() method calls a set of initialization methods:
- addResourcesServiceFactories() - provides all of the static resources: HTML, JavaScript, CSS, etc...
- addLibsServiceFactories() - serves the JavaScript and CSS libraries
- addJsonStoreServiceFactories() - provides the JSON Store REST services
- addSocialServiceFactories() - provides the User service (and more to come)
- addHybridServiceFactories() - provides the hybrid-specific services, such as commands
- addApplicationServiceFactories() - allows the creation of custom application services
- addLibrariesServiceFactories() - let you load libraries that register services through extension points. It will find an extension point for HTTPServiceFactory and it will add the result of that extension point. This lets you drop the library into the project and have its services automatically registered.

You can override any of these methods to stop it from registering its services. For example, if you won't be using the social services, you can override addSocialServiceFactories() and stop the services from being loaded.

The wizard’s output for a native app is smaller; there is no HTTP server included. The wizard will generate the DarwinoApplication class and the MainActivity, but it will leave creating the UI to the developer.
