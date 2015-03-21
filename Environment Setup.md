Darwino Demo Environment Setup
==============================

Eclipse
-------

The included projects are intended for use with Eclipse Kepler and above.


Maven Configuration
-------------------

The Maven settings differ based on whether you're using a [local repository](Maven Settings - Local Repo) or a [remote repository](Maven Settings - Remote Repo).

Android SDKs
------------

The Android projects use the ADT plugins for Eclipse, available from http://developer.android.com/sdk/installing/installing-adt.html . Using the SDK manager (Window -> Android SDK Manager), select and install at least the entire "Android 4.2.2 (API 17)" folder.

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