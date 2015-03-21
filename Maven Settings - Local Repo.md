Maven Settings - Local Repo
===========================

Add paths to the local Darwino and Darwino Dependencies repositories to Maven's ~/.m2/settings.xml file.

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