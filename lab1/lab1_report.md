University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)  
Year: 2023/2024  
Group: K4113c  
Author: Hoang Minh Thang  
Lab: Lab1  
Date of create: 20.09.2023  
Date of finished: 31.09.2023  
# 

## OBJECTIVE:
Get familiar with Minikube and Docker, deploy my first pod.
## COURSE OF WORK:
### 1. Start Docker daemon by running Docker Desktop application.
### 2. After installing Minikube, deploy Minikube cluster with the command
```
minikube start
```
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/609417dd-1859-4136-a9a2-7714a8a1792b)

### 3. Create a deployment named *vault*, using image Vault, version 1.13.3
```
kubectl create deployment vault --image=vault:1.13.3
```
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/27a5bcff-20a4-4a70-95a2-8487cc7bf20b)

### 4. Get the pod name with the command
```
kubectl get pod
```
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/fea95435-95fc-41d4-978b-3d9367d763d9)

After creating the deployment, a pod is also created.

### 5. Create a service to access the pod through port 8200
```
kubectl expose pod vault-6c599ff9f-ch2fm --type=NodePort --port=8200
```
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/c3619f11-5a39-407e-9d67-b9a5ab648e60)

### 6. After that use port forwarding so that we can access the service from our local machine
```
kubectl port-forward service/vault-6c599ff9f-ch2fm 8200:8200
```
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/85ef7b47-e723-4013-8356-0ae3a0847bc5)

### 7. Access the service using the link *localhost:8200*
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/b89ddf25-62b2-4328-a600-f2ac456664fe)

### 8. In order to find the credentials, use this command
```
kubectl logs vault-6c599ff9f-ch2fm
```
The credentials are at the end of the output.

![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/bb24e8f0-d6af-49aa-bb6e-1a986ca24057)

### 9. Use the credentials to authenticate, then we will be taken to this page
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/35927871-c020-4cc7-a1ca-7cacafcd516e)

### 10. Another way to create a pod is to use *yaml* file (myfirst.yaml)
```
apiVersion: v1
kind: Pod
metadata:
  name: "vault"
  namespace: default
  labels:
    app: ""
spec:
  containers:
  - name: vault
    image: "vault:1.13.3"
    ports:
    - containerPort: 8200  
```
This file specifies the pod's name and port, as well as the image used to create the container.
After creating the *yaml* file, run the following command to create a pod
```
kubectl apply -f myfirst.yaml
```
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/d06ef56b-1820-418e-8a9f-15e29772042b)

Port forwarding
```
kubectl port-forward pod/vault 8200:8200
```

### 11. Stop the Minikube cluster
```
minikube stop
```
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/e652b19c-0061-4c9c-8820-d2f47e15bbd2)

## DIAGRAM
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/903fa9db-79f6-4089-9654-52a15c121a03)



