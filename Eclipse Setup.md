Darwino Demo Environment Setup for Eclipse
==========================================

The included projects are intended for use with Eclipse Kepler and above (Luna recommended as of now). 

In order to configure web servers and get the best web development experience, please download and install the 'Eclipse IDE for Java EE Developers'.
[https://eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunar](https://eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunar "Download Eclipse")

Installing Eclipse is achieved by unzipping the archive in its target directory:

![](install-eclipse.png)

Launching Eclipse for the first time
------------------------------------

When Eclipse is launched for the first time, it prompts for a workspace directory. If the default value should work well, you can also make this directory a peer of the eclipse one, to get the whole environment contained within `c:\Darwino`:
![](install-workspace.png)

Configuring Eclipse
-------------------

Darwino requires the plug-ins bellow to be installed on top of the version of Eclipse. These plug-ins can generally be installed from the Eclipse Marketplace or by selecting `Help->Install New Software...`, then use the provided update site URL

- For developing Web (J2EE) applications
	- M2E WTP
Select `Help->Eclipse Marketplace...` and search for `m2e wtp`. Be careful to install m2e-wtp matching your Eclipse version and *not* m2eclipse-wtp, which is deprecated.
- If Android is a desired target platform
	- Android development toolkit (aka ADT), 
See: [http://developer.android.com/sdk/installing/installing-adt.html](http://developer.android.com/sdk/installing/installing-adt.html)
	- Android for Maven Eclipse ([http://rgladwell.github.io/m2e-android/](http://rgladwell.github.io/m2e-android/))
Select `Help->Eclipse Marketplace...` and search for `android m2e`.
- If iOS is a desired target platform
	- RoboVM, 
See: [http://robovm.com/develop/documentation/](http://robovm.com/develop/documentation/)


Maven Configuration
-------------------

The Maven settings differ based on whether you're using a [local repository](Maven Settings - Local Repo) or a [remote repository](Maven Settings - Remote Repo).

Android SDKs
------------

The Android projects use the ADT plugins for Eclipse, available from http://developer.android.com/sdk/installing/installing-adt.html. On Windows, it is advised to install it on: `c:\Android\SDK`
Using the SDK manager (Window -> Android SDK Manager), select and install at least the entire "Android 4.4.2 (API 19)" folder. Also, 

Additionally, set the path to the Android SDK root (e.g. c:\android-sdks) in an environment variable named ANDROID_HOME as well as in the Maven settings.xml above.

Note: using the stock Android emulator, it is likely that the internal web server for the Hybrid application will be too slow to launch for the app to work correctly, leading to a connection-failure dialog. It is better to run this application on real hardware or in a faster emulator/virtual machine.

Eclipse Import
--------------

Using "Import &rarr; Maven &rarr; Existing Maven Projects", import the projects from the darwino-demo folder.

In a new Eclipse installation, Eclipse will generate some errors about "Plugin execution not covered by lifecycle configuration" referring to several of the pom files. To fix this, choose the Quick Fix for the error that discovers new m2e connectors. After going through this process and installing the connectors, Eclipse will stop complaining.

J2EE Projects
-------------

The "dwo-demo-news-j2ee" and "dwo-jre-j2ee-devtools" apps are written to be run on [Tomcat](http://tomcat.apache.org), though would likely be fine on other servers as well.

The demo projects use a static list of users, defined in the dwo-demo-commons-triloggroup project, in the `com.triloggroup.demo.users.StaticTomcatUserService` class. To work with this default user pool, add at least the following users to the Tomcat server's tomcat-users.xml file:

	<role name="user"/>
	<user password="floflo" roles="user" username="atinov"/>
	<user password="floflo" roles="user" username="amass"/>
	<user password="floflo" roles="user" username="aboucher"/>
	<user password="floflo" roles="user" username="acalder"/>
	<user password="floflo" roles="user" username="agardner"/>
	<user password="floflo" roles="user" username="bchapot"/>
	<user password="floflo" roles="user" username="blemercier"/>
	<user password="floflo" roles="user" username="bchris"/>
	<user password="floflo" roles="user" username="bbright"/>
	<user password="floflo" roles="user" username="larmatti"/>
	<user password="floflo" roles="user" username="lbros"/>
	<user password="floflo" roles="user" username="mdavis"/>
	<user password="floflo" roles="user" username="pcollins"/>
	<user password="floflo" roles="user" username="rjordan"/>

PostgreSQL
----------

By default, the demo projects look for a [PostgreSQL](http://www.postgresql.org) database on the local host using:

Port: 5434
Database: dwodemo
Username: postgres
Password: postgres

These parameters are defined in the dwo-demo-commons-triloggroup project, in the `com.triloggroup.demo.users.DemoSqlContext` class.