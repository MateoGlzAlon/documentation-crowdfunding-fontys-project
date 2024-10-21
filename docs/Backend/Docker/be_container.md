---
sidebar_position: 3
---

# Creating Docker Image of the Back-end

This document provides instructions on setting up a Docker container for the Crowdfunding project. The Dockerfile is based on the `gradle:8.1.1-jdk17` image, and it uses the `Crowdfund-0.0.1-SNAPSHOT.jar` file built with Gradle.

## Dockerfile Configuration

Below is the configuration for the Dockerfile with explanations added:

```dockerfile, title="Doc"
# Base Image: Use the official Gradle image with JDK 17
FROM gradle:8.1.1-jdk17

# Set the working directory to /opt/app
WORKDIR /opt/app

# Copy the JAR file built by Gradle into the container
COPY ./build/libs/Crowdfund-0.0.1-SNAPSHOT.jar ./

# Run the application with java and any provided JAVA_OPTS
ENTRYPOINT ["sh", "-c", "java ${JAVA_OPTS} -jar Crowdfund-0.0.1-SNAPSHOT.jar"]
```

## How to create the image

Once the Dockerfile is set up, you can build the Docker image for your application by running the following command in the root of your project (where the Dockerfile is located):

```bash
docker build -t crowdfund_backend .
```

### Explanation
- **docker build**: The command used to create a Docker image from the Dockerfile.
- **-t crowdfund_backend**: This option tags the image with the name `crowdfund_backend`.
- **.**: The dot indicates the build context is the current directory, where Docker will look for the Dockerfile and other files.

## Verify the Image

After building the image, verify its existence by listing all Docker images or check in Docker Desktop:

```bash
docker images
```

You should see `crowdfund_backend` in the output, indicating the image was successfully built.

## Running the Docker Container

Once the Docker image is created, you can run the container using the following command:

```bash
docker run -d -p 8080:8080 --net=docker_network_crowdfund --env spring_profiles_active=staging --name=crowdfunding_be crowdfund_backend
```


### Explanation
- **-p 8080:8080**: Maps port 8080 on your local machine to port 8080 in the Docker container.
- **crowdfund_backend**: The name of the Docker image created earlier.

The application will now be available at [http://localhost:8080](http://localhost:8080).

