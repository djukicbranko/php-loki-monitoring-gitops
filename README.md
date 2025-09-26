# PHP Loki Monitoring GitOps Stack

A complete monitoring and logging stack for PHP applications using Grafana Loki, Promtail, and ArgoCD on Kubernetes.

## Quick Start

### Prerequisites
- Minikube running
- kubectl configured
- Git installed

### 1. Clone Repository
```bash
git clone https://github.com/djukicbranko/php-loki-monitoring-gitops.git
cd php-loki-monitoring-gitops
```

### 2. Install ArgoCD
```bash
# Create namespace
kubectl create namespace argocd

# Install ArgoCD
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Wait for ArgoCD to be ready
kubectl wait --for=condition=available --timeout=300s deployment/argocd-server -n argocd
```

### 3. Deploy Applications
```bash
# Create application namespace
kubectl create namespace interview-app

# Deploy all applications
kubectl apply -f applications/loki-stack.yaml
kubectl apply -f applications/php-app.yaml
kubectl apply -f applications/grafana.yaml
```

### 4. Access Services

#### ArgoCD UI
```bash
# Port forward ArgoCD
kubectl port-forward svc/argocd-server -n argocd 8080:443

# Get admin password
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```
- URL: https://localhost:8080
- Username: `admin`
- Password: (from command above)

#### Grafana
```bash
# Port forward Grafana (in new terminal)
kubectl port-forward -n interview-app svc/grafana 3000:3000
```
- URL: http://localhost:3000
- Username: `admin`
- Password: `admin`

#### PHP Application
```bash
# Port forward PHP app (in new terminal)
kubectl port-forward -n interview-app svc/php-app 8081:80
```
- URL: http://localhost:8081

### 5. Generate Logs & View in Grafana
```bash
# Generate traffic
for i in {1..50}; do curl http://localhost:8081/; sleep 1; done
```

In Grafana → Explore → Run query:
```logql
{app="php-app"}
```

## Architecture

```
┌─────────────┐
│   ArgoCD    │ → GitOps deployment
└─────────────┘
       ↓
┌─────────────────────────────┐
│     interview-app namespace  │
│                              │
│  ┌─────────┐  ┌──────────┐ │
│  │ PHP App │→ │ Promtail │ │
│  └─────────┘  └─────┬────┘ │
│                     ↓       │
│  ┌──────────┐  ┌──────┐    │
│  │ Grafana  │← │ Loki │    │
│  └──────────┘  └──────┘    │
└─────────────────────────────┘
```

## Stack Components
- **ArgoCD**: GitOps continuous delivery
- **Loki**: Log aggregation system
- **Promtail**: Log collector agent
- **Grafana**: Visualization and dashboards
- **PHP App**: Sample application with logging

## Useful Commands

```bash
# Check all pods
kubectl get pods -n interview-app

# View logs
kubectl logs -n interview-app deployment/loki
kubectl logs -n interview-app deployment/grafana
kubectl logs -n interview-app deployment/php-app

# Restart deployments
kubectl rollout restart deployment/loki -n interview-app
kubectl rollout restart deployment/grafana -n interview-app

# Delete everything
kubectl delete application loki-stack php-app grafana -n argocd
kubectl delete namespace interview-app argocd
```
