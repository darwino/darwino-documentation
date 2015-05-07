Darwino projects require Maven to be properly configured to point to the Darwino repository.

Setting up the Repository
-------------------------

Add the Darwino Maven repository to your settings.xml, along with your repo username and password.

Here is an example settings.xml containing the remote repository using the generic repo accessor username:

	<?xml version="1.0"?>
	<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
		<profiles>
			<profile>
				<id>darwino-repository-profile</id>
				<repositories>
					<repository>
						<id>darwino-repository</id>
						<name>Darwino Platform</name>
						<url>http://builds.darwino.com/artifactory/repo/</url>
					</repository>
				</repositories>
			</profile>
		</profiles>
		<activeProfiles>
			<activeProfile>darwino-repository-profile</activeProfile>
		</activeProfiles>
		<servers>
			<server>
			    <id>darwino-repository</id>
			    <username>repo</username>
			    <password>AP7d25JuFEitQAXAfkLb45Gn8xr</password>
			</server>
		</servers>
	</settings>

To merge this into an existing settings.xml, the pertinent components are the `<profile>...</profile>` block, including the named profile in the `activeProfiles`, and specifying the credentials used when accessing the repository.