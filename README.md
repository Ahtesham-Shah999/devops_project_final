# FLUX-CD-Devops

A complete GitOps project using Flux CD.

## Project Structure

```
devops_project_final/
├── apps/
│   ├── base/               # Base Kubernetes manifests
│   │   ├── deployment.yaml
│   │   ├── service.yaml
│   │   └── kustomization.yaml
│   └── production/         # Production overlay (3 replicas)
│       └── kustomization.yaml
├── clusters/
│   └── my-cluster/         # Flux cluster config
│       └── webapp-kustomization.yaml
└── infrastructure/
    └── sources/            # Flux GitRepository sources
```

## Features
- GitOps with Flux CD v2
- Automatic drift detection and reconciliation
- Production overlay with 3 replicas
- Nginx webapp deployment
- NodePort service on port 30080
