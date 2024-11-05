# Notes-3
we have previously seen the commands used in docker!!

open Docker VM ec2 instance!!
 
we use DockerFile name only 

so first command
- vi DockerFile

### DockerFile

```text
FROM ubuntu

MAINTAINER mohit<mohitkr@samsung.com>

RUN echo 'hello from run instruction-1'
RUN echo 'hello from run instruction-2'

CMD echo 'hi from cmd-1'
CMD echo 'hi from cmd-2'

```

no logic here just basic keywords! echo is used to print message!!

RUN all will execute from top to bottom!!

CMD last instruction will execute!!

Now we want to create Docker Image from DockerFile

so use

- docker build -t img-1 .

img-1 is the image name you can give anything , -t means tag 

while creating image you see RUN instruction being executed .

![alt text](image.png)

now we create docker container by docker run!!

docker run \<image-name\> or \<image id\>

![alt text](image-1.png)

on docker images you see all images we can see img-1 

we run by image id 

then you see last CMD is executed

---

### How to push docker image into docker hub account

Create account in DockerHub, you must have docker account to store docker images!!

first login into docker hub account

    $ docker login

On login it will ask username and password after successful login it will say successful login!!

then tag docker image

    $ docker tag <image-name> <tag-name>

Ex1 : docker tag img-1 ashokit/img-1:latest

Ex2 : docker tag img-1 ashokit/img-1:v1

v1, latest tell versions 

>Note: tag name must have your user name

now when you do docker images you see your image with tag

then push docker image to docker hub

    $ docker push <tag-name>

---

### Dockerizing java web app


=> Java web app will be packaged as war file

Note: war file will be created inside target directory

=> To deploy war file we need web server (Ex: tomcat)

=> Inside tomcat server webapps directory we need to place our war file to run the application.

DockerFile
```text
FROM tomcat:latest

MAINTAINER Ashok

EXPOSE 8080

COPY target/app.war /usr/local/tomcat/webapps/
```


Java Web App Git Repo : https://github.com/ashokitschool/maven-web-app.git

$ sudo yum install git 

$ sudo yum install maven 

$ git clone https://github.com/ashokitschool/maven-web-app.git

$ cd maven-web-app

$ mvn clean package

$ ls -l target

$ docker build -t <img-name> .

$ docker images

$ docker run -d -p <host-port:container-port> <img-name>

=> Enable host port number in security group inbound rules and access our application

		URL : http://public-ip:host-port/war-file-name/

---

### Dockerizing Java Spring Boot Application


=> Every SpringBoot application will be packaged as jar file only

=> To run spring boot application we need to execute jar file.

	Syntax : java -jar <jar-file-name>

>Note: When we run springboot application jar file then springboot will start tomcat server with 8080 port number (embedded tomcat server).

Dockerfile for Spring Boot Application 
```text
FROM openjdk:17

MAINTAINER Ashok

COPY target/app.jar /usr/app/

WORKDIR /usr/app/

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
```

Java Spring Boot App Git Repo : https://github.com/ashokitschool/spring-boot-docker-app.git

$ git clone https://github.com/ashokitschool/spring-boot-docker-app.git

$ cd spring-boot-docker-app

$ mvn clean package

$ ls -l target

$ docker build -t sb-app .

$ docker run -d -p 8080:8080 sb-app

Note: Once container created check logs of container

$ docker logs <container-id>

Note: Access our application using host-vm public and host port

		URL : http://localhost:host-port/

---

### Dockerize Python Flask Application

=> Python is a scripting language

=> We don't need any build tool for python app

=> Directly we can run python programs

	Ex : python app.py

=> Flask is a python library which is to develop rest apis in python.	

=> To download flask library we will use 'python pip software'

Note: We will configure dependencies in "requirements.txt"

Dockerfile for Python Flask App 
```text
FROM python:3.6

MAINTAINER Ashok

COPY . /usr/app/

WORKDIR /usr/app/

RUN pip install -r requirements.txt

EXPOSE 5000

ENTRYPOINT ["python", "app.py"]
```
Python App Git Repo : https://github.com/ashokitschool/python-flask-docker-app.git

$ git clone https://github.com/ashokitschool/python-flask-docker-app.git

$ cd python-flask-docker-app

$ docker build -t <img-name> .

$ docker run -d -p 5000:5000 <img-name>

$ docker ps

Note: Enable 5000 port in security group inbound rules.

=> Access application with URL

			URL : http://public-ip:host-port/

---

### Can we get into docker container machine ?	


Yes, using below commands

-  display running docker containers info
    
        $ docker ps

-  get into container using container id

        $ docker exec -it <container-id> /bin/bash

- check files in pwd
        
        $ ls -l

-  come out from container vm to host vm
        
        $ exit


---

### Tasks

Task-1: Run jenkins server using docker

Task-2: setup mysql db using docker

Task-3: Write docker file to execute reactjs app
