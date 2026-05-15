# ♾️ Flux CD GitOps Pipeline

<div align="center">

![GitOps](https://img.shields.io/badge/GitOps-Principles-blue?style=for-the-badge)
![Flux CD](https://img.shields.io/badge/Flux%20CD-v2-4A154B?style=for-the-badge&logo=flux&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)
![Kustomize](https://img.shields.io/badge/Kustomize-Built--in-0B3D91?style=for-the-badge)

**A professional implementation of GitOps methodologies using Flux CD to automate, manage, and continuously deploy Kubernetes infrastructure and applications.**

[🏗️ Architecture](#-gitops-architecture) • [⚙️ Core Concepts](#-core-concepts-demonstrated) • [🚀 Setup Guide](#-quick-start) • [🗂️ Project Structure](#-project-structure)

</div>

---

## 📌 Overview

This project demonstrates the transition from traditional, manual `kubectl apply` deployment methods to a fully automated **GitOps workflow**. 

By leveraging **Flux CD**, this repository serves as the single source of truth for the desired state of our Kubernetes cluster. Any changes merged into the main branch are automatically detected by Flux, reconciled, and applied to the cluster, ensuring that the live environment always matches the Git repository.

### 🌟 Why GitOps?
- **Auditable & Reversible:** Every change to the infrastructure is a Git commit. Rollbacks are as simple as `git revert`.
- **Drift Detection:** If someone manually edits a resource in the cluster, Flux automatically detects the drift and reverts it to the state defined in Git.
- **Security:** CI/CD tools no longer need cluster credentials; Flux runs inside the cluster and pulls changes securely.

---

## 🏗️ GitOps Architecture

```text
┌─────────────────┐        ┌──────────────────────────────────────────────────┐
│   Developer     │        │               Kubernetes Cluster                 │
│                 │        │                                                  │
│  1. Commits     │        │  ┌─────────────────┐       ┌──────────────────┐  │
│  manifests      │        │  │  Source         │       │  Kustomization   │  │
│  to Git         │        │  │  Controller     │       │  Controller      │  │
└──────┬──────────┘        │  └───────┬─────────┘       └─────────┬────────┘  │
       │                   │          │                           │           │
       │                   │          │ 3. Polls Git repository   │           │
┌──────▼──────────┐        │          ▼                           │           │
│                 │◄───────┤  ┌─────────────────┐                 │           │
│  Git Repository │        │  │  GitRepository  │◄────────────────┘           │
│  (GitHub)       │        │  │  Resource       │  4. Reconciles state &      │
│                 │        │  └─────────────────┘     applies changes         │
└─────────────────┘        │                                      │           │
    2. Single Source       │  ┌───────────────────────────────────▼────────┐  │
       of Truth            │  │        Deployed Applications (Nginx)       │  │
                           │  └────────────────────────────────────────────┘  │
                           └──────────────────────────────────────────────────┘
```

---

## ⚙️ Core Concepts Demonstrated

- **Continuous Reconciliation:** Flux actively polls the repository and applies the declared state.
- **Kustomize Overlays:** Base configuration for standard environments, with overlays tailored for `production` (e.g., scaling up replicas).
- **Drift Correction:** Hands-on demonstration of self-healing infrastructure. If a deployment is manually deleted, Flux reinstates it immediately.
- **Declarative Infrastructure:** Everything, from the app to the Flux tracking configuration (`GitRepository` and `Kustomization`), is defined as code.

---

## 🗂️ Project Structure

The repository uses the standard Flux directory structure:

```text
FLUX-CD-Devops/
├── apps/
│   ├── base/                  # Base Kubernetes manifests
│   │   ├── deployment.yaml    # Nginx Deployment (default 1 replica)
│   │   ├── service.yaml       # NodePort service on port 30080
│   │   └── kustomization.yaml # Kustomize base compiler
│   └── production/            # Production overlay 
│       └── kustomization.yaml # Overrides base (scales to 3 replicas)
│
├── clusters/
│   └── my-cluster/            # Cluster-specific Flux configurations
│       └── webapp-kustomization.yaml # Tells Flux how to apply apps/production
│
└── infrastructure/
    └── sources/               # Source tracking configuration
        └── gitrepository.yaml # Points Flux to this GitHub repository
```

---

## 🚀 Quick Start

### Prerequisites
- A running Kubernetes cluster (e.g., Minikube, kind, or EKS)
- `kubectl` installed and configured
- `flux` CLI installed

### 1. Install Flux on your Cluster
If Flux isn't already installed, bootstrap it using the CLI. *Note: Replace with your GitHub token and username.*

```bash
export GITHUB_TOKEN=<your-token>
export GITHUB_USER=<your-username>

flux bootstrap github \
  --owner=$GITHUB_USER \
  --repository=FLUX-CD-Devops \
  --branch=main \
  --path=./clusters/my-cluster \
  --personal
```

### 2. Verify Flux Components
Ensure all Flux controllers are running correctly:
```bash
kubectl get pods -n flux-system
```

### 3. Verify the Application Deployment
Flux will automatically clone the repository and apply the manifests in `clusters/my-cluster`, which in turn applies `apps/production`.

```bash
# Check the deployment (should show 3 replicas due to the production overlay)
kubectl get deployments -n default

# Check the NodePort service
kubectl get svc -n default
```

### 4. Test Drift Detection (Self-Healing)
You can test the GitOps automated reconciliation by intentionally breaking the cluster:
```bash
# Manually scale down the deployment
kubectl scale deployment webapp --replicas=1

# Wait a minute, then check again. Flux will correct the drift!
kubectl get deployments
```

---

## 🛠️ Tech Stack

| Layer | Technology | Role |
|-------|-----------|------|
| **Version Control** | GitHub | Single Source of Truth |
| **GitOps Operator** | Flux CD (v2) | Continuous deployment & reconciliation |
| **Configuration** | Kustomize | Template-free manifest management (base/overlays) |
| **Orchestration** | Kubernetes | Container management & scaling |
| **Workload** | Nginx | Demo web application |

---

## 👨‍💻 Author

**Ahtesham Shah**
- 🌐 [GitHub](https://github.com/Ahtesham-Shah999)
- 💼 Cloud-Native DevOps & Software Engineering

---

<div align="center">
  <i>⭐ If this project helped you understand GitOps and Flux, consider leaving a star!</i>
</div>
