# Jenkins CI/CD Pipeline

This repository contains the Jenkins pipeline code to automate building, testing, code analysis, Docker image creation, and deployment of the VProfile application to AWS ECS.

---

## Repository

[https://github.com/hkhcoder/vprofile-project](https://github.com/hkhcoder/vprofile-project)


## Jenkins Pipeline Overview

The Jenkins pipeline automates the following stages:

1. **Fetch Code**  
   Clone the `docker` branch from the GitHub repository.

2. **Build**  
   Build the project using Maven, skipping tests during this step. Archives the generated `.war` file on success.

3. **Unit Test**  
   Run the unit tests using Maven.

4. **Checkstyle Analysis**  
   Run static code analysis for code style violations using Maven Checkstyle plugin.

5. **SonarQube Code Analysis**  
   Perform code quality analysis using SonarQube scanner.

6. **Quality Gate**  
   Wait for SonarQube Quality Gate result; aborts the pipeline on failure.

7. **Build Docker Image**  
   Build the application Docker image using a multi-stage Dockerfile.

8. **Push Docker Image**  
   Push the built Docker image to the AWS ECR repository with tags for the build number and `latest`.

9. **Clean Up**  
   Remove all Docker images from the build agent to free space.

10. **Deploy to ECS**  
    Trigger ECS service update to deploy the new image.

---

## Prerequisites

- Jenkins server with:  
  - Maven 3.9  
  - JDK 17  
  - SonarQube Scanner (version 6.2)  
  - Docker installed and configured  
  - AWS CLI installed and configured  
- Jenkins credentials configured:  
  - `ecr:us-east-1:awscreds` (AWS ECR credentials)  
  - `awscreds` (AWS credentials for ECS deployment)  
- AWS ECS cluster and service (`vprofile2` cluster and `vprofileappsvc` service)  
- AWS ECR repository: `183295453652.dkr.ecr.us-east-1.amazonaws.com/vprofileappimg`

---

## How to Use

1. Push your code changes to the `docker` branch of this repository.
2. Trigger the Jenkins pipeline build.
3. Jenkins will execute the stages automatically and deploy the new image to ECS on success.

---

## Pipeline Environment Variables

| Variable          | Description                         |
|-------------------|-----------------------------------|
| `registryCredential` | Jenkins credential ID for AWS ECR registry access |
| `appRegistry`        | AWS ECR repository URL for the app image         |
| `vprofileRegistry`   | AWS ECR base registry URL                       |
| `cluster`            | ECS cluster name (`vprofile2`)                   |
| `service`            | ECS service name (`vprofileappsvc`)              |

---

