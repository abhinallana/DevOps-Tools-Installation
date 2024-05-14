Install SonarQube in Local / Cloud

Step 1:

Check for Java Installation :

```sudo apt-get install openjdk-11-jdk -y```

To check the version - ```java -v```

Step 2:

Install and Configure PostgreSQL and Add the PostgreSQL repository. (You can use any DB, in my case I have used PostgreSQL)

```sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main"   /etc/apt/sources.list.d/pgdg.list'```

Step 3:

Add the PostgreSQL signing key.

``` wget -q https://www.postgresql.org/media/keys/ACCC4CF8.asc -O - | sudo apt-key add - ```

Step 4: 

Install PostgreSQL

``` sudo apt install postgresql postgresql-contrib -y ```

Step 5:

Enable the database server to start automatically on reboot.

``` sudo systemctl enable postgresql```

Step 6:

Start the database server.

``` sudo systemctl start postgresql ```

Step 7:

Change the default PostgreSQL password.

``` sudo passwd postgres ```

Step 8:

Switch to the postgres user.

``` su - postgres ```   --> Enter the pwd which you have set.

Step 9:

Create a user named "sonar".

``` createuser sonar ```

Step 10:

Log in to PostgreSQL.

``` psql ```

Step 11:

Set a password for the sonar user. Use a strong password in place of password .

``` ALTER USER sonar WITH ENCRYPTED password '<password>'; ```

Step 12:

Create a sonarqube database and set the owner to sonar.

``` CREATE DATABASE sonarqube OWNER sonar; ```

Step 13:

Grant all the privileges on the sonarqube database to the sonar user.

``` GRANT ALL PRIVILEGES ON DATABASE sonarqube to sonar; ```

Step 14:

Exit PostgreSQL

``` \q ``` and return to your normal sudo account with ``` exit ``` .

Step 15 :

Download and Install " SonarQube "
Install the zip utility, which is needed to unzip the SonarQube files.

``` sudo apt-get install zip -y ```

Step 16:

Locate and download the latest download URL from the SonarQube official download page with wget.

in my case ``` wget https://www.sonarsource.com/products/sonarqube/downloads/success-download-community-edition/```

Step 17:

unzip the downloaded file using ``` unzip <pkg.zip>```

Step 18:

Renaming the directory for easy access -> ``` mv sonarqube-10.5.1.90531 sonarqube ```

Move the unzipped files to /opt/sonarqube directory
  
``` sudo mv sonarqube /opt/  ```

Step 19:

Add SonarQube Group and User
Create a dedicated user and group for SonarQube, which can not run as the root user.
Create a sonar group.

``` sudo groupadd sonar ```

Step 20:

Create a sonar user and set " /opt/sonarqube " as the home directory.

``` sudo useradd -d /opt/sonarqube -g sonar sonar ```

Step 21:

Grant the sonar user access to the /opt/sonarqube directory.

``` sudo chown sonar:sonar /opt/sonarqube -R ```

Step 22:

Configure SonarQube
Edit the SonarQube configuration file, using the below path

``` sudo vi /opt/sonarqube/conf/sonar.properties ```

Find the following lines:

#sonar.jdbc.username=sonar
#sonar.jdbc.password=<password>

Uncomment the lines, and add the database user and password you created in Step 2.


Below those two lines, add the sonar.jdbc.url.

sonar.jdbc.url=jdbc:postgresql://localhost:5432/sonarqube

Save and exit the file.

Step 23:

Edit the Sonar Script file...

``` sudo vi /opt/sonarqube/bin/linux-x86-64/sonar.sh ```

Search for -> #RUN_AS_USER=

Uncomment the line and change it to:

RUN_AS_USER=sonar

Save and exit the file.

Step 24:

Setup Systemd service
Create a systemd service file to start SonarQube at system boot.

 ``` sudo vi /etc/systemd/system/sonar.service ```


Paste the following lines in Terminal .. 

``` 
Description=SonarQube service
After=syslog.target network.target

[Service]
Type=forking

ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop

User=sonar
Group=sonar
Restart=always
PermissionsStartOnly=true
StandardOutput=syslog
LimitNOFILE=65536
LimitNPROC=8192
TimeoutStartSec=5


[Install]
WantedBy=multi-user.target
```

Step 25:

Enable the SonarQube service to run at system startup.

``` sudo systemctl enable sonar ```

Step 26:

Start the SonarQube service.

``` sudo systemctl start sonar ```

Step 27:

Check the service status.

``` sudo systemctl status sonar ```

Step 28:

Modify Kernel System Limits
SonarQube uses Elasticsearch to store its indices in an MMap FS directory. It requires some changes to the system defaults.

Edit the sysctl configuration file.

```  sudo nano /etc/sysctl.conf ```

and Add the following lines to it.

``` 
vm.max_map_count=262144
fs.file-max=65536
ulimit -n 65536
ulimit -u 4096 
```

Step 29:

Reboot the system to apply the changes.

``` sudo reboot ```

Step 30:

Repeat Step 25 to Step 27.



-----------------------------------------------------------------------------------------------------

TROUBLESHOOTING STEPS (if required):

1. Check the logs of sonarqube following the path -  ``` cd /opt/sonarqube/logs ``` -> nohup.log
2. Go through all the logs if failing to start sonar web interface.
3. Make sure the installed java version is above jdk-11.











