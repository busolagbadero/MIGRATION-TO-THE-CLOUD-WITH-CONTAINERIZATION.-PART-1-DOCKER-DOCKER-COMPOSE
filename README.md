# MIGRATION-TO-THE-CLOUD-WITH-CONTAINERIZATION.-PART-1-DOCKER-DOCKER-COMPOSE

This project will focus on practicing containerization technology using Docker, which revolutionized application distribution and deployment in 2013. While there are other options for containerization, Docker has become the standard and default choice for shipping applications in a container.

To get started with this project, we'll need to have Docker Engine installed. This is a client-server application that includes a server with a long-running daemon process called dockerd, APIs that define interfaces for interacting with the Docker daemon, and a command-line interface (CLI) client called docker.

Fortunately, we can use the codespace feature in GitHub to work on this project without needing to install Docker on our local machines. This will allow us to start working with Docker and prepare for migrating our application to the cloud.

## Step 1: Pull MySQL Docker Image from Docker Hub Registry

To get started, we'll need to download the Docker image for MySQL. This can be done using the docker pull command, where you can choose a specific version or use the latest release.For example, you can pull the latest MySQL Docker image using the following command: 'docker pull mysql/mysql-server:latest' This command will download the latest version of the MySQL Docker image from the Docker Hub repository.

![ml1](https://user-images.githubusercontent.com/94229949/236973652-75263d6f-cbcb-428d-8eba-46bf4a95d73a.png)


![ml2](https://user-images.githubusercontent.com/94229949/236973683-67aee8e4-b605-47ea-992a-3055a537f5f4.png)

## Step 2: Deploy the MySQL Container to your Docker Engine

Once you have the image, move on to deploying a new MySQL container with: `docker run --name <container_name> -e MYSQL_ROOT_PASSWORD=<my-secret-pw> -d mysql/mysql-server:latest`

* Replace <container_name> with the name of your choice. If you do not provide a name, Docker will generate a random one
* The -d option instructs Docker to run the container as a service in the background
* Replace <my-secret-pw> with your chosen password
* In the command above, we used the latest version tag. This tag may differ according to the image you downloaded
 
Then, check to see if the MySQL container is running: Assuming the container name specified is mysql-server `docker ps -a`.
 
  ![t1](https://user-images.githubusercontent.com/94229949/236976763-fea8fb90-9cb7-48f6-a97c-bea965d5a564.png)

  ## Step 3: Connecting to the MySQL Docker Container
 
  To use a second container as a MySQL client, we need to create a custom network to allow both the MySQL server container and the application container to connect. Although creating a custom network is not necessary since Docker will use the default network by default.

To create a custom network, we can use the docker network create command, as shown below:
 
  `docker network create --subnet=172.18.0.0/24 tooling_app_network `
  
This command will create a new network called `tooling_app_network` with a subnet of 172.18.0.0/24.

To run the MySQL server container on this network, we can use the `docker run` command with the appropriate flags and options. First, we need to set an environment variable to store the root password for MySQL. We can do this by running the following command: export MYSQL_PW=

Then, we can pull the MySQL Docker image and run the container with the following command: docker run --network tooling_app_network -h mysqlserverhost --name=mysql-server -e MYSQL_ROOT_PASSWORD=$MYSQL_PW -d mysql/mysql-server:latest

  
Here, we're running the container in detached mode (`-d `flag) and connecting it to the tooling_app_network network (`--network` flag). We're also setting the hostname of the container to mysqlserverhost (`-h` flag) and naming the container mysql-server (`--name` flag). Finally, we're passing the root password environment variable to the container (`-e` flag).

Note that the docker run command may take a few moments to complete as it pulls the MySQL Docker image and starts the container.
  
![ml6](https://user-images.githubusercontent.com/94229949/236978302-48110bce-1a4e-4130-a025-1cf03a2d3475.png)

When working with a MySQL server in a Docker container, it is important to create a new user to connect to the server remotely, instead of using the root user. To do this, we can create an SQL script that creates a new user and grants them the necessary privileges.

In your GitHub documentation, you can create a new file named create_user.sql in your project directory and copy the following code into it:
  
  
![mln](https://user-images.githubusercontent.com/94229949/236978980-0e5cb594-7323-4c6e-b9d0-3b477c5d19b5.png)

To run this script inside the MySQL server container, we can use the `docker exec command`. The following command will execute the `create_user.sql` script inside the container, using the MySQL root user and password that we defined earlier:
 `docker exec -i mysql-server mysql -uroot -p$MYSQL_PW < ./create_user.sql`

Note that you may see a warning message when running this command that using a password on the command line interface can be insecure. However, since this is a local development environment, it is acceptable to ignore this warning.
    
                                                                                                                            
## Step 4: Connecting to the MySQL server from a second container running the MySQL client utility                                               

                                                                           
The good thing about this approach is that you do not have to install any client tool on your laptop, and you do not need to connect directly to the container running the MySQL server
                            
Run the MySQL Client Container: `docker run --network tooling_app_network --name mysql-client -it --rm mysql mysql -h mysqlserverhost -u  -p`.                                                                          
 
Flags used:
* --name gives the container a name
* -it runs in interactive mode and Allocate a pseudo-TTY
* --rm automatically removes the container when it exits
* --network connects a container to a network
* -h a MySQL flag specifying the MySQL server Container hostname
* -u user created from the SQL script
* -p password specified for the user created from the SQL script  
                                                                           
                                                                           
![ml10](https://user-images.githubusercontent.com/94229949/236980056-5d53b88c-6b1d-42eb-86fc-50f574083218.png)

## Step 5: Prepare database schema 
 
Now we need to prepare a database schema so that the Tooling application can connect to it.
 
i. Clone the Tooling-app repository `git clone https://github.com/darey-devops/tooling.git`.
                                                                                          
ii. On your terminal, export the location of the SQL file  `export tooling_db_schema=/home/ubuntu/tooling/html/tooling_db_schema.sql`.  
                                                                                          
iii. Use the SQL script to create the database and prepare the schema. With the docker exec command, you can execute a command in a running container. `docker exec -i mysql-server mysql -uroot -p$MYSQL_PW < $tooling_db_schema `.
 
iv. Edit the .env file in the tooling/html folder with your db credentials used in your create_user.sql.                                                                           

 ![ml11](https://user-images.githubusercontent.com/94229949/236983026-3bedb1d0-592d-4e19-86cd-0d0f4b84b4b4.png)
 
 ![ml12](https://user-images.githubusercontent.com/94229949/236983071-65c9278f-570a-4041-8ffb-7c166c387409.png)
 
 
To containerize an application, a special file named 'Dockerfile' needs to be created, which can be considered as a recipe or instruction for Docker to pack the application into a container. In this project, we will build the container using a pre-created Dockerfile.

Make sure that you are inside the directory that contains the Dockerfile, and then build the container using the command: `docker build -t tooling:0.0.1 .` . The "-t" parameter specifies the image tag as tooling:0.0.1. Also, note the "." at the end, which tells Docker to locate the Dockerfile in the current directory.

To run the container, use the command: `docker run --network tooling_app_network -p 8085:80 -it tooling:0.0.1.` The "-p" flag maps port 8085 on the host to port 80 in the container. The "-it" flags are used to start the container in interactive mode.
 
 Run the container: `docker run --network tooling_app_network -p 8085:80 -it tooling:0.0.1.`
 
 ![ml15](https://user-images.githubusercontent.com/94229949/236983682-850b722f-fab4-468c-b79f-21105d895042.png)
 
 ![ml16](https://user-images.githubusercontent.com/94229949/236983723-b6b78c42-772d-4eda-954b-9dcef0b6caea.png)
 
 ![ml17](https://user-images.githubusercontent.com/94229949/236983750-1ad332f6-e14c-4022-b411-6df588cb107d.png)



 

