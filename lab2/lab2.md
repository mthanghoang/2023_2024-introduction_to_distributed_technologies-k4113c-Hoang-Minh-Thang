University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)  
Year: 2023/2024  
Group: K4113c  
Author: Hoang Minh Thang  
Lab: Lab2  
Date of create: 20.09.2023  
Date of finished: 31.09.2023  
# 

## OBJECTIVE:
Get acquainted with different types of container deployment, get acquainted with network services and deploy a web application.
## COURSE OF WORK:
### 1. Create a deployment with 2 replicas based on the image *ifilyaninitmo/itdt-contained-frontend*.
First of all, create a yaml manifest file
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
        env:
        - name: REACT_APP_USERNAME
          value: thanghoang
        - name: REACT_APP_COMPANY_NAME
          value: ITMO
```
This file passes environment variables to our 2 replicas.
After that run this command to create the deployment.
```
kubectl apply -f myfirst.yaml
```
### 2. Create a service to expose our deployment to external traffic
```
kubectl expose deployment/web-deployment --type="NodePort" --port 3000 
```
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/773ec893-3e7d-4558-81d3-8ebb25742d0d)

### 3. Launch port-forwarding mode to access our containers through browser
```
kubectl port-forward service/web-deployment 3000:3000
```
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/bfe1ac13-a3e9-459f-8ea9-6ce9287cad6a)

### 4. Access the web page through a browser
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/b23ca7cd-6266-4940-8018-b6356d00af79)
The 2 environment variables REACT_APP_USERNAME and REACT_APP_COMPANY_NAME will always be the same. However the container name can change, depending on which pod the service routes the traffic to.
In order to test this, I deleted 1 of the 2 pods and reexecute the port-forwarding command. The container name on the web page was changed to the other pod's.

## DIAGRAM

