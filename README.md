# Question 1-1 
# Document your database container essentials: commands and Dockerfile.

# Database Container Essentials

This answer describes the essentials of the database container, including the commands used and the Dockerfile.

I imported the image `my-postgres-image`. 
Then, I created a volume called `my-postgres-volume`. 

## Docker Run Command

The following command is used to create and run the container named `my-postgresdb`. This container is based on the `my-postgres-image` and uses the Dockerfile for configuration. The `-v` option is used to mount the `my-postgres-volume` at the `/var/lib/postgresql/data` directory in the container.

```bash
docker run --name my-postgresdb --network app-network -d -p 5432:5432 -v my-postgres-volume:/var/lib/postgresql/data my-postgres-image
```

After executing this command, we will be able to connect to the database on port 5432 thanks to the adminer app-network.
It will run in background following the dockerfile. 

# Dockerfile details : 

# Specify the image used to build the container. 
FROM postgres:14.1-alpine

# Set environment variables to have access to the database
ENV POSTGRES_DB=db \
    POSTGRES_USER=usr \
    POSTGRES_PASSWORD=pwd

# Link to the sql file to create the tables of the database during the launching of the container.
COPY ./init.sql /docker-entrypoint-initdb.d/10-CreateScheme.sql

# Link the sql file to insert datas in each columns of the tables.
COPY ./InsertData.sql /docker-entrypoint-initdb.d/20-insert-data.sql

# The files are indicated with 10 and 20 to order the queries in alphabetic order.


# Question 1-2 
# Why do we need a multistage build? And explain each step of this dockerfile.

The multistage build is very useful to make the servor run without paying attention to the version of local softwares as we build a java image with docker. We can configurate a build that install exactly the installation of the application configuration.

More globally, it makes sure that every component of the configuration work together. 

# Dockerfile : 

# Build the application using maven.
# Take amazon-corretto-17 that is in maven:3.8.6 image.
FROM maven:3.8.6-amazoncorretto-17 AS myapp-build

# Define MYAPP_HOME as an environment variable to keep the same directory.
ENV MYAPP_HOME /opt/myapp
WORKDIR $MYAPP_HOME

# Copy the pom.xml file into a docker image. 
COPY pom.xml .
# Copy the source directory in a docker image.
COPY src ./src
# Run the maven package to compile the application and precises to skip the tests so that it does not take too long. 
RUN mvn package -DskipTests

# Run the application
# Precise the image.
FROM amazoncorretto:17
ENV MYAPP_HOME /opt/myapp
WORKDIR $MYAPP_HOME

# Copy the .jar that has just been built into an image.
COPY --from=myapp-build $MYAPP_HOME/target/*.jar $MYAPP_HOME/myapp.jar

# Run the myapp.jar file when the container starts.
ENTRYPOINT java -jar myapp.jar

# Important commands
```bash
docker run -p 8000:8080 -it --network app-network --name my-spring-boot myapp
```

```bash
docker run -d --network app-network -p 8082:80 --name my-http-container my-http
```

# Question 1-3 Document docker-compose most important commands. 
```bash
docker-compose build # Configure the containers and images.
docker-compose up # Launch all the containers to make the web application accessible
docker-compose down -v # Stop and delete all containers, images and volumes involved. 
```

# Question 1-4 Document your docker-compose file.
# See in the docker-compose.yml

# Question 2-1 What are testcontainers?
The testcontainers provide the needed tools to test the created containers regarding their dependencies. 