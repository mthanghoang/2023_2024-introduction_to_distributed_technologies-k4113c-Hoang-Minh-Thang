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
kubectl create configmap myconfigmap --from-literal=REACT_APP_USERNAME=thanghoang --from-literal=REACT_APP_COMPANY_NAME=ITMO -n reactapp
```
<img width="946" alt="image" src="https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/ec64b50b-e5e9-40d3-bfa3-5f98042f9cdb">


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

### 4. Generate a self-signed certificate and import it to our Kubernetes cluster
Generate a self-signed certificate
```
openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out MyCertificate.crt -keyout MyKey.key
```
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/448d038f-c677-42e7-bf21-e23e19f06a27)

Import the certificate to our cluster by creating a secret named *my-certificate*
```
kubectl create secret tls my-certificate --key MyKey.key --cert MyCertificate.crt
```
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/311206bd-5cac-45da-a901-7fc850b9d0db)

### 5. Create an ingress with the imported certificate
Create a manifest file for the ingress *ingress.yaml*
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: web-deployment
  namespace: reactapp
spec:
  tls:
  - hosts:
      - edu.info
    secretName: my-certificate
  rules:
  - host: edu.info
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-deployment
            port:
              number: 3000
```
Then apply it
```
kube
```

