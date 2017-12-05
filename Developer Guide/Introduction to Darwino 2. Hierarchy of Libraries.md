## Hierarchy of Libraries##

Darwino is at its core, a set of reusable and extendable components. Because they are both hierarchical and encapsulated you, as the developer, can choose to use whatever components you wish, and can choose not to implement other ones - of course, there's a few that are required, such as:

**Commons**

The root library includes all of the libraries required to build a basic Darwino application, and includes all of the libraries that are common and not platform-specific.

**Platform-Specific Libraries**	

Underneath the root, you'll find platform specific libraries for the various platforms that Darwino supports:

- Jre
    - J2EE
- Mobile
    - iOS
    - Android

You simply include the libraries that you need for the platforms you intend to support. Each library is made of several projets, and the dependencies are managed by Maven.

>If you use the Eclipse Darwino Application Wizard, all of the appropriate libraries are imported into your Application Project.
