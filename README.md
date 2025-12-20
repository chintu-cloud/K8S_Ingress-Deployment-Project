## 🧱 Architecture
<img width="1222" height="498" alt="image" src="https://github.com/user-attachments/assets/95715877-6b33-40c2-b36a-62e9dc73ce6d" />


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



## ✨ Highlights

✅ Single LoadBalancer
✅ Multiple Applications
✅ Path-Based Routing
✅ Docker → ECR → EKS
✅ Production-style Kubernetes setup

---

## 🏗️ Architecture

```
🌍 User Browser
      |
      v
🧭 NGINX Ingress Controller (AWS ELB)
      |
      ├── /        → Main App
      ├── /aws     → AWS App
      ├── /azure   → Azure App
      └── /gcp     → GCP App
```

---

## 🧰 Tech Stack

| Tool          | Purpose               |
| ------------- | --------------------- |
| AWS EC2       | Management Host       |
| AWS EKS       | Kubernetes Cluster    |
| AWS ECR       | Docker Image Registry |
| Docker        | Containerization      |
| Kubernetes    | Orchestration         |
| NGINX Ingress | Traffic Routing       |
| GitHub        | Source Control        |

---

## 📁 Project Structure

```
K8S_Ingress-Deployment-Project/
│
├── Dockerfile
├── index.html
├── aws/
│   ├── Dockerfile
│   └── index.html
├── azure/
│   ├── Dockerfile
│   └── index.html
├── gcp/
│   ├── Dockerfile
│   └── index.html
├── k8s-files/
│   ├── aws.yml
│   ├── azure.yml
│   ├── gcp.yml
│   ├── main.yml
│   └── ingress.yml
└── README.md
```

---

# 🚀 COMPLETE STEP-BY-STEP IMPLEMENTATION

---

## 🔹 Step 1: Login to EC2

```bash
sudo su -
```

---

## 🔹 Step 2: Install kubectl

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
mv kubectl /usr/local/bin/kubectl
kubectl version --client
```

### ✅ Output

```
Client Version: v1.35.0
Kustomize Version: v5.7.1
```

---

## 🔹 Step 3: Configure AWS CLI

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

## 🔹 Step 4: Connect EC2 to EKS Cluster

```bash
aws eks update-kubeconfig --region us-east-1 --name cloud
```

### ✅ Output

```
Added new context arn:aws:eks:us-east-1:xxxx:cluster/cloud
```

## 🔹 Step 4 : get nodes

```bash
kubectl get nodes
```

### ✅ Output

```
NAME                             STATUS   AGE   VERSION
ip-172-31-26-89.ec2.internal     Ready    3m    v1.34.x
ip-172-31-5-50.ec2.internal      Ready    3m    v1.34.x
```

---

## 🔹 Step 6: Install Git & Docker

## 🔹 Step 6.1: Install Git

📌 Git is used to clone the project repository from GitHub.

```bash
yum install git -y
```

### ✅ Output

```
Installed:
  git-2.xx.x.x86_64
Complete!
```

✔ Git installed successfully

🔍 Verify:

```bash
git --version
```

Output:

```
git version 2.xx.x
```

---

## 🔹 Step 6.2: Install Docker

📌 Docker is used to build container images for Kubernetes.

```bash
yum install docker -y
```

### ✅ Output

```
Installed:
  docker-25.xx.x
Complete!
```

✔ Docker package installed

🔍 Verify installation:

```bash
docker --version
```

Output:

```
Docker version 25.xx.x, build xxxx
```

---

## 🔹 Step 6.3: Start Docker Service

📌 Docker daemon must be running to build and push images.

```bash
systemctl start docker
```

✔ Docker service started

(Optional but recommended – enable Docker on boot)

```bash
systemctl enable docker
```

---

## 🔹 Step 6.4: Check Docker Service Status

📌 Confirm Docker is running correctly.

```bash
systemctl status docker
```

### ✅ Output

```
● docker.service - Docker Application Container Engine
   Active: active (running)
```

✔ Docker is running successfully

---


## 🔹 Step 7: Clone GitHub Repository

## 🔹 Step 7.1: Clone the GitHub Repository

📌 This command copies the complete project from GitHub to your local machine.

```bash
git clone https://github.com/chintu-cloud/K8S_Ingress-Deployment-Project.git
```

### ✅ Output

```
Cloning into 'K8S_Ingress-Deployment-Project'...
remote: Enumerating objects: 90, done.
remote: Counting objects: 100% (90/90), done.
remote: Compressing objects: 100% (86/86), done.
Receiving objects: 100% (90/90), done.
Resolving deltas: 100% (41/41), done.
```

✔ Repository cloned successfully

📁 A new folder named **`K8S_Ingress-Deployment-Project`** is created.

---

## 🔹 Step 7.2: Move Into Project Directory

📌 Navigate inside the cloned repository to access project files.

```bash
cd K8S_Ingress-Deployment-Project
```

✔ You are now inside the project directory

🔍 Verify:

```bash
ls
```

### ✅ Output

```
Dockerfile
Monolithic
aws
azure
gcp
index.html
k8s-files
```

---

## 🔹 Step 8: Install NGINX Ingress Controller

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.2.1/deploy/static/provider/cloud/deploy.yaml
```

### ✅ Output

```
namespace/ingress-nginx created
deployment.apps/ingress-nginx-controller created
ingressclass.networking.k8s.io/nginx created
```

---

## 🔹 Step 9: Build Docker Images

## 🔹 Step 9.1: Build Main Application Image

📍 **Location:** Project root directory

```bash
cd K8S_Ingress-Deployment-Project
docker build -t main .
```

### ✅ Output

```
[+] Building 2.0s
 => naming to docker.io/library/main
```

✔ Image `main:latest` created successfully

---

## 🔹 Step 9.2: Build AWS Application Image

📍 **Move to aws directory**

```bash
cd aws
docker build -t aws .
```

### ✅ Output

```
[+] Building 0.3s
 => naming to docker.io/library/aws
```

✔ Image `aws:latest` created successfully

📍 **Go back to root directory**

```bash
cd ..
```

---

## 🔹 Step 9.3: Build Azure Application Image

📍 **Move to azure directory**

```bash
cd azure
docker build -t azure .
```

### ✅ Output

```
[+] Building 0.3s
 => naming to docker.io/library/azure
```

✔ Image `azure:latest` created successfully

📍 **Go back to root directory**

```bash
cd ..
```

---

## 🔹 Step 9.4: Build GCP Application Image

📍 **Move to gcp directory**

```bash
cd gcp
docker build -t gcp .
```

### ✅ Output

```
[+] Building 0.3s
 => naming to docker.io/library/gcp
```

✔ Image `gcp:latest` created successfully

📍 **Return to root directory**

```bash
cd ..
```

---

## 🔍 Step 5: Verify Docker Images

```bash
docker images
```

### ✅ Output

```
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
main         latest    af4c45efb7bb    1 min ago      23MB
aws          latest    abf93e07cfdb    1 min ago      23MB
azure        latest    c5d0b98fc49b    1 min ago      23MB
gcp          latest    25bfaab44be8    1 min ago      23MB
```

---


## 🔹 Step 10: Create AWS ECR Repositories

Create **4 repositories** in AWS ECR:

* `main`
* `aws`
* `azure`
* `gcp`

---
<img width="1558" height="269" alt="image" src="https://github.com/user-attachments/assets/711fb793-ed60-40cf-be02-44cac8638e3b" />

## 🔹 Step 11: Login to AWS ECR

```bash
aws ecr get-login-password --region us-east-1 \
| docker login --username AWS --password-stdin <ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com
```

### ✅ Output

```
Login Succeeded
```

---

## 🔹 Step 12: Push Images to ECR

```bash
docker tag main:latest <ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/main:latest
docker push <ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/main:latest
```

(Same steps for `aws`, `azure`, `gcp`)

### ✅ Output

```
latest: digest: sha256:xxxx size: 2197
```

---

## 🔹 Step 13: Update Kubernetes YAML Files

Update image paths in:

```
k8s-files/aws.yml
k8s-files/azure.yml
k8s-files/gcp.yml
k8s-files/main.yml
```

```yaml
image: <ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/aws:latest
```

---

## 🔹 Step 14: Pull Latest Changes

```bash
git pull
```

### ✅ Output

```
4 files changed, 4 insertions(+), 4 deletions(-)
```

---

## 🔹 Step 14: Deploy to Kubernetes
# k8s-files/ inside 

```bash
kubectl apply -f .
```

### ✅ Output

```
deployment.apps/aws-deployment created
service/aws-service created
deployment.apps/azure-deployment created
service/azure-service created
deployment.apps/gcp-deployment created
service/gcp-service created
deployment.apps/main-deployment created
service/main-service created
ingress.networking.k8s.io/k8s-ingress created
```

---

# 🔍 VERIFICATION & OUTPUTS

## Pods

```bash
kubectl get pods
```

```
aws-deployment-xxx     Running
azure-deployment-xxx   Running
gcp-deployment-xxx     Running
main-deployment-xxx    Running
```

---

## Services

```bash
kubectl get svc
```

```
aws-service      ClusterIP
azure-service    ClusterIP
gcp-service      ClusterIP
main-service     ClusterIP
```

---

## Ingress

```bash
kubectl get ingress
```

```
NAME          CLASS   ADDRESS                                   PORTS
k8s-ingress   nginx   a1b2c3d4.elb.amazonaws.com                80
```

---



## 🧑‍💻 Author

**Chintu Cloud**
🔗 [https://github.com/chintu-cloud](https://github.com/chintu-cloud)

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
argocd-server   NodePort   10.100.101.35   80:30586/TCP,443:30915/TCP
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
4U6LqCxkwlVT2UvN
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

