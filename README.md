# tropical-fnb-platform-gitops

GitOps repository for **Tropical F&B Platform**.

## Repository Structure
```text
.
├── argocd/
│   └── application.yaml       # Declarative ArgoCD Application manifest
└── helm/
    └── tropical-fnb/          # Production Helm Chart
        ├── Chart.yaml
        ├── values.yaml        # Image tags promoted automatically by Jenkins CI
        └── templates/
            ├── backend-deployment.yaml
            ├── backend-service.yaml
            ├── configmap.yaml
            ├── database-external.yaml  # Headless Service & EndpointSlice for DB-dt (192.168.100.70)
            ├── frontend-deployment.yaml
            ├── frontend-service.yaml
            ├── ingress.yaml
            ├── job-migrate.yaml        # Pre-upgrade DB schema migration hook
            └── secret.yaml
```

## Continuous Delivery Flow
1. Jenkins CI validates code, executes SonarQube Quality Gate, builds & scans container images.
2. Images pushed to Harbor: `harbor-dt.co.id/devops-apps/{tropical-fnb-backend, tropical-fnb-frontend}:${TAG}`.
3. Jenkins updates `helm/tropical-fnb/values.yaml` in this repository with the new immutable tag.
4. ArgoCD reconciles and performs zero-downtime rolling update on Kubernetes namespace `tropical-fnb`.
