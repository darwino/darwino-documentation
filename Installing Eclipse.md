Installing the Eclipse IDE
--------------------------

Although the use of an IDE, and in particular Eclipse, is not a requirement, it
certainly eases the development of Darwino applications.

In order to configure web servers and get the best web development experience,
please download and install the latest version of '**Eclipse IDE for Java EE
Developers**‘ (NOT Eclipse IDE for Java Developers).
<http://www.eclipse.org/downloads/>

Darwino has been validated with Eclipse 4.5.1, also known as Mars-1.

Installing Eclipse is achieved by unzipping the archive in its target directory.
The following directory structure shows eclipse unzipped into the c:\\Darwino
directory:

![](<install-eclipse.png>)

Launching Eclipse for the first time
------------------------------------

When Eclipse is launched for the first time, it prompts for a workspace
directory.  The default value is fine, but well, you could also make this
directory a peer of the eclipse one, to get the whole environment contained
within `c:\Darwino`:

![](<install-workspace.png>)

Configuring Eclipse
-------------------

Darwino requires some extra plug-ins bellow to be installed on top of the
version of Eclipse. These plug-ins can generally be installed from the Eclipse
Marketplace or by selecting `Help->Install New Software...`, then use the
provided update site URL

-   For developing Web (J2EE) applications
    -   M2E WTP **Note**: your version of Eclipse J2EE edition, if it is recent enough, might already have this feature installed. In this case, just skip this step. Select `Help->Eclipse Marketplace...` and search for `m2e wtp`. Be careful to install m2e-wtp matching your Eclipse version and *not* m2eclipse-wtp, which is deprecated.

-   If Android is a desired target platform
    -   Android Development Tools (aka ADT), Select `Help->Eclipse->Marketplace...` and search for `android adt`.
    -   Android for Maven Eclipse Select `Help->Eclipse Marketplace...` and search for `m2e android`.

-   If iOS is a desired target platform
    -   RoboVM, Follow the instructions under "*RoboVM for Eclipse*" from
<http://docs.robovm.com/getting-started/eclipse.html>

-   If IBM Domino is a desired target platform
    -   The Darwino project wizard as well as the sample applications use Tycho to target OSGi platforms (Eclipse, IBM Domino...). As a result, it requires the Tycho m2e connector to be installed. The easiest way to get it installed is to wait from Eclipse to display a build problem (see bellow), right click on the issue and choose quick fix. Eclipse will automatically propose to download and install the connector.
![](<eclipse-maventycho.png>)


Installing the Darwino Studio add-on
------------------------------------

The Darwino studio provides a set of tools and wizards. It is available from an Eclipse update site located.

Bellow are the instructions:

1.  Select Help \> Install New Software.

2.  In the "Work With:" field, enter the following URL, depending on the edition you selected:
    Enterprise Edition: <https://eclipse.darwino.com/darwino/studio/enterprise>
    Community edition: <https://eclipse.darwino.com/darwino/studio/community>

3.  When prompted, enter your Darwino user and password, as provided during the registration process.

4.  In the Available Software dialog, select the Darwino Studio checkbox.

5.  In the next window, you'll see a list of the tools to be downloaded. Click
    Next.

6.  Read and accept the license agreements, then click Finish.

7.  If you get a security warning saying that the authenticity or validity of
    the software can't be established, click OK.

8.  When the installation completes, restart Eclipse.

Eclipse Add-ons Information
---------------------------

-   Darwino Studio

    -   Update site URL:
        <https://eclipse.darwino.com/darwino/studio/enterprise>
        <https://eclipse.darwino.com/darwino/studio/community>

-   Android Development Tools

    -   Instructions:
        <http://developer.android.com/sdk/installing/installing-adt.html>

    -   Update site URL: <https://dl-ssl.google.com/android/eclipse/>


-   Android for Maven Eclipse

    -   Instructions: <http://rgladwell.github.io/m2e-android/>

-   RoboVM

    -   Instructions: <http://docs.robovm.com/getting-started/eclipse.html>

    -   Update site URL: <http://download.robovm.org/eclipse/>

-   m2e-wro4j connector

    -   Web site: <https://github.com/jbosstools/m2e-wro4j>.

    -   Update site URL:
        <http://download.jboss.org/jbosstools/updates/m2e-wro4j/>
