The Platform Object
==
Darwino applications execute on multiple platforms, including iOS, Android, JVM, and a Web Container. Your code must handle differences in these platforms. The Darwino Platform Object manages these platform differences so that you do not have to worry about them.

Darwino is built to be platform agnostic, and exposes everything in the architecture as pluggable. The foundation of this capability is the Darwino Platform object, which encapsulates all platform-specific functionality. When working with multiple platform development, even simple tasks such as logging are managed differently depending upon on which platform the application is currently running. The Darwino Platform object allows you to interact with platform capabilities such as logging in an abstract manner, without having to determine the execution platform.

>NOTE: The Darwino Platform (com.darwino.commons.Platform) is instantiated as a [singleton](https://en.wikipedia.org/wiki/Singleton_pattern) object.

Services
--------

The Platform object provides several default services. See [Services and extensions](Important Concepts 2. Services and Extensions.md) for details.

Developers can extend the Platform object using custom services and extensions.  

>The Platform object is the entry point for all services, and the Platform object makes all services available from anywhere in your application. Further, any library can contribute services, as Darwino services are POJOs, and do not need to extend any particular interface.

Platform Configuration
----

In order to maintain platform agnosticism, Darwino does not depend on the use of fixed configuration files; instead, it is the Platform object that provides access to configuration properties.