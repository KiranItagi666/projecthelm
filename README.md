# 🚀 BankApp Helm Deployment on AWS EKS

This repository demonstrates how to deploy a containerized Java Spring Boot application (`BankApp`) with a MySQL database using **Helm** on **Amazon EKS**.

---

## 🧰 Tech Stack

- **AWS EKS** – Managed Kubernetes cluster
- **Helm 3** – Kubernetes package manager
- **MySQL 8** – Database backend
- **Spring Boot** – Java web application
- **AWS EBS (gp3)** – Persistent storage for MySQL

---

## 📦 Project Structure

```
bankapp-chart/
├── Chart.yaml
├── values.yaml
└── templates/
    ├── secrets.yaml
    ├── configmap.yaml
    ├── storageclass.yaml
    ├── pvc.yaml
    ├── mysql-deployment.yaml
    ├── mysql-service.yaml
    ├── bankapp-deployment.yaml
    └── bankapp-service.yaml
```

---

## ⚙️ Setup Summary

1. **Install Helm** on your local system:

   ```bash
   curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
   ```

2. **Create and customize** Helm chart:

   ```bash
   helm create bankapp-chart
   ```

3. **Define values** in `values.yaml` to parameterize your deployment.

4. **Install EBS CSI Driver** to enable dynamic volume provisioning:

   ```bash
   kubectl apply -k "github.com/kubernetes-sigs/aws-ebs-csi-driver/deploy/kubernetes/overlays/stable/ecr/?ref=release-1.11"
   ```

5. **Deploy the application** using Helm:

   ```bash
   helm install bankapp ./bankapp-chart
   ```

6. **Verify deployment:**

   ```bash
   kubectl get all
   ```

---

## 📊 Architecture Diagram

Below is a simplified diagram of the BankApp Helm deployment on EKS:

```
            +---------------------+
            |     AWS EKS         |
            |   (Kubernetes)      |
            +---------+-----------+
                      |
              +-------+--------+
              |     Helm 3     |
              +-------+--------+
                      |
          +-----------+------------+
          |                        |
+-------------------+   +-----------------------+
|  MySQL Deployment |   |  BankApp Deployment   |
|   + PVC (EBS)     |   |  + LoadBalancer SVC   |
+-------------------+   +-----------------------+
```

---

## ✅ Outcome

- **Reusable Helm chart** for BankApp and MySQL
- **Production-grade configuration** with resource limits and persistent volumes
- Seamless deployment on AWS EKS

---
Refer bankapp-project for the helm chart, template and values files for implementation

