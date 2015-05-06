Maven Settings
==============

Darwino projects require Maven to be properly configured to point to the Darwino repository. This repository can be local, or remote


Local Repository
----------------

Add paths to the local Darwino and Darwino Dependencies repositories to Maven's ~/.m2/settings.xml file.

	<profiles>
	  <profile>
	    <id>darwino</id>
	    <properties>
	      <darwino-repo-url>file:///c:/phildev/Workspace-luna/mvnrepo/mvnrepo</darwino-repo-url>
	    </properties>
	    <repositories>
	      <repository>
	        <id>darwino-repo</id>
	        <name>Darwino repository</name>
	        <url>${darwino-repo-url}</url>
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
	  <activeProfile>darwino</activeProfile>
	</activeProfiles>

JESSE, THIS IS OLD, right?

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


Remote Repository
-----------------

Add the Darwino Maven repository to your settings.xml, along with your repo username and password.

JESSE, THIS IS OLD, right?

	<?xml version="1.0"?>
	<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
		<profiles>
			<profile>
				<id>main</id>
				<properties>
					<android.sdk.path>C:\path\to\android-sdks</android.sdk.path>
					<darwino-repo-url>http://builds.darwino.com/artifactory/repo/</darwino-repo-url>
				</properties>
				<repositories>
					<repository>
						<id>darwino-repository</id>
						<name>Darwino Platform</name>
						<url>${darwino-repo-url}</url>
					</repository>
				</repositories>
			</profile>
		</profiles>
		<activeProfiles>
			<activeProfile>main</activeProfile>
		</activeProfiles>
		<servers>
			<server>
			    <id>darwino-repository</id>
			    <username>repo</username>
			    <password>AP7d25JuFEitQAXAfkLb45Gn8xr</password>
			</server>
		</servers>
	</settings>