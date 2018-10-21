# SonarQube02 Steps
------ SonarQube Installation ------
To install SonarQube we need an Linux EC2 instance which is installed MySQL otherwise Launch MySQL RDS instance to acomplish this

Launch an MySQL RDS Database Instance (It takes few minutes)

Launch an EC2 instance and Install MySQL client version

# yum install mysql
Install java
# yum install java-1.8.0-openjdk -y
Download stable SonarQube version from below website. website: https://www.sonarqube.org/downloads/
Note: for this video I have downloaded version 6.0 : https://sonarsource.bintray.com/Distribution/sonarqube/sonarqube-6.0.zip

download & unzip it to Linux EC2 instance
# cd /opt
# wget https://sonarsource.bintray.com/Distribution/sonarqube/sonarqube-6.0.zip
# unzip sonarqube-6.0.zip
# mv /opt/sonarqube-6.0 /opt/sonar
Allow RDS instance security group to access SonarQube server

Connect to RDS instance

mysql -h <RDS_Instance_endpoint>:3306 -u sonardb -p 
Create a new sonar database
CREATE DATABASE sonar CHARACTER SET utf8 COLLATE utf8_general_ci;
Create a local and a remote user
CREATE USER sonar@localhost IDENTIFIED BY 'sonar';
CREATE USER sonar@'%' IDENTIFIED BY 'sonar';
Grant database access permissions to users
GRANT ALL ON sonar.* TO sonar@localhost;
GRANT ALL ON sonar.* TO sonar@'%';
check users and databases
use mysql
show databases;
SELECT User FROM mysql.user;
FLUSH PRIVILEGES;
QUIT
------ On EC2 Instance ------
edit SonarQube properties file
# vi /opt/sonar/conf/sonar.properties
sonar.jdbc.username=sonar
sonar.jdbc.password=sonar
mysql://<MySQL_Database_Server>:3306/sonar?useUnicode=true&characterEncoding=utf8&rewriteBatchedStatements=true&useConfigs=maxPerformance&useSSL=false
sonar.web.host=0.0.0.0
sonar.web.context=/sonar
Start SonarQube service
# cd /opt/sonar/bin/linux-x86-64/
# ./sonar.sh start
Optional
Implement SonarQube server as a service
Copy sonar.sh to etc/init.d/sonar and modify it according to your platform.
# sudo cp /opt/sonar/bin/linux-x86-64/sonar.sh /etc/init.d/sonar
# sudo vi /etc/init.d/sonar
add below values to your /etc/init.d/sonar file
Insert/modify below values
SONAR_HOME=/opt/sonar
PLATFORM=linux-x86-64

WRAPPER_CMD="${SONAR_HOME}/bin/${PLATFORM}/wrapper"
WRAPPER_CONF="${SONAR_HOME}/conf/wrapper.conf"
PIDDIR="/var/run"
Run SonarQube server
# service sonar start
Troubleshooting
Check whether you enabled port 9000 in EC2 instance security group
Check whether you enabled EC2 instance IP range in RDS security group
