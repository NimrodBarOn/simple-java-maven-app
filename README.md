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
- **TruffleHog**: Installed locally or via GitHub Actions for secret scanning. [Official Documentation](https://github.com/trufflesecurity/trufflehog).
- **Hadolint**: Installed locally or via GitHub Actions for Dockerfile linting. [Official Documentation](https://github.com/hadolint/hadolint).
- **CodeQL**: Installed locally or via GitHub Actions for static code analysis. [Official Documentation](https://codeql.github.com/docs/).
- **Cosign**: Installed locally or via GitHub Actions for image signing. [Official Documentation](https://github.com/sigstore/cosign).
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

### Cosign Setup

1. **Generate Cosign Key Pair**:
    - Run the following command locally to generate a Cosign key pair:
    ```bash
    cosign generate-key-pair
    ```
    - This will create two files: `cosign.key` (private key) and `cosign.pub` (public key).

2. **Store Cosign Keys in GitHub Secrets**:
    - Add the contents of `cosign.key` to the `COSIGN_PRIVATE_KEY` secret.
    - Add the contents of `cosign.pub` to the `COSIGN_PUBLIC_KEY` secret.

## Workflows Overview

### 1. CI/CD Workflow (.github/workflows/cicd.yml)

This workflow automates the CI/CD pipeline for building, testing, and deploying the Java application.

**Key steps include:**
- **Checkout repository**: Fetches the latest code from the repository.
- **Verify POM file**: Ensures the `pom.xml` file exists before proceeding.
- **Set up JDK 17**: Configures the environment with JDK 17.
- **Install Maven**: Installs the specified Maven version.
- **Build with Maven**: Compiles the Java application using Maven.
- **Test with Maven**: Runs tests on the `development` branch.
- **Run Snyk Maven Dependency Scan**: Scans for vulnerabilities using Snyk.
- **Update Maven version**: Increments the version number based on the branch.
- **Upload artifact**: Saves the built JAR file as an artifact.
- **Login to Docker Hub**: Authenticates Docker Hub credentials.
- **Build and Push Docker Image**: Builds and pushes the Docker image to Docker Hub.
- **Get Image Digest**: Retrieves the image digest for signing.
- **Install Cosign**: Sets up Cosign for image signing.
- **Sign images with a key**: Signs the Docker image with Cosign.
- **Commit and push new version**: Commits the updated `pom.xml` to the repository.
- **Verify image signature**: Verifies the signed Docker image.
- **Deploy Docker Image**: Deploys the Docker image to an EC2 instance.

### 2. TruffleHog Secret Scanning (.github/workflows/trufflehog-secret-scan.yml)

This workflow scans the repository for any exposed secrets using TruffleHog.

**Key steps include:**
- **Install TruffleHog**: Sets up TruffleHog in the CI environment.
- **Scan repository for secrets**: Executes a TruffleHog scan on the repository.
- **Report found secrets**: Reports any detected secrets in the scan results.

### 3. Dockerfile Linting (.github/workflows/dockerfile-lint-scan.yml)

This workflow ensures the Dockerfile adheres to best practices using Hadolint.

**Key steps include:**
- **Install Hadolint**: Sets up Hadolint in the CI environment.
- **Lint Dockerfile**: Scans the Dockerfile for issues and reports any findings.

### 4. CodeQL Static Code Analysis (.github/workflows/codeql-static-analysis.yml)

This workflow performs static code analysis on the Java application using CodeQL.

**Key steps include:**
- **Initialize CodeQL**: Sets up CodeQL in the CI environment.
- **Analyze codebase**: Runs static analysis on the Java code.
- **Upload results**: Uploads the analysis results to GitHub for review.

### 5. Cosign Image Signing (.github/workflows/cosign-signing.yml)

This workflow signs the Docker images using Cosign and verifies them before deployment.

**Key steps include:**
- **Generate Cosign Key Pair**: (Setup step) Generates the keys required for signing.
- **Sign Docker image**: Signs the Docker image with the generated Cosign key.
- **Verify image signature**: Confirms the authenticity of the Docker image before deployment.

### 6. Snyk Dependency Scanning (.github/workflows/snyk-dependency-scan.yml)

This workflow scans the Java application for vulnerabilities in its dependencies using Snyk.

**Key steps include:**
- **Install Snyk CLI**: Sets up Snyk CLI in the CI environment.
- **Run dependency scan**: Scans the Maven dependencies for known vulnerabilities.
- **Report vulnerabilities**: Reports any detected vulnerabilities.

---
