# Deployment Guide

## Overview

This guide describes how to deploy the Farmoria Ecommerce project using either Docker Compose or Kubernetes.

The Docker deployment is intended for local development, while the Kubernetes deployment demonstrates a cloud-native environment running locally.

---

# Prerequisites

Before starting, install:

- Git
- Docker Desktop
- Docker Compose
- Kubernetes
- kubectl

Verify the installation:

```bash
git --version
docker --version
docker compose version
kubectl version --client
kubectl cluster-info
```

---

# Clone Repository

Clone the project:

```bash
git clone https://github.com/stilqka-dotcom/farmoria-ecommerce.git
cd farmoria-ecommerce
```

---

# Docker Deployment

## Environment Variables

Create a `.env` file inside the `docker` directory.

Example:

```text
MYSQL_DATABASE=<database>
MYSQL_USER=<user>
MYSQL_PASSWORD=<password>
MYSQL_ROOT_PASSWORD=<root-password>
WORDPRESS_PORT=8080
```

The `.env` file is intentionally excluded from Git.

---

## Build and Start

Move into the Docker directory:

```bash
cd docker
```

Start the environment:

```bash
docker compose up -d
```

Docker Compose creates:

- MariaDB container
- WordPress container
- Persistent Docker volumes

---

## Verify Docker Deployment

```bash
docker ps
```

Expected containers:

- farmoria-db
- farmoria-wordpress

---

# Kubernetes Deployment

Return to the project root:

```bash
cd ..
```

---

## Create Namespace

```bash
kubectl apply -f k8s/namespace.yaml
```

---

## Create Secrets

```bash
kubectl apply -f k8s/secrets.yaml
```

---

## Create Persistent Volume Claim

```bash
kubectl apply -f k8s/pvc.yaml
```

---

## Deploy MariaDB

```bash
kubectl apply -f k8s/mariadb-deployment.yaml
kubectl apply -f k8s/mariadb-service.yaml
```

---

## Deploy Redis

```bash
kubectl apply -f k8s/redis-deployment.yaml
kubectl apply -f k8s/redis-service.yaml
```

---

## Deploy WordPress

```bash
kubectl apply -f k8s/wordpress-deployment.yaml
kubectl apply -f k8s/wordpress-service.yaml
```

---

## Deploy Ingress

```bash
kubectl apply -f k8s/wordpress-ingress.yaml
```

---

# Verify Deployment

Verify all Kubernetes resources:

```bash
kubectl get all -n farmoria
```

Verify storage:

```bash
kubectl get pvc -n farmoria
```

Verify ingress:

```bash
kubectl get ingress -n farmoria
```

Verify pods:

```bash
kubectl get pods -n farmoria
```

Verify services:

```bash
kubectl get svc -n farmoria
```

---

# Resource Monitoring

Verify resource usage:

```bash
kubectl top nodes
```

```bash
kubectl top pods -n farmoria
```

---

# Local Access

Forward the NGINX Ingress Controller:

```bash
kubectl port-forward -n ingress-nginx svc/ingress-nginx-controller 8082:80
```

Open:

```text
http://farmoria.localdev:8082
```

WordPress Admin:

```text
http://farmoria.localdev:8082/wp-admin
```

---

# Local DNS

The following entry must exist inside `/etc/hosts`:

```text
127.0.0.1 farmoria.localdev
```

---

# CI/CD

Every push to the `main` branch triggers GitHub Actions.

The workflow:

1. Checkout repository
2. Authenticate to Docker Hub
3. Build Docker image
4. Push image to Docker Hub

Deployment to Kubernetes is currently performed manually.

---

# Shutdown

Docker:

```bash
cd docker
docker compose down
```

Kubernetes:

```bash
kubectl delete -f k8s/
```

---

# Troubleshooting

Useful commands:

```bash
kubectl describe pod <pod-name> -n farmoria
```

```bash
kubectl logs <pod-name> -n farmoria
```

```bash
kubectl get events -n farmoria
```

```bash
kubectl rollout restart deployment/wordpress -n farmoria
```

```bash
kubectl rollout restart deployment/mariadb -n farmoria
```

```bash
kubectl rollout restart deployment/redis -n farmoria
```