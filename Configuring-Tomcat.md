If you plan to use Tomcat as the development web application server, then you have to both install it in the file system and eventually configure Eclipse to use it.


Installing Apache Tomcat
------------------------
Download the latest Tomcat server (8.x) from the Apache Web Site: [https://tomcat.apache.org/download-80.cgi](https://tomcat.apache.org/download-80.cgi "Download Tomcat").
Just unzip the server under your installation directory:
![](install-tomcat.png)


If you plan to use the demo applications, then you need to add some demo users with roles in your tomcat environment. Add the following content to `{tomcat install dir}\conf\tomcat-users.xml` :

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
    


Configuring Eclipse with Tomcat
-------------------------------
Eclipse J2EE comes with a set of tools called WTP, which allows the configuration and use of application servers.
To configure Apache TOMCAT, you have to create a new 'Runtime Environment' from the Eclipse 'Window->Preferences...':
![](eclipse_tomcat1.png)
![](eclipse_tomcat2.png)


Once the runtime environment is configured, you should create a new server. For this, you should either make visible the Eclipse 'Servers' view (Window->Show View...Servers) or switch to the J2EE perspective (Window->Open Perspective...Java EE).
In the server view, create a new server by hitting the link bellow. If the view displays a list instead, right click and select 'New->Server...'
![](eclipse_tomcat3.png)
Select your Tomcat environment and eventually change the server name, and hit 'Finish'
![](eclipse_tomcat4.png)

Download the [darwino-beans.xml](http://github.com/darwino/darwino-demo/wiki/darwino-beans.xml) and [darwino.properties](http://github.com/darwino/darwino-demo/wiki/darwino.properties) files to the Tomcat install directory.

![](eclipse_tomcat6.png)

Now you have your server ready for the Darwino applications
![](eclipse_tomcat5.png)