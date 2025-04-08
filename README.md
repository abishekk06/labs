# GCP GKE Application Setup with GitOps

This repository contains Terraform configurations and Kubernetes manifests for deploying a sample application on Google Cloud Platform using GKE and GitOps practices.

## Prerequisites

- Google Cloud SDK installed and configured
- Terraform installed (version >= 1.0)
- kubectl installed
- Docker installed
- A GitHub repository for GitOps

## Project Structure

```
.
├── terraform/
│   ├── modules/
│   │   ├── gke/
│   │   └── network/
│   └── environments/
│       └── dev/
├── k8s-manifests/
│   ├── namespace.yaml
│   ├── backend.yaml
│   ├── frontend.yaml
│   └── argocd-app.yaml
└── docker/
    ├── frontend/
    └── backend/
```

## Setup Instructions

1. Initialize Terraform:
```bash
cd terraform/environments/dev
terraform init
```

2. Create a terraform.tfvars file with your GCP project details:
```hcl
project_id = "your-project-id"
region     = "us-central1"
```

3. Apply Terraform configuration:
```bash
terraform apply
```

4. Build and push Docker images:
```bash
# Build frontend
cd docker/frontend
docker build -t gcr.io/PROJECT_ID/frontend:latest .
docker push gcr.io/PROJECT_ID/frontend:latest

# Build backend
cd ../backend
docker build -t gcr.io/PROJECT_ID/backend:latest .
docker push gcr.io/PROJECT_ID/backend:latest
```

5. Install ArgoCD:
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

6. Update the ArgoCD application manifest (k8s-manifests/argocd-app.yaml) with your GitHub repository details.

7. Apply Kubernetes manifests:
```bash
kubectl apply -f k8s-manifests/
```

## Accessing the Application

1. Get the external IP of the frontend service:
```bash
kubectl get svc -n demo-app frontend
```

2. Access the application using the external IP in your browser.

## GitOps Workflow

1. Make changes to your application code
2. Build and push new Docker images
3. Update the image tags in Kubernetes manifests
4. Commit and push changes to GitHub
5. ArgoCD will automatically detect changes and apply them to the cluster

## Cleanup

To destroy the infrastructure:
```bash
cd terraform/environments/dev
terraform destroy
``` 