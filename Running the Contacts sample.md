The Contacts sample is a simple project showing the capabilities of the Darwino platform:
- Sample view/form based application
- Runs as a web app as well as mobile hybrid on mobile devices
- Leverages the UI code generator for the UI

Installing the Contacts application in Eclipse
----------------------------------------------

The Contacts application is provided by the [darwino-demo](https://github.com/darwino/darwino-demo) Github repository as a set of maven enabled projects.
To import them in Eclipse once the Git repository has been cloned, select `Import...->Existing Maven Projects` and choose `darwino-demo/contacts`.

The result should be a set of projects in your workspace:

![](contactsprojects.png)

Note: if this is the first project you import, you might be prompted by Eclipse to install some M2E connectors. If this happens, just install the connectors and reboot Eclipse.

Running the Web Application
---------------------------

There are two ways to run the contact application:
- By starting TOMCAT using the Maven plugin. This is the simplest solution to start with.
- By installing the application within a full TOMCAT

The URL to the application is: [http://localhost:8080/contacts/mobile/index.html](http://localhost:8080/contacts/mobile/index.html)

The application rendering, with no data, should look like this:
![](contactswebhome.png)


### Running the application through Maven ###
You first have to build the project using Maven. For this, right click on the `contacts` root project and select `Maven install`:
![](contactsmaveninstall.png)

Once done, right click on the `contacts-j2ee` project and select `Maven build...`. In the goal field, enter `tomcat7:run`:
![](contactsmaventomcat.png)
If this is the first time TOMCAT is run this way, then all the dependencies will be downloaded by Maven, which will take a few minutes.


### Running the application using Eclipse WTP ###

![](runas-runonserver.png)

This will launch the app in Tomcat and open up the default landing page for the application. 


Android Applications
--------------------

The Android application is `contacts-android-hybrid`. To run it, right-click the project and choose Run As &rarr; Android Application:

![](runas-android.png)

iOS Applications
----------------

If you are running on a Mac, you can run the iOS application, which is `contacts-moe-native`. To do so, right-click on the project and choose one of the Run As &rarr; iOS Simulator App options:

![](runas-ios.png)

Adding data from IBM Notes/Domino
---------------------------
If you're an IBM Domino developer, then the Contacts application is a great example for setting up the replication with an existing NSF database

This assumes that you have some IBM Notes/Domino knowledge and both the Darwino runtime, as well as the Sync Admin database are properly installed on an IBM Domino server. It also assumes that the Darwino configuration files (beans ,properties) are setup, and finally the database JDBC driver in added to the Domino JVM. Be careful, as the Domino server can be executed as a system user and thus the user.home directory can be `C:\Windows\System32\config\systemprofile`.

 As a first, create a new NSF based on the template 'DarwinoContacts.ntf. This template has an XPages UI that you can reach using a URL like:
	[http://localhost/DarwinoContacts.nsf](http://localhost/DarwinoContacts.nsf)

Then, from the header bar of the XPages application, click the 'Admin' option and generate contacts documents by hitting 'Create 500 fake contacts'.

Once done, you can setup the synchronization between the Domino database and the Darwino one. For this, launch the synchronization admin UI, running on your Domino server:
	[http://localhost/DarwinoSync.nsf](http://localhost/DarwinoSync.nsf)

First, create an adapter: name it `contacts` to match the Darwino database name. Generate the definition by clicking 'Generate From Database' and selecting DarwinoContacts.nsf.
![](contactsdomadapter.png)

Once done, create a schedule service for this adapter and the proper Darwino connection bean:
![](contactsdomservice.png)

You can schedule the replication, or execute it once from the main replication service view. After the replication is run, then the Darwino application should show the data:
![](contactswebdata.png)
