# How to setup SonarQube with PostgreSQL DB on Docker Container?
* Let's start creating a docker network for both SonarQube and PostgreSQL database, as they both need to be on the same network to communicate with each other.
* If you are restarting the server make sure the docker service is enabled so that it will automatically start SonarQube and PostgreSQL docker containers.

You can enable docker by passing command:
  ```
  sudo systemctl enable docker
  ```

## Start with cleaning process:
If there is any docker container with either SonarQube or Postgres, let's delete it and create a new one.
  ```
  #SonarQube
  docker stop sonarqube
  docker rm sonarqube
    
  #PostgreSQL
  docker stop postgres
  docker rm postgres
  ```

If there is any docker network with the same name, let's delete it and create a new one.
  ```
  docker network rm sonarqube_network
  ```
## Container creation process:
Creating new docker network...
  ```
  docker network create sonarqube_network
  ```

Creating new PostgreSQL docker container...
  ```
  docker run --name postgres --restart=always -e POSTGRES_USER=root -e POSTGRES_PASSWORD=Mypass12345 -p 5432:5432 --network sonarqube_network -d postgres
  ```

Creating new SonarQube docker container...
  ```
  docker run -d --name sonarqube --restart=always -p 9000:9000 -e sonar.jdbc.url=jdbc:postgresql://postgres/postgres -e sonar.jdbc.username=root -e sonar.jdbc.password=Mypass12345 --network sonarqube_network sonarqube
  ```

If you already have container running and you want to automatically start the container after every server restart, then use the following command;
  ```
  docker update --restart=always [container id or container name]
  ```

## Verification:
* SonarQube should be up and running at http://localhost:9000 or http://your-ip4-address:9000. 
* The default credentials to login into the SonarQube are;
  ```
  username: admin
  password: admin
  ```
  
Now you should be able to use SonarQube in Jenkins pipeline for Continuous Inspection, by configuring SonarQube into Jenkins.
