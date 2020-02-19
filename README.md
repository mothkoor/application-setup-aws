# Instructions for setting up your web application on Amazon Web Services ( Steps include JAVA, Tomcat and MYSQL Setup)

# Activate AWS & Create instance

•	Activate the credit card account

•	Create EC2 Instance (Free tier allowed)

•	Check Public IP details which include
•       Server name: <<XX>>.compute.amazonaws.com
•	IP:  AA.BB.CC.DD
	
•	Download key pair to the local system. The key pair file (.pem file) will be used for connecting to the Amazon application server instance

# CyberDuck setup (Cyberduck for Mac, Windows user can use winscp/putty)

•	To connect using Cyberduck
Specify public DNS: Servername/PublicIP (created above)

•	User name: ec2-user

•	Password: empty

•	Pass the PEM file by using browse option

# Install JAVA on Amazon Application server instance

•	Goto the folder where your pem key is located

•	In the terminal/command prompt, type below command

	>>ssh -i "xx.pem" server-name
	
•	Install the latest Java JDK 11 by running below on SSH client

	>>sudo amazon-linux-extras install java-openjdk11
	
•	Check whether java is installed by typing

	>> java -version
	
•	Set JAVA_HOME by adding below lines in /etc/profile

	export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-11.0.5.10-0.amzn2.x86_64
	export JRE_HOME=/usr/lib/jvm/jre-11-openjdk-11.0.5.10-0.amzn2.x86_64
	export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
	
•	Switch to root user before adding below lines in profile. To switch follow below commands

	sudo su
	passwd root
	create a new password for root

# Tomcat setup

•	Download latest version of tomcat:- https://tomcat.apache.org/download-90.cgi 

•	Copy the file through putty/Cyberduck

•	Transfer the tar file to Linux EC2 server and unzip

•	Use tax -zxvf <<>>.jar. If there is error in gzip use, then just use command tar xvf <<>>.tar

•	Goto tomcat bin folder to start and shutdown the instance

•	Try accessing on public IP at 8080 port

•	If this does not work, check inbound traffic on the EC2 aws console. Add port 8080 to inbound traffic (Goto security groups option on AWS console)

•	To change port goto server.xml at /home/ec2-user/apache-tomcat-9.0.27/conf

•	To restart tomcat server automatically when the ec2 restarts, create following script in /etc/rc.d/init.d/

	!/bin/sh
	# Tomcat init script for Linux.
	# chkconfig: 234 20 80
	# description: The Apache Tomcat servlet/JSP container.
	JAVA_HOME=/usr/lib/jvm/java-11-openjdk-11.0.5.10-0.amzn2.x86_64
	CATALINA_HOME=/home/ec2-user/apache-tomcat-9.0.27
	export JAVA_HOME CATALINA_HOME
	exec $CATALINA_HOME/bin/catalina.sh $*

•	Launch below commands for enabling tomcat restart automatically

	chmod 755 /etc/rc.d/init.d/tomcat
	chkconfig --level 2345 tomcat on

•	Setting up tomcat users by adding below lines in tomcat-users.xml

	<role rolename="manager-gui"/>
	<role rolename="manager-script"/>
	<role rolename="manager-jmx"/>
	<role rolename="manager-status"/>
	<role rolename="admin-gui"/>
	<user username="admin" password="admin" 
	roles="manager-gui,manager-status,admin-gui"/>
	<user username="tomcattools" password="tomcat"/>

•	If access is still denied, try commenting value in context.xml at /home/ec2-user/apache-tomcat-9.0.27/webapps/manager/META-INF

•	In the brew set-up, if you want to check the folder where tomcat is installed just use command 

	>>brew ls tomcat

# MySQL Setup

•	Goto Amazon console-->Services-->Storage-->RDS

•	Create mysql8.0+ database. Database name: <<>> Username: <<>> Password: <<>>

•	Connecting to it using below command

•	mysql -h <<servername>> -P <<port>> -u <<username>> -p
	
•	Cannot connect to DB Server. Try ping to <<DB Server name>>
	
•	Try adding your public ip in the inbound and outbound traffic of the database instance. Specifying TCP, 3306 and MYSQL Type.


# Cannot fire http/https requests from EC2 instance

•	Check Security groups,make sure outbound settings allow all port access

•	Check NACL groups, make sure outbound settings allow all port access

•	Check if Internet gateway is configured

•	Login to EC2 instance and try access the server you intend to send requests. e.g. wget http://www.google.com

•	Check https requets e.g. wget https://www.google.com

•	Wierd error in my case is that Linux AMI instance blocked http requests and not https

•	If wget do not work, then try deleting AMI instance and create RHEL type of EC2 instance and deploy your code on the new instance

•	Try wget or curl now, it should work.






