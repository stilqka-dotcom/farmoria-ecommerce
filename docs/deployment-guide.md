# Farmoria Deployment Guide

## Prerequisites

Install Docker Desktop, enable Kubernetes, install kubectl and Git.

## Clone

```bash
git clone https://github.com/stilqka-dotcom/farmoria-ecommerce.git
cd farmoria-ecommerce
```

## Docker Compose

```bash
cd docker
docker compose up -d --build
```

Open `http://localhost:8080`.

Stop:

```bash
docker compose down
```

## Kubernetes

From project root:

```bash
kubectl apply -f k8s/namespace.yaml
kubectl apply -f k8s/secrets.yaml
kubectl apply -f k8s/pvc.yaml
kubectl apply -f k8s/mariadb-deployment.yaml
kubectl apply -f k8s/mariadb-service.yaml
kubectl apply -f k8s/redis-deployment.yaml
kubectl apply -f k8s/redis-service.yaml
kubectl apply -f k8s/wordpress-deployment.yaml
kubectl apply -f k8s/wordpress-service.yaml
kubectl apply -f k8s/wordpress-ingress.yaml
```

Check:

```bash
kubectl get all -n farmoria
```

## Ingress

Install NGINX Ingress Controller:

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.11.3/deploy/static/provider/cloud/deploy.yaml
```

Add to `/etc/hosts`:

```text
127.0.0.1 farmoria.localdev
127.0.0.1 www.farmoria.localdev
```

Start ingress tunnel:

```bash
kubectl port-forward -n ingress-nginx svc/ingress-nginx-controller 8082:80
```

Open `http://farmoria.localdev:8082`.

## Monitoring

```bash
kubectl top nodes
kubectl top pods -n farmoria
```
