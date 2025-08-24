# PHP Loki Monitoring GitOps

This repository contains the complete GitOps infrastructure for deploying a PHP application with comprehensive logging and monitoring using Loki and Grafana.

## Architecture

- **PHP Application**: Simple PHP app with Apache web server
- **Loki**: Log aggregation system
- **Grafana**: Monitoring and visualization dashboard
- **ArgoCD**: GitOps deployment automation
- **Kubernetes**: Container orchestration platform

## Quick Start

1. Start minikube cluster
2. Install ArgoCD
3. Deploy applications via GitOps

## Directory Structure

`
├── charts/           # Helm charts
│   ├── php-app/     # PHP application chart
│   ├── loki-stack/  # Loki logging chart
│   └── grafana/     # Grafana monitoring chart
├── applications/     # ArgoCD application definitions
├── infrastructure/   # Infrastructure components
├── docs/            # Documentation
└── manifests/       # Additional Kubernetes manifests

