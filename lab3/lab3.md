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
  namespace: reactapp
spec:
  replicas: 2
  selector:
    matchLabels:
      app: reactapp
  template:
    metadata:
      labels:
        app: reactapp
    spec:
      containers:
      - name: reactapp
        image: ifilyaninitmo/itdt-contained-frontend:master
        envFrom:
        - configMapRef:
            name: myconfigmap
```
Then apply the file to create the deployment
```
kubectl apply -f deployment.yaml
```
<img width="514" alt="image" src="https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/0b550657-6269-4daa-9ef3-be37ed5a7aa6">

### 3. Expose our application using Service
```
kubectl expose deployment web-deployment --type=NodePort --port=3000 -n reactapp
```
<img width="851" alt="image" src="https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/dd5820ca-936a-4777-ad2e-5b66adefab1b">


### 4. Install Ingress Controller in Minikube
```
minikube addons enable ingress
```
<img width="862" alt="image" src="https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/9c0adb02-f1d4-4ff2-b243-6ed5d4b1ca21">

### 5. Generate a self-signed certificate and import it to our Kubernetes cluster
Generate a self-signed certificate
```
openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out MyCertificate.crt -keyout MyKey.key
```
<img width="960" alt="image" src="https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/8ebf9606-88d0-47a6-b2e9-f0d3ad932cca">

Import the certificate to our cluster by creating a secret named *my-certificate*
```
kubectl create secret tls my-certificate --key MyKey.key --cert MyCertificate.crt -n reactapp
```
<img width="938" alt="image" src="https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/26a58544-9eb9-4a11-8e48-cc25db09c068">


### 6. Create an ingress with the imported certificate
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
kubectl apply -f ingress.yaml
```
<img width="490" alt="image" src="https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/9cf23a99-8bea-4d19-842a-ac8dea344e32">

### 7. Edit hosts file to include our host name edu.info and launch Minikube tunnel
<img width="394" alt="image" src="https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/62a5c0c6-1651-4dc4-88a0-b802df22afc2">

```
minikube tunnel
```
<img width="795" alt="image" src="https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/e5cc851c-c3b4-4fc5-a551-33d1db159816">

### 8. Access the application via browser using the hostname edu.info
<img width="1258" alt="image" src="https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/f77c101d-ce4f-4b52-a9bb-d1944d270b31">

View the self-signed certificate

<img width="576" alt="image" src="https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/e89ba2a3-41b1-48f6-bd2b-4e4050254f2b">

## DIAGRAM


