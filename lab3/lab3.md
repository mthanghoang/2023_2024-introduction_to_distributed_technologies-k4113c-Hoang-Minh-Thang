![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/bb42d580-7244-479f-b162-1abbf1ae4a79)University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)  
Year: 2023/2024  
Group: K4113c  
Author: Hoang Minh Thang  
Lab: Lab3  
Date of create: 20.09.2023  
Date of finished: 31.09.2023  
# 

## OBJECTIVE:
Get acquainted with certificates and “secrets” in Minikube, secure data storage in Minikube.
## COURSE OF WORK:
### 1. Create a config map with 2 variables REACT_APP_USERNAME and REACT_APP_COMPANY_NAME
```
kubectl create configmap myconfigmap --from-literal=REACT_APP_USERNAME=thanghoang --from-literal=REACT_APP_COMPANY_NAME=ITMO
```
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/72806e10-a7b1-457f-a856-a24aaec59b16)

### 2. Create a deployment with 2 replicas, using the config map to pass the environment variables to the pods
Create deployment manifest file
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-deployment
  labels:
    app: web
spec:
  replicas: 2
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: web-container
        image: ifilyaninitmo/itdt-contained-frontend:master
        envFrom:
        - configMapRef:
            name: myconfigmap
```
Then apply the file to create the deployment
```
kubectl apply -f myfirst.yaml
```
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/6b067a86-4b07-49eb-b4f1-7eabde1b718f)

### 3. Install Ingress Controller in Minikube
```
minikube addons enable ingress
```
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/584c7d48-fe70-4656-a242-cdaeec3482fe)

