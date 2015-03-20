Darwino Demo Environment Setup
==============================

Eclipse
-------

The included projects are intended for use with Eclipse Kepler and above.


Maven Configuration
-------------------

Add a path to the "darwino-maven-repo" directory to Maven's ~/.m2/settings.xml file.

	<?xml version="1.0"?>
	<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
		<profiles>
			<profile>
				<id>main</id>
				<properties>
					<android.sdk.path>C:\path\to\android-sdks</android.sdk.path>
					<darwino-deps-url>file://c:/path/to/mvnrepo</darwino-deps-url>
					<darwino-repo-url>file://c:/path/to/darwino-repo</darwino-repo-url>
				</properties>
				<repositories>
					<repository>
						<id>darwino-platform</id>
						<name>Darwino Platform</name>
						<url>${darwino-repo-url}</url>
					</repository>
					<repository>
						<id>darwino-dependencies</id>
						<name>Darwino Dependencies</name>
						<url>${darwino-deps-url}</url>
						<layout>default</layout>
						<releases>
							<enabled>true</enabled>
							<updatePolicy>never</updatePolicy>
						</releases>
						<snapshots>
							<enabled>true</enabled>
							<updatePolicy>never</updatePolicy>
						</snapshots>
					</repository>
				</repositories>
			</profile>
		</profiles>
		<activeProfiles>
			<activeProfile>main</activeProfile>
		</activeProfiles>
	</settings>

Android SDKs
------------

The Android projects use the ADT plugins for Eclipse, available from http://developer.android.com/sdk/installing/installing-adt.html . Using the SDK manager (Window -> Android SDK Manager), select and install at least the entire "Android 4.2.2 (API 17)" folder.

Additionally, set the path to the Android SDK root (e.g. c:\android-sdks) in an environment variable named ANDROID_HOME as well as in the Maven settings.xml above.

Note: using the stock Android emulator, it is likely that the internal web server for the Hybrid application will be too slow to launch for the app to work correctly, leading to a connection-failure dialog. It is better to run this application on real hardware or in a faster emulator/virtual machine.

Eclipse Import
--------------

Using "Import -> Maven -> Existing Maven Projects", import the projects from the dwo-android folder and then the darwino-demo folder.

After initial import, run a basic Maven build (for example, Run As -> Maven build -> goal "compile") on the dwo-android project to make sure all of the dependencies from the Darwino Maven repository are put in place.

J2EE Projects
-------------

The "dwo-demo-news-j2ee" app is written to be run on Tomcat (http://tomcat.apache.org), though would likely be fine on other servers as well.