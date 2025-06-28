# AWS EKS Cluster Configuration, Setup & App Deployment

This documentation provides detailed instructions for setting up and configuring an Amazon Elastic Kubernetes Service (EKS) cluster in the `ap-south-1` region.

The steps include installing AWS CLI, setting up the EKS cluster using Terraform, updating kubeconfig, installing `kubectl` and `eksctl`, associating IAM OIDC provider, creating a service account, deploying the AWS EBS CSI Driver, and applying a custom manifest file.

---

## Step 1: Install AWS CLI

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt install unzip
unzip awscliv2.zip
sudo ./aws/install
aws configure
```

---

## Step 2: Change Directory to Terraform-Code and Deploy EKS

```bash
cd <terraform_project_folder>  # Replace with your Terraform project folder name
terraform init
terraform apply --auto-approve
```

---

## Step 3: Update kubeconfig for the EKS Cluster

```bash
aws eks --region ap-south-1 update-kubeconfig --name kirana-cluster
```

This retrieves the kubeconfig and stores it in the default location (e.g., `~/.kube/config`).

---

## Step 4: Install kubectl

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
kubectl version --client
```

---

## Step 5: Install eksctl

```bash
curl -LO "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_Linux_amd64.tar.gz"
tar -xzf eksctl_Linux_amd64.tar.gz
sudo mv eksctl /usr/local/bin
eksctl version
```

---

## Step 6: Associate IAM OIDC Provider

```bash
eksctl utils associate-iam-oidc-provider --region ap-south-1 --cluster kirana-cluster --approve
```

This enables IAM roles for Kubernetes service accounts.

---

## Step 7: Create IAM Service Account

```bash
eksctl create iamserviceaccount \
  --region ap-south-1 \
  --name ebs-csi-controller-sa \
  --namespace kube-system \
  --cluster kirana-cluster \
  --attach-policy-arn arn:aws:iam::aws:policy/service-role/AmazonEBSCSIDriverPolicy \
  --approve \
  --override-existing-serviceaccounts
```

### Flags Explained:
- `--name`: Name of the service account
- `--namespace`: Namespace to create the account in (e.g., `kube-system`)
- `--attach-policy-arn`: IAM policy ARN for EBS CSI Driver
- `--approve`: Automatically approve creation
- `--override-existing-serviceaccounts`: Replace if already present

---

## Step 8: Deploy the AWS EBS CSI Driver

```bash
kubectl apply -k "github.com/kubernetes-sigs/aws-ebs-csi-driver/deploy/kubernetes/overlays/stable/ecr/?ref=release-1.11"
```

---

## Step 9: Deploy Application Using Manifest File

```bash
kubectl apply -f manifest.yaml
```

> Replace `manifest.yaml` with the path to your actual Kubernetes manifest file.

---

## ✅ Verification

```bash
# Verify kubeconfig and cluster access
kubectl get nodes

# Check if the service account is created
kubectl get serviceaccount ebs-csi-controller-sa -n kube-system

# Check if the EBS CSI Driver is deployed
kubectl get pods -n kube-system -l app.kubernetes.io/name=aws-ebs-csi-driver

# Check if your application is deployed
kubectl get pods
```
