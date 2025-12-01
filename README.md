**E-Commerce Microservices on Kubernetes (EKS)**

This project contains the complete Kubernetes deployment of the Roboshop Microservices Application, deployed on Amazon EKS using eksctl.

Roboshop is an e-commerce application consisting of multiple microservices built with different technologies like Node.js, Java, Python, Go, MySQL, MongoDB, Redis, RabbitMQ, etc.

This repository includes:

✔ Kubernetes manifests for all services
✔ ConfigMaps + environment variables
✔ Liveness & readiness probes
✔ Services (ClusterIP / LoadBalancer)
✔ Debug pod for testing
✔ Fully working EKS cluster setup

🚀 Architecture Overview

The Roboshop platform is split into the following microservices:

frontend (nginx)

catalogue (nodejs)

user (nodejs)

cart (nodejs)

shipping (java)

payment (python)

mongodb

mysql

redis

rabbitmq

debug pod (used to test connectivity)

Each microservice has its own:

Deployment

Service

ConfigMap (where required)

🏗️ EKS Cluster Setup

Cluster created using eksctl:

eksctl create cluster --config-file=eks.yaml


Example config:

apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig
metadata:
  name: eks-project
  region: us-east-1
managedNodeGroups:
  - name: eks-project
    instanceTypes: ["m5.large", "t3.small", "t3.medium", "c5.large"]
    desiredCapacity: 2
    spot: false


Update kubeconfig:

aws eks update-kubeconfig --region us-east-1 --name eks-project

📁 Folder Structure
K8-Project/

    ├── cart/

    ├── catalogue/
    
    ├── user/
    
    ├── shipping/
    
    ├── payment/
    
    ├── frontend/
    
    ├── mysql/
    
    ├── mongodb/
    
    ├── redis/
    
    ├── rabbitmq/
    
    ├── debug/
    
    └── eks.yaml


Each folder contains:

manifest.yaml

🧩 Deployment of Microservices

Apply all components:

kubectl apply -f manifest.yaml


Or deploy all at once:

kubectl apply -f .

🔧 Debug Pod

Used to test connectivity between services:

kubectl exec -it debug -n roboshop -- bash


Examples:

curl cart:8080/health
redis-cli -h redis ping
mongo --host mongodb --eval "db.adminCommand('ping')"
curl frontend

🔍 Health Checks

Each microservice uses:

Liveness Probes → restart if unhealthy

Readiness Probes → only accept traffic when ready

Example:

livenessProbe:
  httpGet:
    path: /health
    port: liveness-port
  failureThreshold: 1
  periodSeconds: 15

🌐 Accessing the Application

The frontend service is exposed via LoadBalancer:

kubectl get svc -n roboshop


Output example:

frontend   LoadBalancer   98.93.27.230   80:xxxx/TCP


Open in browser:

http://98.93.27.230

🛠️ Testing Services

Inside debug pod:

curl cart:8080/health
curl user:8080/health
curl shipping:8080/health
curl payment:8080/health
curl catalogue:8080/health
curl frontend


Test DBs:

mysql -h mysql -u root -pRoboShop@1 -e "show databases;"
redis-cli -h redis ping
mongo --host mongodb --eval "db.adminCommand('ping')"

🧹 Cleanup

Delete the cluster:

eksctl delete cluster --region us-east-1 --name eks-project

📝 Author

Anil Kathoju
GitHub: https://github.com/anilkathoju2/K8-Project