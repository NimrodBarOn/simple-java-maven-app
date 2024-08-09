---

## simple-java-maven-app

This repository is for the [Build a Java app with Maven](https://jenkins.io/doc/tutorials/build-a-java-app-with-maven/) tutorial in the [Jenkins User Documentation](https://jenkins.io/doc/).

The repository contains a simple Java application that outputs the string "Hello world!" and is accompanied by unit tests to ensure the main application works as expected. The results of these tests are saved to a JUnit XML report.

The `jenkins` directory contains an example of the `Jenkinsfile` (i.e., Pipeline) you'll be creating yourself during the tutorial. The `jenkins/scripts` subdirectory contains a shell script with commands that are executed when Jenkins processes the "Deliver" stage of your Pipeline.

# Java CI/CD Project - GitHub Actions

This project demonstrates a CI/CD pipeline for a Java application using GitHub Actions, Maven, Docker, and various security and quality checks. The pipeline automates the build, test, version increment, Docker image build and push processes, and deployment to an EC2 instance, along with secret scanning, dependency scanning, Dockerfile linting, and static code analysis.

## Prerequisites

- **GitHub Account**: Required to fork and manage the repository.
- **GitHub Repository**: A repository to host your code and workflows.
- **Docker Hub Account**: To push Docker images built in the CI/CD pipeline.
- **Maven**: Locally installed Maven for testing purposes (can be installed via `apt`, `brew`, or directly from the [Maven website](https://maven.apache.org/)).
- **JDK 17**: Locally installed JDK 17 for testing purposes (can be installed via `apt`, `brew`, or directly from the [Oracle website](https://www.oracle.com/java/technologies/javase-jdk17-downloads.html)).
- **Snyk Account**: Required for dependency scanning. Sign up at [Snyk.io](https://snyk.io/).
- **TruffleHog**: Installed locally or via GitHub Actions for secret scanning.
- **Hadolint**: Installed locally or via GitHub Actions for Dockerfile linting.
- **CodeQL**: Installed locally or via GitHub Actions for static code analysis.
- **Cosign**: Installed locally or via GitHub Actions for image signing.
- **SSH Access**: SSH access to an EC2 instance for deployment (ensure your SSH key is available and configured).
- **AWS CLI**: To manage AWS resources (EC2 instances, security groups, etc.).

## Setup

### GitHub Repository

1. **Fork the Repository**: Fork the [simple-java-maven-app](https://github.com/jenkins-docs/simple-java-maven-app) repository to your GitHub account.

2. **Clone the Repository**:
    ```bash
    git clone https://github.com/<your-username>/simple-java-maven-app.git
    cd simple-java-maven-app
    ```

### GitHub Secrets

1. **Set up GitHub Secrets**:
    - Go to your GitHub repository's settings.
    - Navigate to `Secrets and variables` > `Actions`.
    - Add the following secrets:
      - `GITHUB_TOKEN`: Your GitHub personal access token.
      - `DOCKER_USERNAME`: Your Docker Hub username.
      - `DOCKER_PASSWORD`: Your Docker Hub password.
      - `EC2_HOST`: Your EC2 instance public IP or DNS.
      - `EC2_SSH_KEY`: Your private SSH key content.
      - `SNYK_TOKEN`: Your Snyk API token for dependency scanning.
      - `COSIGN_PRIVATE_KEY`: Your private key for signing Docker images.
      - `COSIGN_PASSWORD`: Your password for the Cosign private key.
      - `COSIGN_PUBLIC_KEY`: Your public key for verifying Docker image signatures.

### Dockerfile

1. **Create a Multi-Stage Dockerfile**:
    - Ensure the Dockerfile is configured to build and run your Java application using Alpine Linux.
    - Include security best practices such as minimizing the number of layers and using specific versions for the base image.

### EC2 Instance Setup

1. **Create an EC2 Instance**:
    - Use the AWS CLI or Terraform to create an Ubuntu EC2 instance.

2. **Configure SSH Access**:
    - Ensure your SSH key is correctly set up and you can access the EC2 instance.

3. **Install Docker on EC2**:
    - SSH into your EC2 instance and install Docker using the same steps as for your local environment.

## Workflows Overview

### 1. CI/CD Workflow (`.github/workflows/cicd.yml`)

**Overview**:  
This workflow automates the entire CI/CD process, from building and testing the Java application to deploying it as a Docker container on an EC2 instance. It ensures that code changes are properly tested and versioned, and that the Docker image is only built and deployed when pushing to the `master` branch.

**Key Steps**:
1. Set up JDK 17 and Maven 3.9.2.
2. Build and test the application using Maven.
3. Run Snyk for dependency scanning.
4. Increment version numbers based on the branch.
5. Build and push the Docker image to Docker Hub.
6. Sign the Docker image using Cosign.
7. Verify the Docker image signature.
8. Deploy the Docker image to an EC2 instance via SSH.

### 2. Secret Scanning Workflow (`.github/workflows/trufflehog-secret-scan.yml`)

**Overview**:  
This workflow scans the repository for secrets using TruffleHog. It runs on every push to the `master` branch, ensuring that no sensitive information, such as API keys or passwords, is accidentally committed to the repository.

**Key Steps**:
1. Checkout the repository code.
2. Run TruffleHog to scan the entire repository for secrets.

### 3. Dockerfile Linting Workflow (`.github/workflows/dockerfile-lint-scan.yml`)

**Overview**:  
This workflow uses Hadolint to lint the Dockerfile, ensuring that it follows best practices and is free from common issues. It runs on every push and pull request, providing immediate feedback on Dockerfile quality.

**Key Steps**:
1. Checkout the repository code.
2. Install Hadolint.
3. Lint the Dockerfile for best practices and issues.

### 4. CodeQL Analysis Workflow (`.github/workflows/codeql-static-analysis.yml`)

**Overview**:  
This workflow performs static code analysis using CodeQL, identifying potential security vulnerabilities in the code. It runs on pushes and pull requests to the `master` and `development` branches, ensuring that the codebase remains secure.

**Key Steps**:
1. Checkout the repository code.
2. Set up CodeQL for the repository.
3. Run CodeQL analysis to detect security issues and other vulnerabilities in the code.

## CI/CD Workflow Overview

The CI/CD workflow consists of the following steps:

1. **Branching Strategy**:
    - The pipeline is triggered on pushes to the `master` and `development` branches.
    - On the `development` branch, unit tests are executed, and the patch version is incremented.
    - On the `master` branch, the minor version is incremented, the Docker image is built and pushed to Docker Hub, and the application is deployed to an EC2 instance.

2. **Checkout Repository**:
    - Uses `actions/checkout@v4` to check out the repository code.

3. **Set up JDK 17**:
    - Uses `actions/setup-java@v4` to set up JDK 17.

4. **Install Maven 3.9.2**:
    - Installs Maven 3.9.2 for building the Java application.

5. **Build and Test**:
    - Executes the `mvn clean install` command to build the project and run tests.
    - On the `development` branch, runs `mvn test`.

6. **Snyk Dependency Scanning**:
    - Runs Snyk to scan dependencies for known vulnerabilities.

7. **Increment Version**:
    - Increments the version number based on the branch being pushed to.

8. **Build Docker Image**:
    - Uses `docker build` to create a Docker image from the application.

9. **Push Docker Image**:
    - Pushes the Docker image to Docker Hub using the `docker push` command.

10. **Sign Docker Image**:
    - Signs the Docker image using Cosign.

11. **Verify Docker Image Signature**:
    - Verifies the Docker image signature using Cosign.

12. **Deploy to EC2**:
    - SSH into the EC2 instance and pull the Docker image from Docker Hub.
    - Deploy the Docker container on the EC2 instance.

---
