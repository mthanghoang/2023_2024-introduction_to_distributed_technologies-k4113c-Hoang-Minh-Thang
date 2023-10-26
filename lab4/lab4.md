Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)  
Year: 2023/2024  
Group: K4113c  
Author: Hoang Minh Thang  
Lab: Lab4  
Date of create: 20.09.2023  
Date of finished: 31.09.2023  
# 

## OBJECTIVE:
Get acquainted with CNI Calico and the IPAM Plugin function, learn the features of CNI and CoreDNS.
## COURSE OF WORK:
### 1. Launch a 2-node cluster with CNI Calico installation
```
minikube start --network-plugin=cni --cni=calico --nodes 2 -p multinode
```
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/91e03cd4-d3a3-4b2c-84bc-0485ec6c2225)

Get the list of nodes
```
kubectl get nodes
```
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/c737f1aa-b171-4378-a3f5-6d72bc7610b3)

Verify Calico installation in the cluster
```
kubectl get pods -l k8s-app=calico-node -A
```
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/87613819-8232-4e59-9cf6-a0e8e51c2f33)

### 2. Label the nodes
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/bd19b461-a98c-4c7e-a192-98f652a495c1)

### 3. Write a manifest file for assigning IP pool to the nodes based on their labels.
```
apiVersion: projectcalico.org/v3
kind: IPPool
metadata:
  name: master-ippool
spec:
  cidr: 192.168.0.0/24
  ipipMode: Always
  natOutgoing: true
  nodeSelector: name == "master"
---
apiVersion: projectcalico.org/v3
kind: IPPool
metadata:
  name: worker-ippool
spec:
  cidr: 192.168.1.0/24
  ipipMode: Always
  natOutgoing: true
  nodeSelector: name == "worker"
```
Apply the file
```
calicoctl apply -f ippool.yaml --allow-version-mismatch
```
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/4efd0dca-7623-4926-94c5-ea895e39a15b)

We should now have two enabled IP pools, which we can see when running
```
calicoctl get ippool -o wide --allow-version-mismatch
```
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/45fe2e74-b658-4690-aa0a-288acd0b8cb6)

### 4. Deploy an application and expose it using a service
Create a configmap
```
kubectl create configmap myconfigmap --from-literal=REACT_APP_USERNAME=thanghoang --from-literal=REACT_APP_COMPANY_NAME=ITMO -n reactapp
```
Deploy the application using deployment.yaml file
```
kubectl apply -f deployment.yaml file
```
Expose the application
```
kubectl expose deployment web-deployment --type=NodePort --port=3000 -n reactapp
```
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/6c9b9fe6-5c78-4a00-bd9b-d164d187f878)

### 5. Enable port-forwarding
```
kubectl port-forward service/web-deployment -n reactapp
```
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/8a1bd9f8-6039-4d36-9edf-294f83212175)

### 6. Access the application via browser
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/b05564f2-a767-4723-aaca-328834e3de24)

The container name and the container IP will not change even when we reload the page multiple times. To explain this, let's get the IP address of our pods
```
kubectl get pods -o wide -n reactapp
```
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/7679341b-a943-48e8-bd01-f9dd02202ab7)

As we can see one pod is deployed in the master node with IP pool 192.168.0.0/24 and the other pod is deployed in the worker node with IP pool 192.168.1.0/24
The service only routes our request to the worker node, and that's why the container name and container IP fields do not change.

### 7. Connect to one of the pods and ping the other
Connect to the pod in the master node and ping the pod in the worker node
```
kubectl exec -it web-deployment-6f6988c8d7-z476h -n reactapp -- ping 192.168.1.128
```
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/da53133d-89f9-475c-8955-3c0ecb6488de)
The ping works.

## DIAGRAM
![image](https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/fe0decdf-e070-4704-b966-bdb24e613ebd)








