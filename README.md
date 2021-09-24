# sql-phonebook

This project was followed a phoenixNAP tutorial: [MySQL Docker Container Tutorial: How to Set Up & Configure](https://phoenixnap.com/kb/mysql-docker-container), [Connect to MySQL Server](mysqltutorial) from mysqltutorial.org, and [Accessing data with MySQL](https://spring.io/guides/gs/accessing-data-mysql/) from Spring.\
For this particular project, the database was stored on a Raspberry Pi accessed through a secure shell. 

## Steps
### 1. Setup Raspberry Pi
Set up the Raspberry Pi in order to use it to host the mySQL server. To install Ubuntu Server on the pi, follow [this tutorial by Ubuntu](https://ubuntu.com/tutorials/how-to-install-ubuntu-on-your-raspberry-pi#1-overview).

### 2.  Install Docker

Docker is a powerful tool used to deploy "containerized" apps as a container, making it possible to run apps on a server more efficiently, mainly due to containers sharing an operating system.
Here are commonly used Docker commands:
| Command | Description |
| --- | --- |
| docker ps | List all running containers |
| docker ps -al | List all containers |
| docker images | List all images |
| docker start [container]| Start Docker container |
| docker stop [container]| Stop Docker container |
| docker rm [container]| Remove Docker container |
| docker run [image]| Creates and starts a Docker container from an image|
| docker rmi [image]| Remove Docker image |

### 3. Start mySQL Docker Container
  1. Pull down the latest MySQL Docker image: `sudo docker pull mysql/mysql-server:latest`.
  2. Deploy the container by running `sudo docker run --name=spring_tutorial_mySQL -p 3306:3306 -d mysql/mysql-server:latest`. In this case, the container is named spring_tutorial_mySQL and is bound on port 3306. 

### 4. Connect mySQL client to server
  1. Install mySQL client package: `apt-get install mysql-client`
  2. Find and copy generated root password by running `sudo docker logs mysql_docker`. (Image from phoenixNAP tutorial) 
  ![image](https://user-images.githubusercontent.com/41180186/133352211-9988bd79-dea1-4e5b-b8c1-0a1e10c36984.png)
  3. Enter the container bash shell by running `sudo docker exec -it spring_tutorial_mySQL bash` and use the generated root password where prompted.
  4. Change the password of the root by running `ALTER USER 'root'@'localhost' IDENTIFIED BY '[newpassword]';`, replacing newpassword with a chosen new password.

For more detailed directions for steps 1-3, follow the steps on the phoenixNAP tutorial, except use the command from 2.2 instead of the command in the directions
(which doesn't bind the container to a port). 

### 5. Create new user in MySQL client

Using root in applications opens up large security risks, so create a new user to give access to the database to.\
From the Spring tutorial, these are the commands to create a new user and grant them permissions for a database (names replaced):

```
mysql> create database db_phonebook; -- Creates the new database
mysql> create user 'springuser'@'%' identified by 'ThePassword'; -- Creates the user
mysql> grant all on db_phonebook.* to 'springuser'@'%'; -- Gives all privileges to the new user on the newly created database
```

### 6. Create `application.properties` file

The file stores information about how to connect to the database. 

```
spring.jpa.hibernate.ddl-auto=update
spring.datasource.url=jdbc:mysql://${MYSQL_HOST:[ADD RASPBERRY PI IP ADDRESS HERE}:3306]/db_example
spring.datasource.username=springuser
spring.datasource.password=ThePassword
spring.datasource.driver-class-name =com.mysql.jdbc.Driver
#spring.jpa.show-sql: true
```

##

From here, create the Spring classes as specified in the phonebook_spring repo. 
