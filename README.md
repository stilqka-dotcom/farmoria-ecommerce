# Farmoria Ecommerce Platform

Farmoria is a WooCommerce ecommerce project with a traditional production WordPress deployment and a containerized Kubernetes-based development/staging environment.

## Live Website

- Production: https://farmoria.shop
- Kubernetes local environment: http://farmoria.localdev:8082

## Project Goals

The project demonstrates how a WordPress/WooCommerce store can be operated as a modern cloud-ready workload using Docker, Kubernetes, Redis, MariaDB, Ingress, monitoring, and CI/CD.

## Technology Stack

### Application
- WordPress
- WooCommerce
- MariaDB
- Redis Object Cache

### Infrastructure
- Docker
- Docker Compose
- Docker Hub
- Kubernetes
- NGINX Ingress Controller
- Kubernetes Secrets
- PersistentVolumeClaim
- Metrics Server

### CI/CD
- GitHub Actions
- Docker image build
- Docker Hub push

## Architecture Summary

```text
User
 ↓
Local DNS / hosts file
 ↓
NGINX Ingress Controller
 ↓
WordPress Service
 ↓
WordPress Pod
 ↓
Redis Service ─ Redis Pod
 ↓
MariaDB Service
 ↓
MariaDB Pod
 ↓
Persistent Volume Claim
```

CI/CD flow:

```text
git push
 ↓
GitHub Actions
 ↓
Docker build
 ↓
Docker Hub push
 ↓
Kubernetes uses image: stilyan03/farmoria-wordpress:latest
```

## Current Status

Implemented:

- Dockerized WordPress image
- Docker Compose local stack
- Docker Hub image publishing
- Kubernetes namespace
- Kubernetes secrets
- MariaDB deployment and service
- WordPress deployment and service
- Redis deployment and service
- Persistent storage for MariaDB
- NGINX Ingress
- Resource requests and limits
- Liveness and readiness probes
- Metrics Server monitoring
- GitHub Actions CI/CD

## Repository Structure

```text
farmoria-ecommerce/
├── .github/workflows/
│   └── docker-build-push.yml
├── docker/
│   ├── Dockerfile
│   └── docker-compose.yml
├── k8s/
│   ├── namespace.yaml
│   ├── secrets.yaml
│   ├── pvc.yaml
│   ├── mariadb-deployment.yaml
│   ├── mariadb-service.yaml
│   ├── wordpress-deployment.yaml
│   ├── wordpress-service.yaml
│   ├── wordpress-ingress.yaml
│   ├── redis-deployment.yaml
│   └── redis-service.yaml
├── docs/
├── screenshots/
└── README.md
```

## Important Notes

The live production site `farmoria.shop` currently runs on Hostinger.

The Kubernetes version is a local development/staging environment. It is not publicly accessible unless deployed to a VPS/cloud provider and connected through public DNS.
