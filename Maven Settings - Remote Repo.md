Maven Settings - Remote Repo
============================

Add the Darwino Maven repository to your settings.xml, along with your repo username and password.

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