---------------------------------------Run DOCKER inside Jenkins Container-------------------------------------------------------

Expose the port 8080 from the container and bind it on our host machine at port 8080 as well, so we can access Jenkins on this docker container. 

We are going to open another port 50000 and bind it to the host machine, 
this port is where the Jenkins master and worker nodes would be communicating from and running in Detached mode and mounting Volume to it.


```docker run -p 8080:8080 -p 50000:50000 -d -v /var/run/docker.sock:/var/run/docker.sock -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts```

Run the command inside the Jenkins Container. in interactive shell mode:

``` docker exec -it --user root (container id) bash ```

Once inside the Jenkins container, simply run this command to install docker inside of the Jenkins container:

``` curl https://get.docker.com/ > dockerinstall && chmod 777 dockerinstall && ./dockerinstall ```


Exit out of the Jenkins container interactive shell, and run the following command to change permissions on “docker.sock” for added security.

``` sudo chmod 666 /var/run/docker.sock ```

JENKINS HAS NOW DOCKER INSTALLED INSIDE IT.

Again Log On to Jenkins container in interactive shell mode : 

``` docker exec -it <container id> bash ```

Do get the Initial pwd using this Command.

``` cat var/jenkins_home/secrets/initialAdminPassword ```



Run Jenkins on your IP : ``` http://<your_ip>:8080/ ```
