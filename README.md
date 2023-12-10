# Workflow-with-Docker

## This project demonstrates how Docker containers are used in the development and deployment process to create a consistent and portable environment for building, testing and running applications. Specifically, we will use a Java Maven application. Firstly, we will push the application code to a remote repository, then connect Jenkins to the repository. Jenkins builds the application as well as a Docker image which will now be pushed to a Docker repository (DockerHub)

![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/09077fa1-742a-43b9-8e51-588028c6f88d)

## Step 1: 
Installing Jenkins: Here, we will run Jenkins as a Docker container 
-	Create a Jenkins server/VM/EC2 Instance/Digital Ocean Droplet
-	Open port 22 for SSH
-	Add custom port (8080) to expose Jenkins application
-	SSH into the server and run the commands below:
```
apt update
apt install docker.io
docker run –p8080:8080 –p50000:50000 –d –v jenkins_home:/var/jenkins_home jenkins/jenkins:lts
```
