Prerequisite - Docker must be installed inside the Machine. If not kindly follow the link to install Docker.

<h3>Step 1: Pull the Sonar Docker Image</h3>

```sudo docker pull sonarqube```

<h3>Step 2: Create a Docker Network</h3>

```sudo docker network create sonar-network```

**Bonus tip - To check docker networks you can use ```docker network ls```

<h3>Step 3: Start a Database Container(postgres)</h3>

```sudo docker run -d --name sonar-db --network sonar-network -e POSTGRES_USER=sonar -e POSTGRES_PASSWORD=sonar -e POSTGRES_DB=sonar postgres:9.6```

<h3>Step 4: Start the Sonar Container</h3>

```sudo docker run -d --name sonar -p 9000:9000 --network sonar-network -e SONARQUBE_JDBC_URL=jdbc:postgresql://sonar-db:5432/sonar -e SONAR_JDBC_USERNAME=sonar -e SONAR_JDBC_PASSWORD=sonar sonarqube```

This command starts the Sonar container and maps port 9000 on the host to port 9000 in the container. It sets the ```SONARQUBE_JDBC_URL``` environment variable to the URL of the PostgreSQL database.

<h4>Access SonarQube Dashboard</h4>

Check the docker container whether it is running or not using - ```sudo docker ps```

You can access SonarQube from ```http://127.0.0.1:9000/```

Use username and password as <h2><i>"admin"</h2></i>

Later it will prompt to change the password where you can reset it.

