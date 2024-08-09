---

## simple-java-maven-app

This repository is for the [Build a Java app with Maven](https://jenkins.io/doc/tutorials/build-a-java-app-with-maven/) tutorial in the [Jenkins User Documentation](https://jenkins.io/doc/).

The repository contains a simple Java application which outputs the string "Hello world!" and is accompanied by a couple of unit tests to check that the main application works as expected. The results of these tests are saved to a JUnit XML report.

The `jenkins` directory contains an example of the `Jenkinsfile` (i.e. Pipeline) you'll be creating yourself during the tutorial and the `jenkins/scripts` subdirectory contains a shell script with commands that are executed when Jenkins processes the "Deliver" stage of your Pipeline.

# Java CI/CD Project - GitHub Actions

This project demonstrates a CI/CD pipeline for a Java application using GitHub Actions, Maven, and Docker. The pipeline automates the build, test, version increment, Docker image build and push processes, and the deployment to an EC2 instance.

## Prerequisites

- GitHub account
- GitHub repository
- Docker Hub account
- Maven installed locally (for testing purposes)
- JDK 17 installed locally (for testing purposes)
- SSH access to an EC2 instance for deployment

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
      - `SSH_USER`: The user for SSH access (e.g., `ubuntu`).
      - `SSH_KEY`: Your private SSH key content (base64 encoded).

### Dockerfile

1. **Create a Multi-Stage Dockerfile**:
    - Add the necessary configuration to build and run your Java application using Alpine Linux.

### GitHub Actions Workflow

1. **Create `.github/workflows/cicd.yml`**:
    - Define the CI/CD pipeline steps, including setting up JDK, Maven installation, version increment, build, and Docker image build and push.

## CI/CD Pipeline

The CI/CD pipeline consists of the following steps:

1. **Branching Strategy**:
    - The pipeline is triggered on pushes to the `master` and `development` branches.
    - On the `development` branch, unit tests are executed and the patch version is incremented.
    - On the `master` branch, the minor version is incremented, the Docker image is built and pushed to Docker Hub, and the application is deployed to an EC2 instance.

2. **Checkout Repository**:
    - Uses `actions/checkout@v4` to check out the repository code.

3. **Set up JDK 17**:
    - Uses `actions/setup-java@v4` to set up JDK 17.

4. **Install Maven 3.9.2**:
    - Installs Maven 3.9.2 from the Apache archive.

5. **Build with Maven**:
    - Builds the project using Maven.

6. **Test with Maven**:
    - Tests the project using Maven (on the `development` branch).

7. **Update Version**:
    - Increments the version based on the branch: patch version on `development`, minor version on `master`.

8. **Upload Artifact**:
    - Uploads the built JAR file as an artifact with version tag.

9. **Login to Docker Hub**:
    - Logs in to Docker Hub.

10. **Build and Push Docker Image**:
    - Builds and pushes the Docker image to Docker Hub (on the `master` branch).

11. **Commit and Push New Version**:
    - Commits the new version and pushes it to the repository.

12. **Deploy Docker Image**:
    - Deploys the Docker image to an EC2 instance using SSH (on the `master` branch).

## Conclusion

This project showcases a complete CI/CD pipeline setup for a Java application using GitHub Actions, Maven, and Docker. The pipeline automates the build, version increment, Docker image build and push processes, and the app deployment, ensuring a streamlined and efficient development workflow.

---
