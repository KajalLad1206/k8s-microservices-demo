# Microservices on AWS EKS — Kubernetes + Helm + Jenkins + Terraform

![Kubernetes](https://img.shields.io/badge/Kubernetes-1.28-blue?logo=kubernetes)
![Helm](https://img.shields.io/badge/Helm-3.x-blue?logo=helm)
![Jenkins](https://img.shields.io/badge/CI%2FCD-Jenkins-red?logo=jenkins)
![Terraform](https://img.shields.io/badge/IaC-Terraform-purple?logo=terraform)
![AWS](https://img.shields.io/badge/Cloud-AWS-orange?logo=amazon-aws)

## What this project demonstrates

End-to-end, production-style deployment of a microservices application on AWS EKS, featuring automated CI/CD, versioned Helm releases, Terraform-provisioned infrastructure, and Prometheus/Grafana observability.

**Key result:** deployment time reduced from 30 minutes to under 5 minutes.

## Architecture

```
GitHub → Jenkins CI → Docker Build → AWS ECR → Helm Deploy → AWS EKS
                                                               ↑
                                              Terraform: VPC + EKS + IAM
                                                               ↓
                                              Prometheus + Grafana monitoring
```

## Contents

| Folder | Description |
|---|---|
| `eks-cluster/` | Terraform config for VPC, EKS cluster, node groups, IAM roles |
| `k8s-microservice-yaml/` | Raw Kubernetes manifests (Deployment, Service, Ingress) |
| `microservices-with-helmchart/` | Helm chart for repeatable, versioned releases |
| `mongo-app/` | MongoDB + Mongo Express on Kubernetes |
| `src/` | Application source code for the demo services |

## Quick start

```bash
# 1. Provision infrastructure
cd eks-cluster && terraform init && terraform apply

# 2. Deploy with Helm
helm install myapp ./microservices-with-helmchart/

# 3. Trigger CI/CD pipeline
git push origin main   # Jenkins webhook fires automatically
```

## Technologies

Kubernetes (EKS) · Helm · Jenkins · Docker · Terraform · AWS (VPC / EKS / IAM / ECR) · Prometheus · Grafana · Python · Go

## What I learned

Building this project taught me how to provision cloud infrastructure as code, manage container orchestration at scale, and wire CI/CD pipelines so that a single `git push` results in a fully tested, deployed, and monitored release — with zero manual steps.

# Kubernetes Projects
---
### Microservices Demo — Kubernetes Deployment
[https://github.com/KajalLad1206/k8s-microservices-demo/blob/main/k8s-microservice-yaml/README.md](https://github.com/KajalLad1206/k8s-microservices-demo/tree/main/k8s-microservice-yaml#readme)

### Demo Project: Create Helm Chart for Microservices

[https://github.com/KajalLad1206/k8s-microservices-demo/blob/main/microservices-with-helmchart/README.md](https://github.com/KajalLad1206/k8s-microservices-demo/tree/main/microservices-with-helmchart#readme)

 ### MongoDB & Mongo Express Kubernetes Deployment

[https://github.com/KajalLad1206/k8s-microservices-demo/blob/main/mongo-app/README.md](https://github.com/KajalLad1206/k8s-microservices-demo/tree/main/mongo-app#readme)

### Kubernetes on AWS - EKS cluster with AWS Management Console

https://github.com/KajalLad1206/k8s-microservices-demo/tree/main/k8s-microservice-yaml#readme
