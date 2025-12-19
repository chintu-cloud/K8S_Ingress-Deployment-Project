

# 🚀 Kubernetes Ingress Deployment Project

### (AWS EKS + ECR + NGINX Ingress)

---

## 📌 Overview

This project demonstrates **path-based routing** using **NGINX Ingress Controller** on **AWS EKS**, deploying multiple applications from **Amazon ECR** behind a **single LoadBalancer**.

---

## 🧱 Architecture

* Amazon EKS
* Amazon ECR
* NGINX Ingress Controller
* Docker
* Path-based routing
* Single LoadBalancer

---

## 🔐 Prerequisites

* AWS Account
* EKS Cluster
* EC2 Instance with IAM Role:

  * AmazonEKSClusterPolicy
  * AmazonEKSWorkerNodePolicy
  * AmazonEC2ContainerRegistryFullAccess
* Security Group:

  * Port 80 (HTTP)
  * Port 443 (HTTPS)

---

## 🖥️ Step 1: Login to EC2

```bash
ssh -i key.pem ec2-user@<EC2-PUBLIC-IP>
sudo su -
```

---

## ☸️ Step 2: Install kubectl

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
mv kubectl /usr/local/bin/kubectl
```

---

## 🔑 Step 3: Configure AWS CLI

```bash
aws configure
```

---

## 🔗 Step 4: Connect to EKS Cluster

```bash
aws eks update-kubeconfig --region us-east-1 --name <CLUSTER-NAME>
```

---

## 📦 Step 5: Install Git

```bash
yum install git -y
```

---

## 🐳 Step 6: Install Docker

```bash
yum install docker -y
systemctl start docker
systemctl enable docker
```

---

## 📥 Step 7: Clone GitHub Repository

```bash
git clone https://github.com/chintu-cloud/K8S_Ingress-Deployment-Project.git
cd K8S_Ingress-Deployment-Project
```

---

## 🌐 Step 8: Install NGINX Ingress Controller

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.2.1/deploy/static/provider/cloud/deploy.yaml
```

---

## 🏗️ Step 9: Build Docker Images

### Main Application

```bash
docker build -t main .
```

### AWS Application

```bash
cd aws
docker build -t aws .
cd ..
```

### Azure Application

```bash
cd azure
docker build -t azure .
cd ..
```

### GCP Application

```bash
cd gcp
docker build -t gcp .
cd ..
```

---

## 🗂️ Step 10: Create ECR Repositories

Create the following repositories in **Amazon ECR**:

* main
* aws
* azure
* gcp

---

## 🔐 Step 11: Login to Amazon ECR

```bash
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <AWS-ACCOUNT-ID>.dkr.ecr.us-east-1.amazonaws.com
```

---

## 🚀 Step 12: Tag & Push Images to ECR

### GCP

```bash
docker tag gcp:latest <AWS-ACCOUNT-ID>.dkr.ecr.us-east-1.amazonaws.com/gcp:latest
docker push <AWS-ACCOUNT-ID>.dkr.ecr.us-east-1.amazonaws.com/gcp:latest
```

### Azure

```bash
docker tag azure:latest <AWS-ACCOUNT-ID>.dkr.ecr.us-east-1.amazonaws.com/azure:latest
docker push <AWS-ACCOUNT-ID>.dkr.ecr.us-east-1.amazonaws.com/azure:latest
```

### AWS

```bash
docker tag aws:latest <AWS-ACCOUNT-ID>.dkr.ecr.us-east-1.amazonaws.com/aws:latest
docker push <AWS-ACCOUNT-ID>.dkr.ecr.us-east-1.amazonaws.com/aws:latest
```

### Main

```bash
docker tag main:latest <AWS-ACCOUNT-ID>.dkr.ecr.us-east-1.amazonaws.com/main:latest
docker push <AWS-ACCOUNT-ID>.dkr.ecr.us-east-1.amazonaws.com/main:latest
```

---

## 📝 Step 13: Update Kubernetes Manifests

Update image URLs in the following files:

```text
k8s-files/aws.yml
k8s-files/azure.yml
k8s-files/gcp.yml
k8s-files/main.yml
```

```yaml
image: <AWS-ACCOUNT-ID>.dkr.ecr.us-east-1.amazonaws.com/<IMAGE-NAME>:latest
```

---

## 🔄 Step 14: Pull Latest Changes

```bash
git pull
```

---

## ☸️ Step 15: Deploy to Kubernetes

```bash
cd k8s-files
kubectl apply -f .
```

---

## 🌍 Step 16: Get Application URL

```bash
kubectl get ingress
```

---

## 🔗 Step 17: Access Applications

```text
http://<LOADBALANCER-DNS>/

```

---

## 🌐 Step 18: Configure Route 53 Domain Mapping

### Create DNS Record in Route 53

1. Open **AWS Route 53**
2. Select your **Hosted Zone**
3. Click **Create Record**
4. Configure the record:

   * **Record name**: `chintu.shop`
   * **Record type**: `A`
   * **Alias**: Enabled
   * **Route traffic to**:

     * **Alias to Application and Classic Load Balancer**
   * **Region**: `us-east-1`
   * **Load Balancer**: Select your Ingress LoadBalancer DNS
5. Click **Create Record**

---

## 🌍 Step 19: Access Application Using Domain

Open a browser and access:

```text
http://chintu.shop

```

---

## 🏁 Final Result

* Custom domain mapped using Route 53
* Ingress LoadBalancer exposed via DNS
* Path-based routing enabled for multiple applications
* Production-ready Kubernetes Ingress setup



---


## 📁 Step 1: Create ArgoCD Namespace

```bash
kubectl create namespace argocd
```

**Output**

```text
namespace/argocd created
```

---

## ⚙️ Step 2: Install ArgoCD

```bash
kubectl apply -n argocd -f \
https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

**Output (partial)**

```text
customresourcedefinition.apiextensions.k8s.io/applications.argoproj.io created
deployment.apps/argocd-server created
statefulset.apps/argocd-application-controller created
service/argocd-server created
```

---

## 🔍 Step 3: Verify ArgoCD Components

```bash
kubectl get all -n argocd
```

**Output**

```text
pod/argocd-server-9dc66fd74-f884l       1/1   Running
pod/argocd-repo-server-6c857c79ff       1/1   Running
pod/argocd-application-controller-0     1/1   Running
```

---

## 🌐 Step 4: Expose ArgoCD Using NodePort

```bash
kubectl edit svc argocd-server -n argocd
```

Change:

```yaml
spec:
  type: NodePort
```

---

## 🔢 Step 5: Get NodePort

```bash
kubectl get svc -n argocd
```

**Output**

```text
argocd-server   NodePort   10.100.101.35   80:30829/TCP,443:30915/TCP
```

---

## 🖥️ Step 6: Get Node Public IP

```bash
kubectl get nodes -o wide
```

**Output**

```text
ip-172-31-16-18.ec2.internal   Ready   54.147.131.253
```

---

## 🔐 Step 7: Access ArgoCD UI

Open browser:

```text
http://54.147.131.253:30586
```

➡ **Advanced → Continue (unsafe)**


---

## 🔑 Step 8: Get Admin Password

```bash
kubectl get secret argocd-initial-admin-secret \
-n argocd -o yaml
```

**Output**

```yaml
password: RHp2Vjk2dEZLSVVxUGN0LQ==
```

Decode:

```bash
echo "RHp2Vjk2dEZLSVVxUGN0LQ==" | base64 --decode
```

**Output**

```text
DzvV96tFKIUqPct-
```

---

## 🔓 Step 9: Login to ArgoCD

| Field    | Value              |
| -------- | ------------------ |
| Username | `admin`            |
| Password | `4U6LqCxkwlVT2UvN` |

---
<img width="1910" height="958" alt="Screenshot 2025-12-18 140949" src="https://github.com/user-attachments/assets/b50e6a2f-ba31-4157-8d75-3f1d18b6476e" />

## 📦 Step 10: Create Application Namespace

```bash
kubectl create namespace cloud
```

**Output**

```text
namespace/cloud created
```

---

## 🆕 Step 11: Create Application in ArgoCD

Click **➕ New App**

### 🔹 General

```text
App Name: path-based-routing
Project: default
Sync Policy: Automatic
```


### 🔹 Source

```text
Repo URL: https://github.com/chintu-cloud/K8S_Ingress-Deployment-Project.git
Revision: HEAD
Path: k8s-files
```

### 🔹 Destination

```text
Cluster: https://kubernetes.default.svc
Namespace: default
```

Click **Create**

---

## 🏁 Total Applications



---

