

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

**Output**

```text
Last login: Fri Dec 19 15:30:21 2025
[root@ip-172-31-xx-xx ~]#
```

---

## ☸️ Step 2: Install kubectl

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
mv kubectl /usr/local/bin/kubectl
```

**Output**

```text
kubectl downloaded successfully
```

---

## 🔑 Step 3: Configure AWS CLI

```bash
aws configure
```

**Output**

```text
AWS Access Key ID [None]: ************
AWS Secret Access Key [None]: ************
Default region name [None]: us-east-1
Default output format [None]:
```

---

## 🔗 Step 4: Connect to EKS Cluster

```bash
aws eks update-kubeconfig --region us-east-1 --name <CLUSTER-NAME>
```

**Output**

```text
Added new context arn:aws:eks:us-east-1:<ACCOUNT-ID>:cluster/<CLUSTER-NAME>
```

---

## 📦 Step 5: Install Git

```bash
yum install git -y
```

**Output**

```text
Installed:
  git.x86_64
Complete!
```

---

## 🐳 Step 6: Install Docker

```bash
yum install docker -y
systemctl start docker
systemctl enable docker
```

**Output**

```text
Docker installed successfully
Created symlink from docker.service
```

---

## 📥 Step 7: Clone GitHub Repository

```bash
git clone https://github.com/chintu-cloud/K8S_Ingress-Deployment-Project.git
cd K8S_Ingress-Deployment-Project
```

**Output**

```text
Cloning into 'K8S_Ingress-Deployment-Project'...
done.
```

---

## 🌐 Step 8: Install NGINX Ingress Controller

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.2.1/deploy/static/provider/cloud/deploy.yaml
```

**Output**

```text
namespace/ingress-nginx created
deployment.apps/ingress-nginx-controller created
service/ingress-nginx-controller created
```

---

## 🏗️ Step 9: Build Docker Images

### Main Application

```bash
docker build -t main .
```

**Output**

```text
Successfully built <IMAGE-ID>
Successfully tagged main:latest
```

---

### AWS Application

```bash
cd aws
docker build -t aws .
cd ..
```

**Output**

```text
Successfully built <IMAGE-ID>
Successfully tagged aws:latest
```

---

### Azure Application

```bash
cd azure
docker build -t azure .
cd ..
```

**Output**

```text
Successfully built <IMAGE-ID>
Successfully tagged azure:latest
```

---

### GCP Application

```bash
cd gcp
docker build -t gcp .
cd ..
```

**Output**

```text
Successfully built <IMAGE-ID>
Successfully tagged gcp:latest
```

---

## 🗂️ Step 10: Create ECR Repositories

Create the following repositories in **Amazon ECR**:

* main
* aws
* azure
* gcp

**Output**

```text
Repositories created successfully
```
<img width="1558" height="269" alt="image" src="https://github.com/user-attachments/assets/711fb793-ed60-40cf-be02-44cac8638e3b" />

---

## 🔐 Step 11: Login to Amazon ECR

```bash
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <AWS-ACCOUNT-ID>.dkr.ecr.us-east-1.amazonaws.com
```

**Output**

```text
Login Succeeded
```

---

## 🚀 Step 12: Tag & Push Images to ECR

### GCP

```bash
docker tag gcp:latest <AWS-ACCOUNT-ID>.dkr.ecr.us-east-1.amazonaws.com/gcp:latest
docker push <AWS-ACCOUNT-ID>.dkr.ecr.us-east-1.amazonaws.com/gcp:latest
```

**Output**

```text
Pushed: gcp:latest
```

---

### Azure

```bash
docker tag azure:latest <AWS-ACCOUNT-ID>.dkr.ecr.us-east-1.amazonaws.com/azure:latest
docker push <AWS-ACCOUNT-ID>.dkr.ecr.us-east-1.amazonaws.com/azure:latest
```

**Output**

```text
Pushed: azure:latest
```

---

### AWS

```bash
docker tag aws:latest <AWS-ACCOUNT-ID>.dkr.ecr.us-east-1.amazonaws.com/aws:latest
docker push <AWS-ACCOUNT-ID>.dkr.ecr.us-east-1.amazonaws.com/aws:latest
```

**Output**

```text
Pushed: aws:latest
```

---

### Main

```bash
docker tag main:latest <AWS-ACCOUNT-ID>.dkr.ecr.us-east-1.amazonaws.com/main:latest
docker push <AWS-ACCOUNT-ID>.dkr.ecr.us-east-1.amazonaws.com/main:latest
```

**Output**

```text
Pushed: main:latest
```

---

## 📝 Step 13: Update Kubernetes Manifests

Update image URLs in:

```text
k8s-files/aws.yml
k8s-files/azure.yml
k8s-files/gcp.yml
k8s-files/main.yml
```

```yaml
image: <AWS-ACCOUNT-ID>.dkr.ecr.us-east-1.amazonaws.com/<IMAGE-NAME>:latest
```

**Output**

```text
Manifests updated successfully
```

---

## 🔄 Step 14: Pull Latest Changes

```bash
git pull
```

**Output**

```text
Updating files...
Fast-forward
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

<img width="1920" height="1080" alt="Screenshot (512)" src="https://github.com/user-attachments/assets/94185ced-2e18-47e4-a5b4-e68f252bebce" />

```text
http://<LOADBALANCER-DNS>/aws
http://<LOADBALANCER-DNS>/azure
http://<LOADBALANCER-DNS>/gcp

```
<img width="1920" height="1080" alt="Screenshot (513)" src="https://github.com/user-attachments/assets/7d87e3cd-69ae-47f1-a8e6-62515c054d21" />
<img width="1920" height="1080" alt="Screenshot (514)" src="https://github.com/user-attachments/assets/8f8e3218-6706-4a79-a375-251e16b30c3a" />
<img width="1920" height="1080" alt="Screenshot (515)" src="https://github.com/user-attachments/assets/30906816-86c2-47b3-aec6-2a289f885261" />

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
<img width="1533" height="626" alt="Screenshot (516)" src="https://github.com/user-attachments/assets/44bdac2c-9163-432d-ab3d-eee23c7253f2" />

## 🌍 Step 19: Access Application Using Domain

Open a browser and access:

```text
http://chintu.shop

```
<img width="1920" height="1080" alt="Screenshot (517)" src="https://github.com/user-attachments/assets/ee4c9c35-41fb-49ac-9b25-11b516888656" />

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
<img width="1427" height="407" alt="Screenshot 2025-12-19 215824" src="https://github.com/user-attachments/assets/cdf15c81-86b0-4c1b-97cf-a8b6a6b61072" />

### 🔹 Destination

```text
Cluster: https://kubernetes.default.svc
Namespace: default
```
<img width="1434" height="371" alt="Screenshot 2025-12-19 215914" src="https://github.com/user-attachments/assets/da4a8018-e2e7-410b-9caa-4f8baf4e8f60" />

Click **Create**

---

## 🏁 Total Applications
<img width="1920" height="1080" alt="Screenshot (518)" src="https://github.com/user-attachments/assets/8530fed8-472b-48b4-acfe-9188cb4d54ae" />
<img width="1915" height="952" alt="Screenshot 2025-12-19 220035" src="https://github.com/user-attachments/assets/103df537-5f6d-42c8-b7ca-4abfc9eba534" />
<img width="1910" height="933" alt="Screenshot 2025-12-19 220103" src="https://github.com/user-attachments/assets/f1bafdf8-d34b-4e69-bba5-f8db89ee6342" />



---

