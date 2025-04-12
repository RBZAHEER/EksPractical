# 🚀 Kubernetes Deployment on Amazon EKS | Portfolio Website Hosting

This project demonstrates how I deployed my website on **AWS EKS** using **kubectl**, **IAM roles**, **Amazon EC2**, and **LoadBalancer services**, making it publicly accessible from anywhere 🌐

---

## 🛠️ Tech Stack

- AWS EKS (Elastic Kubernetes Service)
- AWS EC2
- kubectl
- Docker
- NGINX
- GitHub
- Linux (Amazon Linux 2)
- CI/CD Pipeline (CodePipeline / Manual using kubectl)

---

## 📦 Steps to Deploy Website on AWS EKS

### 1. ✅ Launch EC2 and Install Tools

```bash
sudo yum update -y
sudo yum install -y git docker
sudo service docker start
```

- Install `kubectl`, `eksctl`, and AWS CLI
- Configure AWS credentials using `aws configure`

---

### 2. 🛠️ Create EKS Cluster with `eksctl`

```bash
eksctl create cluster --name zaheer-cluster --region ap-south-1 --nodegroup-name zaheer-nodes --node-type t3.medium --nodes 2
```

---

### 3. 🔐 Configure IAM Roles and Permissions

- Attach necessary permissions to root and `test-user`
- Add user to `aws-auth` ConfigMap:

```bash
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "eks:ListFargateProfiles",
                "eks:DescribeNodegroup",
                "eks:ListNodegroups",
                "eks:ListUpdates",
                "eks:AccessKubernetesApi",
                "eks:ListAddons",
                "eks:DescribeCluster",
                "eks:DescribeAddonVersions",
                "eks:ListClusters",
                "eks:ListIdentityProviderConfigs",
                "iam:ListRoles"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "ssm:GetParameter",
            "Resource": "arn:aws:ssm:*:111122223333:parameter/*"
        }
    ]
}
```

- Apply EKS Console access manifest:

```bash
kubectl apply -f https://s3.us-west-2.amazonaws.com/amazon-eks/docs/eks-console-full-access.yaml
```

---

### 4. 📦 Dockerize Website and Push to DockerHub

```bash
docker build -t zaheer-portfolio .
docker tag zaheer-portfolio zaheerhub/portfolio:latest
docker push zaheerhub/portfolio:latest
```

---

### 5. 🚢 Deploy to Kubernetes

- Create Deployment and Service YAML files:
```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: portfolio-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: portfolio
  template:
    metadata:
      labels:
        app: portfolio
    spec:
      containers:
      - name: portfolio
        image: zaheerhub/portfolio:latest
        ports:
        - containerPort: 80
```

```yaml
# service.yaml
apiVersion: v1
kind: Service
metadata:
  name: portfolio-service
spec:
  type: LoadBalancer
  selector:
    app: portfolio
  ports:
  - port: 80
    targetPort: 80
```

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

---

### 6. 🌐 Access Website

- Get the LoadBalancer external URL:

```bash
kubectl get svc portfolio-service
```

- Open the external URL in your browser. Your website is now live from **any machine** with internet access!

---

## ✅ Final Output

- Website hosted on **EKS**
- Live LoadBalancer IP works across all devices
- Nodes and pods visible in AWS Console
- IAM permissions properly configured for both GUI and CLI access

---

## 🔗 Follow Me

- 🔗 [LinkedIn – Zaheer Mulani](https://www.linkedin.com/in/defo_notzaheer)
- 💻 [Portfolio](https://zaheerportfoli.netlify.app/)
