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
Get acquainted with certificates and “secrets” in Minikube, secure data storage in Minikube.
## COURSE OF WORK:
### 1. Create a config map with 2 variables REACT_APP_USERNAME and REACT_APP_COMPANY_NAME
```
kubectl create configmap myconfigmap --from-literal=REACT_APP_USERNAME=thanghoang --from-literal=REACT_APP_COMPANY_NAME=ITMO -n reactapp
```
<img width="946" alt="image" src="https://github.com/mthanghoang/2023_2024-introduction_to_distributed_technologies-k4113c-Hoang-Minh-Thang/assets/61542577/ec64b50b-e5e9-40d3-bfa3-5f98042f9cdb">

