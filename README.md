---

# Simple Java Maven App

## Project Overview

This repository is for the [Build a Java app with Maven](https://jenkins.io/doc/tutorials/build-a-java-app-with-maven/) tutorial in the [Jenkins User Documentation](https://jenkins.io/doc/). It includes a simple Java application that outputs "Hello world!" and comes with unit tests that produce JUnit XML reports.

The `jenkins` directory contains an example of the `Jenkinsfile` (i.e., Pipeline) used in the tutorial, while the `jenkins/scripts` subdirectory includes a shell script with commands executed during the "Deliver" stage of the Pipeline.

## Java CI/CD Project - GitHub Actions

This project demonstrates a CI/CD pipeline for a Java application using GitHub Actions, Maven, Docker, and various security and quality checks. The pipeline automates the build, test, version increment, Docker image build and push processes, and deployment to an EC2 instance, along with secret scanning, dependency scanning, Dockerfile linting, and static code analysis.

## Prerequisites

- **GitHub Account**: Required to fork and manage the repository.
- **GitHub Repository**: Host your code and workflows on GitHub.
- **Docker Hub Account**: To push Docker images built in the CI/CD pipeline.
- **Maven**: For local testing (install via `apt`, `brew`, or from the [Maven website](https://maven.apache.org/)).
- **JDK 17**: For local testing (install via `apt`, `brew`, or from the [Oracle website](https://www.oracle.com/java/technologies/javase-jdk17-downloads.html)).
- **Snyk Account**: Required for dependency scanning (sign up at [Snyk.io](https://snyk.io/)).
- **TruffleHog**: For secret scanning, installed locally or via GitHub Actions.
- **Hadolint**: For Dockerfile linting, installed locally or via GitHub Actions.
- **CodeQL**: For static code analysis, installed locally or via GitHub Actions.
- **Cosign**: For image signing, installed locally or via GitHub Actions.
- **SSH Access**: SSH access to an EC2 instance for deployment (ensure SSH key is available and configured).
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
    - Configure the Dockerfile to build and run your Java application using Alpine Linux.
    - Include security best practices such as minimizing the number of layers and using specific versions for the base image.

### EC2 Instance Setup

1. **Create an EC2 Instance**:
    - Use the AWS CLI or Terraform to create an Ubuntu EC2 instance.

2. **Configure SSH Access**:
    - Ensure your SSH key is correctly set up and you can access the EC2 instance.

3. **Install Docker on EC2**:
    - SSH into your EC2 instance and install Docker using the same steps as for your local environment.

## Workflows Overview

### 1. CI/CD Workflow (.github/workflows/cicd.yml)

**CI/CD Workflow Overview**

This CI/CD pipeline automates the build, test, versioning, Docker image build and push processes, and deployment to an EC2 instance. It includes secret scanning, dependency scanning, Dockerfile linting, and static code analysis.

**Key steps include:**

- **Checkout Repository**: Retrieves the latest code from the repository.
- **Verify POM File**: Ensures that the `pom.xml` file exists.
- **Set up JDK 17**: Configures the JDK 17 environment.
- **Install Maven**: Installs Maven 3.9.2.
- **Build with Maven**: Compiles and packages the application.
- **Test with Maven**: Runs tests on the `development` branch.
- **Run Snyk Maven Dependency Scan**: Performs a security scan on Maven dependencies.
- **Update Maven Version**: Increments the Maven version based on the branch (`master` or `development`).
- **Upload Artifact**: Uploads the built JAR file as an artifact.
- **Login to Docker Hub**: Authenticates with Docker Hub.
- **Build and Push Docker Image**: Builds and pushes the Docker image to Docker Hub, only on the `master` branch.
- **Get Image Digest**: Retrieves the digest of the newly pushed Docker image.
- **Install Cosign**: Installs Cosign for image signing.
- **Sign Images with Cosign**: Signs the Docker image with Cosign, only on the `master` branch.
- **Commit and Push New Version**: Commits and pushes the updated version number to the repository.
- **Verify Image Signature**: Verifies the Docker image signature with Cosign, only on the `master` branch.
- **Deploy Docker Image**: Deploys the Docker image to an EC2 instance, only on the `master` branch.

### 2. Secret Scanning with TruffleHog (.github/workflows/trufflehog-secret-scan.yml)

**Secret Scanning Overview**

This workflow uses TruffleHog to scan for secrets in the codebase. It runs on pull requests and pushes to the `master` branch.

**Key steps include:**

- **Checkout Repository**: Retrieves the latest code from the repository.
- **Run TruffleHog Scan**: Scans the codebase for any sensitive information or secrets.

### 3. Dockerfile Linting with Hadolint (.github/workflows/dockerfile-lint-scan.yml)

**Dockerfile Linting Overview**

This workflow uses Hadolint to lint Dockerfiles for best practices and potential issues. It runs on pull requests and pushes to the `master` branch.

**Key steps include:**

- **Checkout Repository**: Retrieves the latest code from the repository.
- **Run Hadolint Scan**: Lints the Dockerfile for compliance with Dockerfile best practices.

### 4. Static Code Analysis with CodeQL (.github/workflows/codeql-static-analysis.yml)

**Static Code Analysis Overview**

This workflow performs static code analysis using CodeQL to identify vulnerabilities and code quality issues. It runs on pull requests and pushes to the `master` branch.

**Key steps include:**

- **Checkout Repository**: Retrieves the latest code from the repository.
- **Set up CodeQL**: Configures the CodeQL environment.
- **Analyze Code**: Runs CodeQL analysis on the codebase to identify potential issues.

---
