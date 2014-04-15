# Prerequisites and assumed conditions

- Mac OS X 10.9.2
- Clean install
- Admin user (required to install Xcode cli dev tools, Virtualbox application)
- Network connection

## Installation

### CLI tools Needed:

- Bash .profile file for setting options and env variables

	```bash
	[ -f ~/.profile ] || { touch ~/.profile;}
	```

- Java JDK 1.7.0_21+

	UI-based install: http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html

	Set JAVA_HOME and JAVA_OPTS
	```bash
	echo 'export JAVA_HOME=$(/usr/libexec/java_home)' >> ~/.profile
	echo "export JAVA_OPTS='-Xms512m -Xmx1024m -XX:MaxPermSize=512m'" >> ~/.profile
	source ~/.profile
	```
	Verify:

	```bash
	which java && java -version
	/usr/bin/java
	java version "1.7.0_51"
	Java(TM) SE Runtime Environment (build 1.7.0_51-b13)
	Java HotSpot(TM) 64-Bit Server VM (build 24.51-b03, mixed mode)

	echo $JAVA_HOME
	/Library/Java/JavaVirtualMachines/jdk1.7.0_51.jdk/Contents/Home

	echo $JAVA_OPTS
	-Xms512m -Xmx1024m -XX:MaxPermSize=512m
	```

- Xcode CLI dev tools
	UI-based install

	```bash
	sudo xcode-select --install
	```

	```bash
	xcode-select -p
	/Applications/Xcode.app/Contents/Developer
	```

- Homebrew

	Run the install script:
	```bash
	ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go/install)"
	echo "export PATH='/usr/local/bin:$PATH'" >> ~/.profile
	source ~/.profile
	```

	Check additional install requirements:
	```bash
	brew doctor
	Your system is ready to brew.
	```

	To verify:
	```bash
	which brew
	/usr/local/bin/brew
	```

- Subversion 1.7.x
	```bash
	brew tap homebrew/versions
	brew install subversion17
	```

	Verify:
	```bash
	which svn && svn --version

	/usr/local/bin/svn
	svn, version 1.8.8 (r1568071)...
	```

- Maven 3.0.4+
	```bash
	brew install homebrew/versions/maven30
	```

	Verify:

	```bash
	which mvn && mvn --version
	```

	```return
	/usr/local/bin/mvn
	Apache Maven 3.0.5 (r01de14724cdef164cd33c7c8c2fe155faf9602da; 2013-02-19 05:51:28-0800)
	Maven home: /usr/local/Cellar/maven30/3.0.5/libexec
	Java version: 1.7.0_51, vendor: Oracle Corporation
	Java home: /Library/Java/JavaVirtualMachines/jdk1.7.0_51.jdk/Contents/Home/jre
	Default locale: en_US, platform encoding: UTF-8
	OS name: "mac os x", version: "10.9.2", arch: "x86_64", family: "mac"
	```

	- Set M3_HOME env variable and add to path

	```bash
	echo "export M3_HOME=/usr/local/Cellar/maven30/3.0.5/libexec" >> ~/.profile
	echo "export PATH=$M3_HOME/bin:$PATH" >> ~/.profile
	source ~/.profile
	```

	- Set MAVEN_OPTS env variable
	```bash
	echo "export MAVEN_OPTS='-Xms512m -Xmx1024m -XX:MaxPermSize=512m'" >> ~/.profile
	source ~/.profile
	```

	- Configure maven local repo settings.xml and .m2

	```bash
	[ -d ~/.m2 ] || { mkdir ~/.m2 && touch ~/.m2/settings.xml;}
	```

	Copy/Paste the following in settings.xml:

	```xml
	<settings xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
	                   http://maven.apache.org/xsd/settings-1.0.0.xsd">
	<activeProfiles>
	 <activeProfile>kuali-developer</activeProfile>
	</activeProfiles>

	<pluginGroups>
	<!-- The plugin group to search for gwt plugin -->
	  <pluginGroup>com.totsp.gwt</pluginGroup>
	</pluginGroups>

	<mirrors>
	  <mirror>
	    <id>kuali.private</id>
	    <url>http://nexus.kuali.org/content/groups/developer</url>
	    <mirrorOf>*</mirrorOf>
	  </mirror>
	</mirrors>
	<servers>
	<server>
	  <id>kuali.private</id>
	  <username>developer</username>
	  <password>THE_PASSWORD</password>
	</server>
	</servers>

	<profiles>

	<!-- Profile used to select kuali developer repositories -->
	<profile>
	 <id>kuali-developer</id>
	  <properties>
	    <!-- Turn off GWT compilation if you are not developing any UI -->
	    <!-- <ks.gwt.compile.phase>none</ks.gwt.compile.phase> -->
	    <!-- Skip unit tests -->
	    <!-- <maven.test.skip>true</maven.test.skip> -->
	    <!-- Skip gwt compiles for all browsers except firefox -->
	    <ks.gwt.module.org>org.kuali.student.core.organization.ui.OrgEntryFirefox</ks.gwt.module.org>
	    <ks.gwt.module.lum>org.kuali.student.lum.lu.ui.main.LUMMainFirefox</ks.gwt.module.lum>
	    <ks.gwt.module.collab>org.kuali.student.lum.lu.ui.workflow.CollaboratorRequestFirefox</ks.gwt.module.collab>
	  </properties>
	</profile>


	</profiles>
	</settings>
	```

### Additional Recommended Tools:

- wget
- Textmate or Sublime Text 2/3

## KS Dev Guide

### Create a project

- Create a project directory structure

	```bash
	sudo mkdir -p /opt/kuali
	sudo chown -Rv YOUR_MAC_USERNAME /opt
	sudo chmod -R +rwX /opt
	```

- Checkout the Kuali Student aggregate trunk

	```bash
	cd /opt/kuali/ && svn co https://svn.kuali.org/repos/student/enrollment/aggregate/trunk trunk
	```

	Note: Accept cert permanently when prompted, wait for checkout procedure to finish

### Set up the DB environment

- Install Virtualbox (UI-based install) - Requires admin permissions

	http://download.virtualbox.org/virtualbox/4.3.8/VirtualBox-4.3.8-92456-OSX.dmg


- Install Vagrant (UI-based install) - Requires admin permissions

	https://dl.bintray.com/mitchellh/vagrant/vagrant_1.5.1.dmg

- Download the Oracle driver (UI-based install, must have Oracle account)

	Always choose the Linx x64 driver, even on a Mac

	http://www.oracle.com/technetwork/products/express-edition/downloads/index.html

- Clone the vagrant-ubuntu-oracle-xe git repo into the project dirctory

	```bash
	git clone git://github.com/hilverd/vagrant-ubuntu-oracle-xe /opt/kuali/vagrant-ubuntu-oracle-xe
	```

- Copy previously downloaded oracle driver into vargrant directory

	```bash
	cp ~/Downloads/oracle-xe-11.2.0-1.0.x86_64.rpm.zip /opt/kuali/vagrant-ubuntu-oracle-xe/modules/oracle/files
	```

- Install vagrant vbguest plugin
	```bash
	cd /opt/kuali/vagrant-ubuntu-oracle-xe && vagrant plugin install vagrant-vbguest
	```

- Start vagrant, install and configure Oracle 11g (this can take a while)
	```bash
	cd /opt/kuali/vagrant-ubuntu-oracle-xe/
	vagrant up
	```

- Install the Oracle JDBC Driver into your local maven repo
	```bash
	mvn install:install-file -DgroupId=com.oracle -DartifactId=ojdbc6_g -Dversion=11.2.0.2 -Dpackaging=jar -Dfile=ojdbc6_g.jar
	```

### Build the code!

#### Local KS Settings

- Create a local ks-config.xml file

	```bash
	[ -d ~/kuali/main/dev ] || { mkdir -p ~/kuali/main/dev && touch ~/kuali/main/dev;}
	```

#### Run KS Impex

- Update impex process
	```bash
	cd /opt/kuali/trunk && mvn clean install -Pimpex-only -DskipTests=true
	```

- Run the impex against the db (must have oracle running -- vagrant up) (this takes quite a while to complete)
	```bash
	cd /opt/kuali/trunk/ks-deployments/ks-dbs/ks-impex/ks-impex-bundled-db && mvn initialize -Pdb,oracle -Doracle.dba.password=manager -DskipTests=true
	```

#### Do a full maven build on the codebase

```shell
cd /opt/kuali/trunk && mvn -U clean install -DskipTests=true -Pbundled-only-war -Dks.gwt.compile.phase=none
```

#### Install and configure a jetty server

- Download and install Jetty 8



- Configure the Jetty server port


#### Conclusion

This concludes the manual environment setup. From here, a dev should be able to update from version control, run impex data, and do maven builds on the application.

To proceed with the dev setup, remove this repo as we will check it out appropriately from VCS with IntelliJ from here out:

```bash
rm -r /opt/kuali/trunk
```

### IntelliJ setup

- Download/Install IntelliJ IDEA Ultimate v12.x from here:
	- http://download.jetbrains.com/idea/ideaIU-12.1.7.dmg

- Configure IntelliJ application options



- Start IntelliJ12

- No settings to import, skip this

- Use the IntelliJ License key found here (authenticated):

- Accept the license and continue

- Select Plugins on next screens
	- Version control
		- Subversion only
	- Web/Java EE
		- Bean validation
		- Database
		- Free Marker
		- Java EE
		- Java Server pages
		- Persistence Frameworks
		- Spring (select all)
		- SQL
		- WebServices
	- Application server
		- Jetty
		- Tomcat
	- HTML/JS Dev
		- Everything but SASS
	- Other
		- Ant
		- Commander
		- Copyright
		- Eclipse
		- GenerateToString
		- Groovy
		- Inspection Gadgets
		- Intention Power Pack
		- JUnit
		- Maven
		- Maven Integration Extension
		- Refactor-X
		- Structural Search
		- Type Migration
		- UML

- Select finish, ignore any upgrade notifications
- Select Checkout from VCS
	- Enter this url as a new remote repository: https://svn.kuali.org/repos/student
	- Navigate to enrollment/aggregate/trunk and click Checkout
	- Destination directory: /opt/kuali, click ok
	- Checkout as: /opt/kuali/trunk, click ok
	- Select 1.7 format, click ok

# Resources used in doc creation

- VMWare Fusion 6
- Mac OS X 10.9.2 Guest OS in VM (action, comparison)
