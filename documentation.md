# Spring Petclinic CI/CD Pipeline with Docker and JFrog Artifactory

This project implements a continuous integration/continuous deployment (CI/CD) pipeline for the Spring Petclinic application using Jenkins. The pipeline compiles the code, runs tests, packages the project as a Docker image, and pushes the image to JFrog Artifactory. 

## Deliverables

1. Compile the code using Maven
2. Run tests on the application
3. Package the project as a Docker image
4. Push the Docker image to JFrog Artifactory

## Prerequisites

Before running the project, ensure you have the following set up:

1. **Jenkins** - Installed and configured on your machine or a remote server.
2. **Docker** - Installed for containerizing the application.
3. **JFrog Artifactory** - Set up for storing and managing Docker images.
4. **Git** - Installed to clone the repository.
5. **Maven** - Installed for building the Java project.

### Jenkins Plugins Required:
- Docker Pipeline
- Artifactory Plugin or JFrog Pipeline Integrations
- Blue Ocean Plugin (for visualizing pipelines)

## Project Structure

- `Dockerfile`: Defines the multi-stage Docker build, which compiles and packages the Spring Boot application.
- `Jenkinsfile`: Defines the Jenkins pipeline to build, test, and deploy the application.
- `pom.xml`: Maven configuration file for the Spring Petclinic project. The JCenter repository was added to the pom.xml so that dependencies are resolved from it amongst others.

## Pipeline Overview

The Jenkins pipeline follows these steps:

1. **Clean and Package**: Compiles the Spring Boot application and packages it using Maven.
2. **Test**: Runs a test on the Spring Boot application using Maven.
3. **Build Docker Image**: Builds a Docker image of the application.
4. **Run Application**: (Optional) Runs the application to verify functionality. This ONLY RUNS when run_deploy_test is set to "true"
5. **Artifactory Login**: Logs into JFrog Artifactory to prepare for image upload.
6. **Tag Image**: Tags the Docker image with the correct version and repository name.
7. **Push Image**: Pushes the tagged Docker image to JFrog Artifactory.

## Dockerfile Explanation

The Dockerfile uses a multi-stage build approach:

1. **Stage 1 - Build**: Installs Maven, copies project files, and builds the Spring Boot application.
2. **Stage 2 - Final Image**: Creates a minimal Docker image with just the JAR file and the OpenJDK runtime to run the application and expose it to the network port 8080 for the container to listen on.

## Commands to run the Docker image in your IDE(in my case I used a VScode)

1. Type the command in this double quote "docker build -t danielo-petclinic-assignment ." in your terminal after you have created a dockerfile in your IDE and it will build your docker image.
2. Type this command "docker run -d -p 8080:8080 -name petclinicontainer danielo-petclinic-assignment" to create your container.

 
## Running the Project in Jenkins Server(Remote or Local)
1. Set Up Jenkins: Make sure you have Jenkins setup on your personal computer or remote server and your jenkins job configured for the project. If it's a remote server, all you have to do is login and use the job to build the project
2. If it's personal and not a remote jenkins server, in the job configuration, use a Freestyle Job and point it to your GitHub Repository
3. Once code is being changed, committed and pushed on GitHub, you can build the project on Jenkins
5. After the set up, trigger the pipeline by hitting "Build"
6. Open Jenkins Blue Ocean to see a visual representation of the build.

## Running the Project Locally
If you want to run the Docker container locally:
1. Clone the Repository
   ```
   git clone https://github.com/yourusername/your-repo.git
   cd your-repo
2. Compile the project:
   ```
   ./mvnw clean package
3. Build the Docker Image:
   ```
   ./mvnw spring-boot:build-image
4. Run the Application(OPTIONAL):
   ```
   ./mvnw spring-boot:run 
5. Run the application container:
   ```
   docker run -p 8080:8080 spring-petclinic:3.3.0-SNAPSHOT
6. View the running web application from your browser:
   ```
   http://localhost:8080
   
7. Stopping the container: If you need to stop the running container, you can either:
   
   a. Press `CTRL + C` in the terminal where the container is running
   
   b. Or find the container ID using `docker ps`  and stop it manually:
   ```
   docker ps  # List running containers
   docker stop <container_id>  # Stop the container

## Running the Built Image From the Artifactory
If you want to use the Docker Image that was built from the Jenkins Pipeline and Pushed to JFrog Artifactory locally, follow these steps to pull and run the image:
1. Prequisites: Ensure you have the following installed on your machine:
   
   a. Docker
   
   b. Access to the JFrog Artifactory: You will need credentials to pull the image from the repository
3. Login to JFrog Artifactory:
   ```
   docker login oluwatobi1.jfrog.io
4. You'll be prompted to enter your `username and password(or access token)`
5. If it's an enterprise JFrog Artifactory, you'll need to have access if not the request will be denied
6. Pull the Docker Image from Artifactory: Once authenticated, you can pull the Docker Image from Artifactory using the `docker pull` command. Replace `<image_tag>` with the specific version tag if necessary. Also replace `daniel` with the appropriate `repo-name` specified in the Artifactory.
   ```
   docker pull oluwatobi1.jfrog.io/daniel/spring-petclinic:3.3.0-SNAPSHOT
This command pulls the image named `spring-petclinic` from the `daniel` repository on JFrog Artifactory with the tag `3.3.0-SNAPSHOT`

7. Running the Docker Image: After pulling the image, you can run it locally using Docker. To start the container, use the following command
   ```
   docker run -p 8080:8080 oluwatobi1.jfrog.io/daniel/spring-petclinic:3.3.0-SNAPSHOT
This command will: 

   a. Run the container on port `8080`
   
   b. Map `port 8080` from the container to `port 8080` on your local machine.
   
8. View the running web application from your browser:
   ```
   http://localhost:8080
9. Stopping the container: If you need to stop the running container, you can either:
    
   a. Press `CTRL + C` in the terminal where the container is running
   
   b. Or find the container ID using `docker ps`  and stop it manually:
   ```
   docker ps  # List running containers
   docker stop <container_id>  # Stop the container

## Jenkins Expression:
      when {
         expression { return run_deploy_test == "true" }
      }
   
This is a condition that runs the application with the command `./mvnw spring-boot:run` in the pipeline.
This stage only runs when run_deploy_test is set to "true" `run_deploy_test = true` 
This was set to optional, because when set to true, the pipeline stops at the `Run Application` stage, and the application goes live in `localhost:8080` and the process continues to stay up, therefore, other stages don't get to run, until the build is cancelled. In other not to set that to be default, you can toggle it to be "true" or "false" in the Jenkinsfile.

BLUE OCEAN PREVIEW:

`run_deploy_test = true`
![Alt text](https://i.postimg.cc/J0Y3SJ9g/with-run.png)
`run_deploy_test = false`
![Alt text](https://i.postimg.cc/Z0ztPt0w/without-run.png)

