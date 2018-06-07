# spring-boot-docker
Dockerizing Spring Boot application

## Docker
Docker is a software platform that enables developers to develop, ship, and run applications anywhere with the help of containers.

### What problem does docker solve with containers?
The below points are familiar to us while moving the appication to QA/production environment.

1. It runs on my machine but not running on production/QA
2. production server version is outdated, migrate the production server to new version.
3. DevOps asks developers to provide all libraries and tools before being able to run your application.

Docker solves these problems by creating a lightweight, standalone, executable package of our application that includes everything needed to run it including the code, the runtime, the libraries, tools, environments, and configurations.These standalone executable packages are called docker images. And a running instance of a docker image is called a docker container.

These container images can be shared, shipped and run anywhere in any environment. They will behave exactly the same regardless of the environment they run in.

### Dockerizing a Spring Boot App
We can create a docker image for our spring boot application, and run it locally. In the application’s root directory, create a new file named Dockerfile.

Dockerfile is where we define the docker image and specify all the configurations required to run the app. Following is the Dockerfile for our spring boot application.

```
# Image containing Java runtime
FROM openjdk:8-jdk-alpine

# Maintainer Info
LABEL maintainer="sudarsan.a@icloud.com"

# Add a volume pointing to /tmp
VOLUME /tmp

# Make sure that port 8080 available to outside this container
EXPOSE 8080

# The Spring boot application's jar file
ARG JAR_FILE=target/spring-boot-docker-0.0.1-SNAPSHOT.jar

# Add the application's jar to the container
ADD ${JAR_FILE} spring-boot-docker.jar

# Run the jar file 
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/spring-boot-docker.jar"]
   ```
The Dockerfile is very simple and easy to understand.

`FROM`: A Dockerfile must start with a `FROM` instruction. The FROM instruction specifies the Base Image from which you are building.           FROM may only be preceded by one or more ARG instructions, which declare arguments that are used in FROM lines in the                   Dockerfile. The `openjdk:8-jdk-alpine` image as our base image. It is a lightweight OpenJDK 8 runtime image that uses Alpine             Linux.

`LABEL`: This is used to add metadata to the image. Here we have added information about the maintainer(author) of the image through              LABEL instruction.

`VOLUME`: A volume is a persistent data stored in `/var/lib/docker/volumes/...`

The common use cases of volumes is to store the log files generated by the container on the Host OS. For example, Let’s say that our application writes log files to a location /var/log/app.log.

In the Dockerfile, we have created a mount point with path /tmp because this is where the spring boot application creates working directories for Tomcat by default.

`EXPOSE`: Here we are exposing certain port to the outside world.

`ARG`: It defines a variable with a default value. 

`ADD`: It is used to copy new files and directories to the docker image.

`ENTRYPOINT`: Entry point to the container, here we configure how the application is executed inside the container.

### Building the Image
So we have Dockerfile, now we can a docker image for our application. Execute the below command from the root directory of the project to build the docker image.

`$ docker build -t spring-boot-docker`

### Running the Docker Image
We can run the docker image using docker run command as mentioned below.

`$ docker run -p 6060:8080 spring-boot-docker`

In the run command, we have specified that the port 8080 on the container should be mapped to the port 6060 on the Host OS.

Once the application is started, we should be able to access it at http://localhost:6060

### Running the Docker image in detached mode.

We can use the -d option in docker run command to run the container in the background.

`$ docker run -d -p 6060:8080 spring-boot-docker`

Now the container starts in the background and gives the container ID.

@mentions (https://docs.docker.com/)
          (https://spring.io/guides/gs/spring-boot-docker/)
