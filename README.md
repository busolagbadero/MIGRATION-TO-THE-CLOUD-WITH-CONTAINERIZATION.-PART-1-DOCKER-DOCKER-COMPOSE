# MIGRATION-TO-THE-CLOUD-WITH-CONTAINERIZATION.-PART-1-DOCKER-DOCKER-COMPOSE

This project will focus on practicing containerization technology using Docker, which revolutionized application distribution and deployment in 2013. While there are other options for containerization, Docker has become the standard and default choice for shipping applications in a container.

To get started with this project, we'll need to have Docker Engine installed. This is a client-server application that includes a server with a long-running daemon process called dockerd, APIs that define interfaces for interacting with the Docker daemon, and a command-line interface (CLI) client called docker.

Fortunately, we can use the codespace feature in GitHub to work on this project without needing to install Docker on our local machines. This will allow us to start working with Docker and prepare for migrating our application to the cloud.

## Step 1: Pull MySQL Docker Image from Docker Hub Registry

To get started, we'll need to download the Docker image for MySQL. This can be done using the docker pull command, where you can choose a specific version or use the latest release.For example, you can pull the latest MySQL Docker image using the following command: 'docker pull mysql/mysql-server:latest' This command will download the latest version of the MySQL Docker image from the Docker Hub repository.

![ml1](https://user-images.githubusercontent.com/94229949/236973652-75263d6f-cbcb-428d-8eba-46bf4a95d73a.png)

![ml2](https://user-images.githubusercontent.com/94229949/236973683-67aee8e4-b605-47ea-992a-3055a537f5f4.png)



