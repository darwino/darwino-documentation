Setting Up Your environment
===========================
Darwino is a flexible development platform that supports IDE or command line
based development. The platform itself is built using [Apache Maven][1]. Maven
is also the recommended build tool for Darwino applications, but other systems
can also be used, including Gradle, Eclipse PDE, etc. The following instructions
assume that you are using:

[1]: <http://maven.apache.org/>

-   Eclipse J2EE as your IDE

-   Maven as your build tool.

-   NodeJS and NPM for JavaScript client development

Unless specified otherwise, all the installation instructions assume a Microsoft
Windows environment, with the software being installed on `c:\Darwino`. However,
most of the instructions work identically on other platforms such as Apple macOS
or Linux.

Darwino currently supports the following target platforms:

-   Web applications through a Java EE server

-   Android applications

-   Apple iOS applications

-   OSGi environments, like Eclipse rich client or IBM Domino

Notes: Based on Apple requirements, an Apple Mac computer running macOS is
required for developing Apple iOS applications. Currently, only Android
applications can be developed and tested using Microsoft Windows or Linux.

Darwino Installation
--------------------

In order to develop with the Darwino Studio, the following components should be
installed:

- Java JDK: The minimum version is Java 7, but it is advised to use the
latest one (Java 8 as of today)
- Eclipse for J2EE or other IDE  
- Web application server is required to run the web applications. Although any
servlet container 2.5+ would work, these instructions show how to install/run
the demo projects using Apache TOMCAT  
- Database server: These instructions document the use of Postgres 9.4+
database server. Although other databases may be used, the demo applications are
pre-configured to work with PostgreSQL. (Note that a future version of Darwino
might use a embedded database and thus this step won't be necessary.)

Also, a few configuration actions has to be taken:

- Configure the Maven settings to point to Darwino repository
- Configure the NPM settings to point to Darwino repository
- Configure the Darwino beans and properties

Note that since Darwino 1.5.1, the Darwino Eclipse studio features a configuration
module that makes the installation process easier. It is highly recommended to use
this helper.

The following instructions describe the installation of each component.

Installing the Java JDK
-----------------------

*If you already have Java JDK 7 or higher installed (Java 8 is highly recommended),
you can skip this step.*

A full Java JDK is required by the maven tools to compile the Java. A Java JRE
is not sufficient.

The Java SDK can be downloaded from the Oracle web site:
<http://www.oracle.com/technetwork/java/javase/downloads/index.html>. An IBM JDK
will also work. Once installed, make sure that the development environment
variable JAVA\_HOME is pointing to you Java environment. Make also sure that the
JVM is available from the command line (Terminal on macOS or Linux) by using the
command `java -version`.

![](<install-java-version.png>)

Installing the Android application development tools
----------------------------------------------------

Developing for the Android platform requires the Android Software Development
Kit to be installed. It is available from:
<http://developer.android.com/sdk/index.html#Other>. The 'SDK Tools only'
package is sufficient if you plan to use Eclipse or Maven from the command line.

Using the SDK manager [Android SDK manager][3], select and install at least the
entire "Android 4.2.2 (API 17)" & "Android 4.4.2 (API 19)" folders.

[3]: <http://developer.android.com/tools/help/sdk-manager.html>

![](<install-android-pkg.png>)

Additionally, set the path to the Android SDK root (e.g. `c:\Android\SDK`) in an environment variable named `ANDROID_HOME`. Also do not forget, when installing maven, to add the android libraries to your local repository, as described in the documentation on installing Maven. The Studio configuration module will also report an error if this is not properly defined.

Note: using the stock Android emulator, it is likely that the applications will
be too slow to provide a great developer experience. This is particularly true
if you launch the emulator from a virtual machine. It is then better to run the
demo applications on real hardware or in a faster emulator/virtual machine. See:
<http://blog.riand.com/2014/08/running-android-apps-for-development.html>

Installing the iOS application development tools
------------------------------------------------

As stated earlier, developing for iOS requires an Apple Mac computer running OS
X. To compile, run in a simulator or deploy the application, the Apple Xcode
development environment is required. It is available freely from the Mac App
Store (<https://itunes.apple.com/us/app/xcode/id497799835?mt=12>).

PostgreSQL
----------

Postgresql is a free and popular relational database featuring well suited JSON extensions for Darwino. The instructions bellow show how to install Postgreql for development and test purposes. Other databases, like IBM DB2 10.5 FP8 or MS SqlServer 2016, are also supported.

See: [Configuring PostgreSQL](Configuring-PostgreSQL.md)

Installing Eclipse for J2EE
---------------------------

We advise developers to use Eclipse as the development IDE. Moreover, starting with Darwino 1.5.1, the Darwino Eclipse plugins feature an installation validation module that checks for the pre-requisites, and helps fixing the potential issues.

If you plan to use Eclipse, or already do, please ensure that you first ensure
that you follow the [instructions] (Installing Eclipse) for downloading and
configuring Eclipse, **and the Darwino Studio Eclipse add-on component**. These
instructions assume that you will use the Eclipse for J2EE version (Not Eclipse
for Java).

NPM Installation
-----------------

NPM should be configured to point to the Darwino repository for Darwino
libraries, identified by @darwino.

You should first install NodeJS and NPM following the instructions here: [https://www.npmjs.com/get-npm](https://www.npmjs.com/get-npm)

Then you should configure NPM to connect to the Darwino private repository. See: [Configuring NPM](NPM-Settings.md)

Manual configuration
--------------------

These steps are only required if they were not achieved through the Studio configuration
module.

### Maven Configuration ###

Maven should be configured to point to the Darwino repository for Darwino
libraries. If you are using Eclipse, please see the following instructions after
installing Eclipse: [Setting Up Maven][2].

[2]: <Maven%20Settings>

(If you are using the command line tools, then you should first install maven
(latest recommended) and configure it as described in the instructions.)


### Apache TOMCAT ###

Apache Tomcat is a free, easy to use, Java servlet container. The instructions bellow explain how to configure it for development and test purposes. Any servlet 3.0 compliant web server can also be used.

See: [Configuring TOMCAT](Configuring-Tomcat.md)

Note that TOMCAT can also be launch from Maven. Most of the demo projects, as well as the new
application wizard, now integrate the TOMCAT Maven plugin. As a result, TOMCAT can be launched
using a maven commend without going though a full installation:
	`mvn tomcat7:run`

### IBM Websphere Liberty ###

Alternatively, you can use IBM Websphere Liberty as the application server, on premises and on the cloud through IBM Bluemix.

See: [Configuring IBM Websphere Liberty](Configuring-WasLiberty.md)
