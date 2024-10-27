# Docker k8 jenkins ashok 
Docker --> containerization software

Kubernetes--> orchestration software

Jenkins--> CI CD software

these 3 are used to automate project build and deployment process!!

=> In realtime we will use several environments to test our application.

1) DEV
2) SIT
3) UAT
4) PILOT
5) PROD (final delivery)

=> Dev env used by developers for code integration testing

=> SIT env used by Testers for system integration testing

=> UAT env used by client side team for acceptance testing (Go or No Go)

=> Pilot env used for pre-production testing

=> Prod env used for live deployment (end users can access our app)


=> As a devops engineer we are responsible to setup infrastructure to run our application

=> We need to install all required softwares (dependencies) to run our application

Note: We need to setup dependencies in all environments to run our application.

Note: There is a chance of doing mistakes in dependencies installation process (version compatibility issues can occur)

=> To simplify application execution in any machine we can use Docker.

## What is docker
=> Docker is a free & open source software

=> Docker is used for containerization

=> With the help of docker, we can run our application in any machine.

Container = package (app code + app dependencies)

=> Docker will take care of dependencies installation required for app execution.

=> We can make our application portable using Docker.
