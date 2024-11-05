# Notes 2
## DockerFile

=> Dockerfile contains set of instructions to build docker image

Filename : Dockerfile


---

## How to create Docker image

Default name of dockerfile:DockerFile

Syntax-1 : docker build -t \<image-name\> .

. means current directory in which dockerfile is present -t means tag name

Syntax-2 : docker build -t \<image-name\> -f \<docker-file-name> .

syntax-2 is for if you have renamed your dockerFile to some other name!!-f means filename!!



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

=> now let us try to understand that!

---
	
### FROM


=> It is used to specify base image for our application 

=>we need to tell which software needed to run our application!!

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

MAINTAINER Ashok\<ashok.b@oracle.com\>

---

### RUN (used at time of image creation)


=> RUN keyword is used to specify instructions to execute at the time of docker image creation.

Ex:

RUN 'git clone \<url\>'

RUN 'mvn clean package'

>Note: We can write multiple RUN instructions in single docker file and all those instructions will be processed in the order.

---

### CMD (used at time of container creation)


=> CMD keyword is used to specify instructions to execute at the time of docker container creation.

Ex:

CMD 'java -jar app.jar'

CMD 'app.py'

> Note: We can write multiple CMD instructions in single docker file but docker will process only last CMD instruction so no use of multiple CMD instruction .

---

### COPY


=> It is used to copy files from host machine to container machine. 

=> used to copy source code

Ex:

COPY target/app.jar  /usr/app/app.jar

COPY target/app.war  /usr/app/tomcat/webapps/app.war

COPY app.py /usr/app/app.py

COPY \<path of host machine\> \<path of docker machine\>

---

### ADD Keyword


=> It is also used to copy files from source to destination.(just like copy)

=> copy and And are used for same purpose

=> Difference Copy keyword can copy only from host machine but add can copy from internet from some URL

Ex:

ADD target/app.jar  /usr/app/app.jar

ADD <http-url>  /usr/app/app.jar

---

### WORKDIR


=> It is used to set working directory (directory navigation) (just like cd of linux)

COPY target/app.jar  /usr/app/app.jar

WORKDIR /usr/app (go to that directory)

CMD 'java -jar app.jar' (now this command will execute in /usr/app)

---

### ENTRYPOINT


=> Entrypoint is used to execute instructions when docker container is creating.(same as of CMD)
 

Ex: 

ENTRYPOINT["java", "-jar", "app.jar"]

ENTRYPOINT["python", "app.py"]

>Note: CMD instruction we can override using command line arguments where ENTRYPOINT instruction we can't override.

we can override CMD command by using 

```text
docker run image-name --CMD command
```

but entrypoint command will not be overridden

---
### USER
(never used but just to know)

=> It is used to set USER to run commands

---

### EXPOSE(just for info)

=> It is used to specify on which port number our application will run in container

=> optional but recommended .just for info of port if someone else see docker file !!it do not do anything (not change the port)!!

Ex:

EXPOSE 8080

>Note: It is only to provide information. We can't change container port using EXPOSE.

---

### Example Docker File

FROM ubuntu

MAINTAINER Ashok <ashok.b@oracle.com>

RUN echo 'hello from run instruction-1'
RUN echo 'hello from run instruction-2'

CMD echo 'hi from cmd-1'
CMD echo 'hi from cmd-2'


----
- create docker image using dockerfile

    $ docker build -t img-1 .

- Run docker image to create docker container

    $ docker run img-1


