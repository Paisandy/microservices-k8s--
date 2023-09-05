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

copy the YAML from here or you can see in Github file or else do "cat mongo-py.yml"
```
cat mongo-pv.yml
```
### mongo-pv.yml
```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mongo-pv
spec:
  capacity:
    storage: 256Mi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /tmp/db
```
run the mongo-pv.yml
```
kubectl apply -f mongo-pv.yml
```
![image](https://github.com/Paisandy/microservices-k8s--/assets/115485972/a66a3336-1284-4d21-9e0e-4b631784dd42)

## Step 5: Creating a Persistent Volume Claim (PVC) Manifest in K8s
Once the creation of the persistent volume has been completed successfully, the next step is to establish a persistent volume claim (PVC). This PVC will be utilized by our MongoDB deployment to request and allocate storage space for storing data.

Think of Persistent Volume Claims (PVCs) as customized storage requests in the Kubernetes environment. They serve as formal requests for storage resources within Kubernetes when your applications require them. These requests specify both the desired amount and the specific type of storage needed. Kubernetes then utilizes this information to find and allocate the requested storage resources, ensuring that your applications have sufficient storage capacity to store and retrieve data as required.
copy the YAML from here or you can see in Github file or else do "cat mongo-pvc.yml"
```
cat mongo-pvc.yml
```
### mongo-pvc.yml
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mongo-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 256Mi
```
run the mongo-pvc.yml
```
kubectl apply -f mongo-pvc.yml
```
![image](https://github.com/Paisandy/microservices-k8s--/assets/115485972/50123d80-5a93-4326-b5d0-1935e6c723d2)

## Step 6: Create a Deployment Manifest file of Mongo DB
```
cat mongo.yml
```
### mongo.yml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongo
  labels:
      app: mongo
spec:
  selector:
    matchLabels:
      app: mongo
  template:
    metadata:
      labels:
        app: mongo
    spec:
      containers:
        - name: mongo
          image: mongo
          ports:
            - containerPort: 27017
          volumeMounts:
            - name: storage
              mountPath: /data/db
      volumes:
        - name: storage
          persistentVolumeClaim:
            claimName: mongo-pvc
```
run the mongo.yml
```
kubectl apply -f mongo.yml
```
### mongo-svc.yml
```
cat mongo-svc.yml
```
```
apiVersion: v1
kind: Service
metadata:
  labels:
    app: mongo
  name: mongo
spec:
  ports:
    - port: 27017
      targetPort: 27017
  selector:
    app: mongo
```
run the mongo-svc.yml
```
kubectl apply -f mongo-svc.yml
```
![image](https://github.com/Paisandy/microservices-k8s--/assets/115485972/2dafd394-55b3-4c07-9b38-516cdac1f474)

## Step 7: Create the Deployment manifest File of the Flask App
```
cat taskmaster.yml
```
### taskmaster.yml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: taskmaster
  labels:
    app: taskmaster
spec:
  replicas: 1
  selector:
    matchLabels:
      app: taskmaster
  template:
    metadata:
      labels:
        app: taskmaster
    spec:
      containers:
        - name: taskmaster
          image: sandypaia320/microservices:latest
          ports:
            - containerPort: 5000
          imagePullPolicy: Always
```
run the taskmaster.yml
```
kubectl apply -f taskmaster.yml
```
Make sure to replace the value of image key with that of your "image" name as shown in the YAML file.

The deployment creates 1 pod with the "sandypaia320/microservices:latest" image we pushed earlier. The key containerPort declares that the container accepts connections from port 5000.
![image](https://github.com/Paisandy/microservices-k8s--/assets/115485972/1edb6978-11dc-4198-a3b0-354a4d1ee563)

## Step 8: Create the service manifest file of the Flask App
```
cat taskmaster-svc.yml
```
### taskmaster-svc.yml
```
apiVersion: v1
kind: Service
metadata:
  name: taskmaster-svc
spec:
  selector:
    app: taskmaster
  ports:
    - protocol: TCP
      port: 80
      targetPort: 5000
      nodePort: 30007
  # This service is of type NodePort, that means you can access it using the URL
  # <NodeIP>:30007 which is the nodePort mentioned above
  type: NodePort
```
run the taskmaster-svc.yml
```
kubectl apply -f taskmaster-svc.yml
```
![image](https://github.com/Paisandy/microservices-k8s--/assets/115485972/972aafb9-22aa-429f-92a0-79bfca01e107)
### do check the pods
```
kubectl get pods
```
![image](https://github.com/Paisandy/microservices-k8s--/assets/115485972/0cc02eca-e45a-4292-9eda-0cf490288c85)

## Step 9: Test the Application
![image](https://github.com/Paisandy/microservices-k8s--/assets/115485972/3a1a1970-279f-4e2b-bcb2-e386bc7ba0c0)
You can now test the application by using 
kubectl exec -it "pod name" -- bash
```
kubectl exec -it mongo-6976f643c8-9g5h7 -- bash
```
![image](https://github.com/Paisandy/microservices-k8s--/assets/115485972/2308431c-9e5b-458c-9e18-4f780e114845)
### do curl
```
curl http://172.0.0.1:30007
```
![image](https://github.com/Paisandy/microservices-k8s--/assets/115485972/f11cba28-098a-4ca6-a707-53749a7e810d)
### Test MongoDB:
```
curl htttp://127.0.0.1:30007/tasks
```
![image](https://github.com/Paisandy/microservices-k8s--/assets/115485972/0b718d13-37f4-4b24-bb78-b40978c64261)
![image](https://github.com/Paisandy/microservices-k8s--/assets/115485972/f6a7f841-2028-46b6-a6c1-88856ee100e0)
![image](https://github.com/Paisandy/microservices-k8s--/assets/115485972/720b30df-1561-4f8f-8235-0b2131399acc)

### Congratulations on your Microservices Project!
Congratulations on your successful completion of this hands-on microservices project, utilizing Flask and MongoDB, while effectively harnessing the capabilities of Persistent Volumes and Claims. Your deployment of this impressive application has not only demonstrated your technical prowess but has also provided you with valuable insights into the realm of Kubernetes.

Why not share this remarkable achievement on LinkedIn to inspire and connect with like-minded tech enthusiasts? And always keep in mind that if you have any questions or require guidance for your next project, please don't hesitate to reach out to me on LinkedIn (https://www.linkedin.com/in/er-sandesh-pai-994181201/) . Keep pushing the boundaries of innovation and continue exploring the dynamic world of Kubernetes!
