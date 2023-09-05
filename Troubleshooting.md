### Troubleshooting Microservices Deployment with Kubernetes
![WhatsApp Image 2023-09-05 at 14 41 52](https://github.com/Paisandy/microservices-k8s--/assets/115485972/e376279c-5469-4607-bcf9-1cc66ab9bd47)
Greetings, Kubernetes enthusiasts! In this post, we'll provide a troubleshooting guide for deploying a two-tier application with Kubernetes.

## Steps to follow:
1. Begin by setting up two AWS EC2 instances, both using the t2.medium instance type.
2. Next, follow the instructions for installing `kubeadm` from this link: [Kubeadm Installation Guide](https://github.com/Paisandy/kubestarter/blob/main/kubeadm_installation.md).
3. Once the installation is complete, you can proceed to use Docker images available on Docker Hub, which you already have access to.
4. On the worker node instance, you create a folder by executing the following command:
```
mkdir mongodata
```
5. Once the folder is created, navigate into it with the following command:
```
cd mongodata
```
6. To confirm your current directory, you can run the "pwd" command, which will display the current path.
![image](https://github.com/Paisandy/microservices-k8s--/assets/115485972/c0d21813-d335-4628-8ca1-058ce351430e)
7. Go on the master instance,
8. To proceed, access the master server and make modifications to the mongo-pv.yml file. Below, I've provided a code snippet that you can simply copy and paste. Lastly, ensure the path matches your specific setup; I've used /home/ubuntu/mongodata, but replace it with the path you've configured.
 ```
   apiVersion: v1
kind: PersistentVolume
metadata:
  name: mongo-pv
spec:
  capacity:
    storage: 256Mi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: /home/ubuntu/mongodata
```
![image](https://github.com/Paisandy/microservices-k8s--/assets/115485972/c50f857a-0f0e-4e9f-8d72-51cb35b2a501)
10. after that save it.
11. should add "volumeMode" as a Filesyatem and persistent volume here is the documents follow it https://kubernetes.io/docs/concepts/storage/persistent-volumes/
12. should add path of the worker node
13. now do "curl" command run it.
![image](https://github.com/Paisandy/microservices-k8s--/assets/115485972/2512f01f-5c09-4846-871f-aa6effe63392)

### The reason is 
the hostPath is set to "/home/ubuntu/mongodata"

Verify that the directory specified in hostPath has appropriate filesystem permissions that allow the Pod to read and write to it. The directory may need to be accessible by the Kubernetes service account associated with the Pod.

## Let's see the comparison between the "old mongo-pv.yml"  and "new mongo-pv.yml"
### "old mongo-pv.yml" --> snippet "a"
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
### "new mongo-pv.yml" --> snippet "b"
```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mongo-pv
spec:
  capacity:
    storage: 256Mi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: /home/ubuntu/mongodata
```

## The difference between code snippet "a" and code snippet "b" lies in the configuration of the PersistentVolume (PV) settings in Kubernetes:
Here's why "a" is different from "b":

### 1. "persistentVolumeReclaimPolicy":
In code snippet "a," the "persistentVolumeReclaimPolicy" is set to "Retain." This means that even if the associated PersistentVolumeClaim (PVC) is deleted, the data stored in the PV will be retained. This is useful when you want to preserve data across PVC deletions. In contrast, code snippet "b" does not specify a reclaim policy, so it uses the default value, which is typically "Delete." This means that when the associated PVC is deleted, the PV and its data will be deleted as well.
### 2. "volumemode":
In code snippet "a," the "volumeMode" is explicitly set to "Filesystem." This specifies that the PV should be treated as a filesystem. This is important when you want to use the PV as a storage volume for applications that expect a filesystem. In code snippet "b," the volumeMode is not set, so it defaults to "Block," which means the PV is treated as a block device.

The reason why code snippet "a" is working better for your use case might be related to the specific requirements of your application. If your application expects a filesystem and you want to retain data even after PVC deletions, then the settings in code snippet "a" are more suitable. If you don't need these features, then code snippet "b" may suffice.
