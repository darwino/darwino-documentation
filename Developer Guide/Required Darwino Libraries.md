Required Darwino Libraries
==

Rather than delivering the entire framework as a single library, Darwino is organized into a set of hierarchical libraries. These libraries are organized by platform. For instance, if a library is particular to Android, it is part of the Android library hierarchy, if a library is a core, common component of the platform, it is part of the commons hierarchy. If a library is required for the web server, it is part of the jre hierarchy. The base Darwino hierarchy is:

-dwo (Darwino)  
-dwo-anrdoid  (libraries required for building Android apps)  
-dwo-commons  (libraries required for building cross-functional Darwino apps)  
-dwo-dependencies  (I don't know)  
-dwo-expr  (I don't know) 
-dwo-ios  (libraries required for building ios Apps)  
-dwo-jre (libraries required for building web-based Darwino apps)  
-dwo-jsonstore (libraries required for Darwino DB)  
-dwo-mobile (I don't know)  
-dwo-p2      
-dwo-platform    
-dwo-project  
-dwo-rdbc

(click on the links above for more information about the structure of these libraries)

>As a developer, you have the freedom to include any particular libraries (or sub-libraries) that you wish to use in your code.  *(When using the Darwino Studio, Darwino Studio will automatically include all of the necessary libraries to build Darwino apps for your selected platforms.)* For example, if you choose to create web-based application, Darwino studio would automatically include libraries from the commons and jre hierarchy. If your application uses Darwino DB, the studio automatically includes the required libraries from the jsonstore hierarchy.






