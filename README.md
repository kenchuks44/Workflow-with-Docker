# Workflow-with-Docker

## This project demonstrates how Docker containers are used in the development and deployment process to create a consistent and portable environment for building, testing and running applications. Specifically, we will use a Java Maven application. Firstly, we will push the application code to a remote repository, then connect Jenkins to the repository. Jenkins builds the application as well as a Docker image which will now be pushed to a Docker repository (DockerHub)

![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/09077fa1-742a-43b9-8e51-588028c6f88d)

## Step 1: Installing Jenkins 
Here, we will run Jenkins as a Docker container 
-	Create a Jenkins server/VM/EC2 Instance/Digital Ocean Droplet
-	Open port 22 for SSH
-	Add custom port (8080) to expose Jenkins application
-	SSH into the server and run the commands below:
```
apt update
apt install docker.io
docker run –p8080:8080 –p50000:50000 –d –v jenkins_home:/var/jenkins_home jenkins/jenkins:lts
```
![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/a07701dc-d158-4bd8-8b30-12685980aafd)

![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/9a4e582e-0c45-4726-aeab-5049fec17775)

![new](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/8776ee79-9888-49a9-96f6-0ed132f98f38)


![Screenshot (140)](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/8615d273-d2d2-4cd5-b062-577ede2203bb)


![new 1](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/f083b78b-da67-49f7-bce9-72eb48efd647)


![Screenshot (142)](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/e7b21b52-2b0d-48ef-9a0b-50c6009666f8)


![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/c7cdc8bd-bff7-4d0d-9bfd-519fc2ae5a2e)

![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/63d09427-c4a1-437d-a43f-72b45fa0728b)

![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/82f784e6-aaa3-491c-9bb9-ce885c965851)

## Step 2: 
After running Jenkins as a Docker container, we authenticate Jenkins using the Initial Admin Password. To do this, we go into the container where Jenkins runs and execute the commands below
```
docker ps 
docker exec –it <container_ID> bash
cat /var/jenkins_home/secrets/initialAdminPassword
```
![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/c5484d45-7ca0-45b5-8253-d42ecbaf991b)

Then, in the browser, using the Jenkins server IP and the port to load the Jenkins UI, install suggested plugins and create your first admin user

![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/be81edb8-a02d-4e6d-8613-fb16d6313d8b)

![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/8354df8f-c0dc-4ab6-a3c7-ca0062ed1aef)

![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/037ad2a0-0b8e-4bd4-b74c-16d914d647e1)

![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/397a02a4-18f8-4a2a-87e1-139fad5d6ecb)

## Step 3:Install Build tools in Jenkins 
Here, as we have a Java Maven Application, we will install maven plugin. Other required plugins like Github plugin already got installed when we installed recommended plugins during Jenkins initialization.

![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/2f4fa804-b8b3-4518-b458-414e8ad3cf79)

Next, we create a new free-style job called java-maven-build. Next, we configure the java-maven-build. Under Global configuration, we add Maven plugin that had been installed.

![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/14143b8a-119e-4c61-a56c-9b62fd087df5)

## Step 4: Connect Jenkins to Git repository 
Firstly, we create a PAT under Developer Settings in GitHub and add the credentials to  Jenkins

![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/88aaa722-701b-40c3-b04f-5300c1134365)

![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/8f6ea344-cd55-4dae-a610-3cfa51a5ceb0)

![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/9ef9c10a-e6c4-4a6f-b911-4fb6787c5fbb)

Under Configuration, input the GitHub credentials that was created, specify the repository URL and the branch to build

![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/c94aadad-6e47-4dc0-9836-f37827a339a7)

![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/9781d47d-850b-48d1-b8eb-08b9f09a7181)

## Step 5: Building Docker image and pushing to DockerHub 
To build docker image of our application, we need to have docker commands available in Jenkins by attaching a volume to Jenkins from the host file. This entails mounting docker directory from the server into the container as a volume. To set this up, run the commands below. The commands re-attaches persistent volume to new container.
```
docker stop <container_ID>
docker run –p8080:8080 –p50000:50000 –d \
-v jenkins_home:/var/jenkins_home \
-v /var/run/docker.sock:/var/run/docker.sock \
-v $(which docker) : /usr/bin/docker jenkins/jenkins:lts
```

![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/756c3a5a-e885-470e-b75d-5475bb3afcff)

Then, we reconfigure permission for /var/run/docker.sock file to enable docker commands to now be executed inside the container by running the commands below
```
docker exec –u 0 –it <container_ID> bash
chmod 666 /var/run/docker.sock
```
## Step 6: Pushing image to DockerHub
We will first create a DockerHub account, add the dockerhub credentials in Jenkins. In the java-maven-app configuration in Jenkins, under build steps, we invoke the top-level maven target and then input the commands below into the execute shell window. Also, under java-maven-build configuration, we configure the build environment using the dockerhub credentials created. Finally, we build the application.

```
docker build –t <dockerhub_username>/<dockerhub_repo>:jma-1.0
echo $PASSWORD | docker login –u $USERNAME --password-stdin
docker push <dockerhub_username>/<dockerhub_repo>:jma-1.0
```
![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/69572ba7-6228-4f99-a9b0-ce31bfda1bc3)

![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/6fa5fc1b-f609-48d1-88e2-7ba55f2eb50d)

![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/a4e73b6e-9140-4108-8ebb-18cd26b5167c)

![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/561ed308-aed7-4fa1-8b21-0927e76ed938)

![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/6f84b66c-f11c-4b5d-bf9f-f2dcfd58445d)

![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/28058483-eb31-4b01-8f38-b9a4ca2cca2d)

![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/481361c1-3832-460b-8421-d42f3c8c8340)

![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/e5b5021e-3826-4a9e-8b32-d427bbd4b326)

![image](https://github.com/kenchuks44/Workflow-with-Docker/assets/88329191/9593157e-a66c-49a3-9be5-e2a34656382f)




































