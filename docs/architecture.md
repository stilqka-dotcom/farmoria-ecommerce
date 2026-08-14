# Farmoria Architecture

## Overview

Farmoria Ecommerce follows a cloud-native architecture based on Docker, Kubernetes and GitHub Actions.

The application is built around WordPress and WooCommerce and is deployed inside a local Kubernetes cluster. The infrastructure separates the application, database and cache into individual services while using Kubernetes for orchestration and lifecycle management.

The project is designed as a learning environment that demonstrates modern DevOps practices and serves as the foundation for a future production deployment in AWS.

---

## High-Level Architecture

![Farmoria Architecture](../screenshots/architecture-diagram.png)

Overall workflow:

```text
Developer
      │
      ▼
GitHub Repository
      │
      ▼
GitHub Actions
      │
      ▼
Docker Hub
      │
      ▼
Kubernetes Cluster
      │
      ├──────────────┐
      ▼              ▼
 WordPress       MariaDB
      │
      ▼
    Redis
```

---

## CI/CD Flow

The project uses GitHub Actions for Continuous Integration.

Every push to the **main** branch automatically starts the pipeline.

Pipeline sequence:

1. Checkout repository
2. Authenticate to Docker Hub
3. Build Docker image
4. Push Docker image

The current implementation automates image creation and publishing only.

Deployment to Kubernetes is currently performed manually.

---

## Kubernetes Architecture

The application is deployed inside the Kubernetes namespace:

```text
farmoria
```

The namespace contains:

- WordPress Deployment
- MariaDB Deployment
- Redis Deployment
- Services
- Ingress
- Persistent Volume Claim
- Secrets

---

## Application Layer

The application layer consists of a single WordPress Deployment.

Responsibilities:

- Website rendering
- WooCommerce
- Product management
- User authentication
- Order processing

WordPress communicates internally with MariaDB and Redis.

---

## Database Layer

MariaDB stores all persistent ecommerce data.

Examples include:

- Users
- Products
- Orders
- WooCommerce settings
- WordPress configuration

MariaDB uses a Persistent Volume Claim to preserve data across pod restarts.

---

## Cache Layer

Redis is used as an Object Cache.

Caching reduces the number of repeated database queries and improves response times.

Redis is available only inside the Kubernetes cluster.

---

## Networking

Traffic enters the cluster through the NGINX Ingress Controller.

Flow:

```text
Browser
    │
    ▼
NGINX Ingress
    │
    ▼
WordPress Service
    │
    ▼
WordPress Pod
```

Internal communication:

```text
WordPress
     │
     ├────► MariaDB Service
     │
     └────► Redis Service
```

MariaDB and Redis are exposed only as ClusterIP services and are not directly accessible from outside the cluster.

---

## Persistent Storage

Current persistent storage:

| Component | Storage |
|----------|---------|
| MariaDB | Persistent Volume Claim |

MariaDB data remains available after pod recreation.

WordPress currently relies on the deployed container image and does not use a dedicated Persistent Volume Claim.

---

## Health Checks

The project uses Kubernetes health probes.

### Readiness Probe

Ensures that WordPress and MariaDB are ready before receiving traffic.

### Liveness Probe

Allows Kubernetes to restart unhealthy containers automatically.

---

## Resource Management

CPU and memory requests and limits are configured for the main workloads.

Benefits include:

- predictable scheduling
- resource isolation
- improved cluster stability

---

## Monitoring

The project uses Kubernetes Metrics Server.

Available commands:

```bash
kubectl top nodes
kubectl top pods -n farmoria
```

Current monitoring provides CPU and memory utilization for both nodes and application pods.

---

## Security

Security mechanisms currently implemented:

- Kubernetes Secrets
- Internal ClusterIP networking
- GitHub Secrets for Docker Hub authentication
- Environment variables stored outside the repository

---

## Current Limitations

Current limitations include:

- Local Kubernetes cluster only
- No public cloud deployment
- No production TLS certificates
- Kubernetes deployment is manual
- No production monitoring stack
- No automated backup solution

---

## Future Architecture

The next planned architecture stage includes:

- AWS deployment
- Public DNS
- HTTPS certificates
- Automated Kubernetes deployment
- Backup automation
- Centralized monitoring
- Production environment