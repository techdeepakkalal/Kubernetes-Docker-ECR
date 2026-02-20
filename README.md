# 🚀 MultiCloud DevOps Project (Docker → ECR → EKS → Kubernetes)

This project demonstrates a complete DevOps workflow:

✔ Application Containerization using Docker  
✔ Image Push to AWS ECR  
✔ EKS Cluster Creation  
✔ Kubernetes Deployment using NodePort  
✔ Application Access via Public IP  

---

# 🧱 Project Architecture

Frontend (React)  
⬇  
Backend (Node.js + Express)  
⬇  
MySQL Database  

---

# ☁️ STEP 1 — Launch EC2 Instance

1. Launch EC2 (Amazon Linux 2)
2. Instance Type: t2.medium
3. Open Security Group Ports:
   - 22 (SSH)
   - 80 (Frontend)
   - 84 (Backend)
   - 30080 (Kubernetes NodePort)
   - 30084 (Kubernetes NodePort)

Connect:

ssh -i key.pem ec2-user@PUBLIC_IP

---

# 🐳 STEP 2 — Install Docker

sudo yum update -y
sudo yum install docker -y
sudo systemctl start docker
sudo usermod -aG docker ec2-user
exit

Reconnect to EC2

Verify:

docker --version

---

# 📦 STEP 3 — Install Git

sudo yum install git -y
git --version

---

# 📥 STEP 4 — Clone Repository

git clone https://github.com/<your-username>/multicloud-devops-project.git
cd multicloud-devops-project

---

# 🔧 STEP 5 — Code Changes Done

## Backend Fix

In backend/index.js:

Changed:

port: process.env.PORT

To:

port: process.env.DB_PORT

---

## Frontend Fix

Changed API URL to EC2 Public IP:

const API_BASE_URL = "http://PUBLIC_IP:84"

---

## Dockerfile Changes

### MySQL Dockerfile

Added credentials:

ENV MYSQL_ROOT_PASSWORD=Cloud122
ENV MYSQL_DATABASE=test
ENV MYSQL_USER=admin
ENV MYSQL_PASSWORD=Devops123

---

### Backend Dockerfile

Added:

ENV DB_HOST=mysql-db
ENV DB_USERNAME=admin
ENV DB_PASSWORD=Devops123
ENV DB_PORT=3306

---

# 🏗 STEP 6 — Build Docker Images

docker build -t mysql-image ./mysql
docker build -t backend-image ./backend
docker build -t frontend-image ./client

Verify:

docker images

---

# 🌐 STEP 7 — Create Docker Network

docker network create appnet

---

# ▶ STEP 8 — Run Containers

### Run MySQL

docker run -d \
--name mysql-db \
--network appnet \
-p 3306:3306 \
mysql-image

---

### Run Backend

docker run -d \
--name backend-app \
--network appnet \
-p 84:80 \
backend-image

---

### Run Frontend

docker run -d \
--name frontend-app \
--network appnet \
-p 80:80 \
frontend-image

---

# ✅ STEP 9 — Test Application

Open browser:

http://PUBLIC_IP

Application successfully connected:

Frontend → Backend → MySQL

---

# ☁️ STEP 10 — Push Images to AWS ECR

## Configure AWS

aws configure

---

## Create ECR Repositories

aws ecr create-repository --repository-name frontend-app
aws ecr create-repository --repository-name backend-app
aws ecr create-repository --repository-name mysql-app

---

## Login to ECR

aws ecr get-login-password --region us-east-1 | \
docker login --username AWS --password-stdin ACCOUNT_ID.dkr.ecr.us-east-1.amazonaws.com

---

## Tag Images

docker tag frontend-image:latest ACCOUNT_ID.dkr.ecr.us-east-1.amazonaws.com/frontend-app:latest
docker tag backend-image:latest ACCOUNT_ID.dkr.ecr.us-east-1.amazonaws.com/backend-app:latest
docker tag mysql-image:latest ACCOUNT_ID.dkr.ecr.us-east-1.amazonaws.com/mysql-app:latest

---

## Push Images

docker push ACCOUNT_ID.dkr.ecr.us-east-1.amazonaws.com/frontend-app:latest
docker push ACCOUNT_ID.dkr.ecr.us-east-1.amazonaws.com/backend-app:latest
docker push ACCOUNT_ID.dkr.ecr.us-east-1.amazonaws.com/mysql-app:latest

---

# ☸️ STEP 11 — Install kubectl

curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.29.0/2024-01-04/bin/linux/amd64/kubectl
chmod +x kubectl
sudo mv kubectl /usr/local/bin/

kubectl version --client

---

# ☸️ STEP 12 — Install eksctl

curl --silent --location \
"https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" \
| tar xz -C /tmp

sudo mv /tmp/eksctl /usr/local/bin

eksctl version

---

# 🔐 STEP 13 — Attach IAM Role to EC2

Attach IAM role with:

AmazonEKSClusterPolicy  
AmazonEKSWorkerNodePolicy  
AmazonEC2ContainerRegistryFullAccess  

---

# ☸️ STEP 14 — Create EKS Cluster

eksctl create cluster \
--name my-cluster \
--region us-east-1 \
--nodegroup-name my-nodes \
--node-type t2.medium \
--nodes 2

Verify:

kubectl get nodes

---

# 🚀 STEP 15 — Deploy Kubernetes YAML Files

kubectl apply -f k8s/mysql-deployment.yml
kubectl apply -f k8s/mysql-service.yml

kubectl apply -f k8s/backend-deployment.yml
kubectl apply -f k8s/backend-service.yml

kubectl apply -f k8s/frontend-deployment.yml
kubectl apply -f k8s/frontend-service.yml

---

# 🔍 Verify Deployment

kubectl get pods
kubectl get svc

---

# 🌍 STEP 16 — Access Application

kubectl get nodes -o wide

Open:

http://NODE_PUBLIC_IP:30080

Application successfully running on Kubernetes 🎉

---

# 🛠 Tech Stack

- Docker
- AWS EC2
- AWS ECR
- AWS EKS
- Kubernetes
- Node.js
- React
- MySQL

---

# 🎯 Conclusion

This project demonstrates complete DevOps lifecycle:

Local Docker → ECR → EKS → Kubernetes Deployment → NodePort Access
