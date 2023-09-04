# Implementing the deployment of microservices while ensuring the utilization of persistent volumes within a Kubernetes environment.

## Hello again, fellow Kubernetes enthusiasts! In this post, we'll guide you through the process of deploying a two-tier application within the Kubernetes ecosystem, harnessing the capabilities of a Flask Python backend and a MongoDB database. Throughout this project, we'll also implement and utilize Persistent Volumes (PV) and Persistent Volume Claims (PVC).
## Before diving into this thrilling Microservices Deployment endeavor, let's ensure you've met the following prerequisites:
## Prerequisites:
### Before embarking on this journey, it is assumed that you possess a fundamental grasp of Kubernetes components, including Pods, Deployments, and Services.
### DockerHub Account: 
To successfully complete this tutorial, it is essential to have an active DockerHub account. This account will be utilized for both uploading and downloading the Docker image that we'll be constructing. You can create a DockerHub account. Docker Image visiting the following link: https://hub.docker.com/repository/docker/sandypaia320/microservices/tags?page=1&ordering=last_updated
### Git: 
Ensure that Git is installed on your Ubuntu machine before proceeding.
### Kubernetes Cluster: 
In order to follow along with these practical exercises, it is imperative that you have access to a pre-configured and operational Kubernetes cluster.

## Step 1: Cloning the source code of the project
```
git clone https://github.com/Paisandy/microservices-k8s--.git
```
![image](https://github.com/Paisandy/microservices-k8s--/assets/115485972/c0770f40-df2b-4750-a4ee-e90ae5e46644)

## Step 2: Download the Docker on Ubuntu 
```
sudo apt-get update
sudo apt-get install docker.io -y
sudo usermod -aG docker $USER
sudo reboot
```
Wait for 2 min. After that start the Ec2 instance.
### Do docker ps so that it is downloaded correctly
```
docker ps
```
![image](https://github.com/Paisandy/microservices-k8s--/assets/115485972/3ddbca5b-b4d4-4194-912a-ed5622747466)
### Buid the Dockerfile
![image](https://github.com/Paisandy/microservices-k8s--/assets/115485972/ad7b1cbd-d0c3-45f9-a0de-e0bcd6ab89e2)

```
docker build . -t sandypaia320/microservices:latest
```
![image](https://github.com/Paisandy/microservices-k8s--/assets/115485972/14300498-e0ab-4553-8953-6c77a1753d2e)
![image](https://github.com/Paisandy/microservices-k8s--/assets/115485972/a445c250-f9d9-4b50-ada0-b11cfd2e7b90)

## Step 3: Push the docker image to Docker Hub
### Login to DockerHub form docker CLI:
```
docker login
```
![image](https://github.com/Paisandy/microservices-k8s--/assets/115485972/dd20ac14-0058-4f6b-8331-d729db57e025)
### Push the Docker image to DockerHub:
command is for example: "docker push username/microservices:latest"
```
docker push sandypaia320/microservices:latest
```
![image](https://github.com/Paisandy/microservices-k8s--/assets/115485972/d7dee8ad-d6a8-4ac1-8c99-12b24d7ab905)
![image](https://github.com/Paisandy/microservices-k8s--/assets/115485972/e8314dda-773f-43d7-bd90-d5bb0a1ccda0)

## Step 4: Creating a Persistent Volume Manifest for K8s
Basically I have used in Killer Code. The Killer code is a open source where you can practice a K8s. It's a free of cost.

Congratulations on reaching this milestone! 🥳 You've successfully completed the initial steps of the continuous integration process by building a Docker image and uploading it to Docker Hub.

Now, our focus shifts to deploying the application within our Kubernetes cluster. To achieve this, we'll start by creating a Persistent Volume (PV) object designed to securely store MongoDB data.

Persistent Volumes, or PVs, serve as a vital component in Kubernetes, providing a dependable means to store data. Particularly beneficial for databases like MongoDB, PVs offer dedicated storage space that ensures the safe preservation of your data. Regardless of changes or restarts to your applications or containers, PVs guarantee the integrity and accessibility of your valuable data within the Kubernetes environment.

In summary, PVs play a crucial role in safeguarding your database information and maintaining its consistency in the Kubernetes ecosystem. For more in-depth information about persistent volumes, please refer to the official Kubernetes documentation on this topic: Kubernetes - Persistent Volumes (https://kubernetes.io/docs/concepts/storage/persistent-volumes/#node-affinity)




