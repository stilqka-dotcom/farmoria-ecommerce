# Farmoria Architecture

## Overview

Farmoria currently has two environments:

1. Production WordPress site on Hostinger: `https://farmoria.shop`
2. Local Kubernetes environment: `http://farmoria.localdev:8082`

The Kubernetes environment is used as a cloud-native staging and portfolio environment. It demonstrates container orchestration, service discovery, persistent storage, Redis caching, monitoring, and CI/CD.

## Production Environment

```text
Internet
 ↓
farmoria.shop
 ↓
Hostinger
 ↓
WordPress + WooCommerce
 ↓
Hostinger database/storage
```

Production includes SSL, MFA, security hardening, backup/restore testing, and WooCommerce setup.

## Kubernetes Environment

```text
Browser
 ↓
farmoria.localdev:8082
 ↓
NGINX Ingress Controller
 ↓
WordPress Service
 ↓
WordPress Pod
 ├── Redis Service → Redis Pod
 └── MariaDB Service → MariaDB Pod → PVC
```

## Components

- Namespace: `farmoria`
- Secrets: `farmoria-secrets`
- Image: `stilyan03/farmoria-wordpress:latest`
- MariaDB storage: `mariadb-pvc`
- Cache: Redis Object Cache
- Monitoring: Metrics Server
- CI/CD: GitHub Actions → Docker Hub

## Current Limitations

The Kubernetes environment is local only. For public production Kubernetes deployment, the project needs VPS/cloud hosting, public DNS, TLS, cloud persistent volumes, backup automation, and production monitoring.
