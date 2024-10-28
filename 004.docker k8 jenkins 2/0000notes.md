# Notes 2
## DockerFile

=> Dockerfile contains set of instructions to build docker image

Filename : Dockerfile

=> To write dockerfile we will use below keywords

1) FROM
2) MAINTAINER
3) RUN
4) CMD
5) COPY
6) ADD
7) WORKDIR
8) EXPOSE
9) ENTRYPOINT
10) USER

---
	
### FROM


=> It is used to specify base image for our application

Ex: 

FROM openjdk:17
FROM python:3.3
FROM node:19.5
FROM mysql:8.5
FROM tomcat:9.0

---

### MAINTAINER


=> MAINTAINER is used to specify who is author of this Dockerfile

Ex :

MAINTAINER Ashok<ashok.b@oracle.com>

---

### RUN 


=> RUN keyword is used to specify instructions to execute at the time of docker image creation.

Ex:

RUN 'git clone <url>'
RUN 'mvn clean package'

Note: We can write multiple RUN instructions in single docker file and all those instructions will be processed in the order.

---

### CMD


=> CMD keyword is used to specify instructions to execute at the time of docker container creation.

Ex:

CMD 'java -jar app.jar'

CMD 'app.py'

Note: We can write multiple CMD instructions in single docker file but docker will process only last CMD instruction.

---

### ENTRYPOINT


=> Entrypoint is used to execute instructions when docker container is creating.

Ex: 

ENTRYPOINT["java", "-jar", "app.jar"]

ENTRYPOINT["python", "app.py"]

Note: CMD instruction we can override using command line arguments where ENTRYPOINT instruction we can't override.

---

### COPY


=> It is used to copy files from host machine to container machine

Ex:

COPY target/app.jar  /usr/app/app.jar

COPY target/app.war  /usr/app/tomcat/webapps/app.war

COPY app.py /usr/app/app.py

---

### ADD Keyword


=> It is also used to copy files from source to destination.

Ex:

ADD target/app.jar  /usr/app/app.jar

ADD <http-url>  /usr/app/app.jar

---

### WORKDIR


=> It is used to set working directory (directory navigation)

COPY target/app.jar  /usr/app/app.jar

WORKDIR /usr/app

CMD 'java -jar app.jar'

---

### USER


=> It is used to set USER to run commands

---

### EXPOSE


=> It is used to specify on which port number our application will run in container

Ex:

EXPOSE 8080

>Note: It is only to provide inforation. We can't change container port using EXPOSE.

---