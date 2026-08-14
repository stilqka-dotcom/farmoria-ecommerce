# Troubleshooting Guide

## Overview

This document contains common troubleshooting procedures for the Farmoria Ecommerce Kubernetes environment.

The commands below are intended for the local Kubernetes deployment running in the `farmoria` namespace.

---

# Pod Not Running

Check pod status:

```bash
kubectl get pods -n farmoria
```

If a pod is not running, inspect it:

```bash
kubectl describe pod <pod-name> -n farmoria
```

Check logs:

```bash
kubectl logs <pod-name> -n farmoria
```

For deployments, logs can also be retrieved directly:

```bash
kubectl logs deployment/wordpress -n farmoria
kubectl logs deployment/mariadb -n farmoria
kubectl logs deployment/redis -n farmoria
```

---

# Pod Not Ready

Check pod readiness:

```bash
kubectl get pods -n farmoria
```

Inspect readiness and liveness probe events:

```bash
kubectl describe pod <pod-name> -n farmoria
```

Typical causes include:

- incorrect service configuration
- database unavailable
- application startup delay
- failing health probes
- incorrect environment variables

---

# WordPress HTTP 500 Error

Check WordPress logs:

```bash
kubectl logs deployment/wordpress -n farmoria
```

Inspect the pod:

```bash
kubectl describe pod -n farmoria -l app=wordpress
```

Restart the deployment if required:

```bash
kubectl rollout restart deployment/wordpress -n farmoria
```

Monitor rollout status:

```bash
kubectl rollout status deployment/wordpress -n farmoria
```

---

# WordPress Cannot Connect to MariaDB

Verify MariaDB is running:

```bash
kubectl get pods -n farmoria
```

Verify MariaDB service:

```bash
kubectl get svc mariadb -n farmoria
```

Check WordPress database environment variables:

```bash
kubectl exec -n farmoria deployment/wordpress -- env | grep WORDPRESS_DB
```

Verify that WordPress is configured to connect to:

```text
mariadb:3306
```

Check MariaDB logs:

```bash
kubectl logs deployment/mariadb -n farmoria
```

---

# Redis Unreachable

Verify Redis pod:

```bash
kubectl get pods -n farmoria
```

Verify Redis service:

```bash
kubectl get svc redis -n farmoria
```

Check Redis-related WordPress environment variables:

```bash
kubectl exec -n farmoria deployment/wordpress -- env | grep REDIS
```

Expected configuration includes:

```text
WP_REDIS_HOST=redis
WP_REDIS_PORT=6379
```

Check Redis logs:

```bash
kubectl logs deployment/redis -n farmoria
```

Inside WordPress, verify the Redis Object Cache plugin reports:

```text
Status: Connected
Redis: Reachable
```

---

# Ingress Not Reachable

Verify the application ingress:

```bash
kubectl get ingress -n farmoria
```

Expected hostname:

```text
farmoria.localdev
```

Verify the NGINX Ingress Controller:

```bash
kubectl get pods -n ingress-nginx
```

Verify its service:

```bash
kubectl get svc -n ingress-nginx
```

For local access, start port forwarding:

```bash
kubectl port-forward -n ingress-nginx svc/ingress-nginx-controller 8082:80
```

Then open:

```text
http://farmoria.localdev:8082
```

---

# Local Port Already in Use

If port forwarding returns:

```text
bind: address already in use
```

the local port is already being used.

Either use the existing port-forward process or select another local port:

```bash
kubectl port-forward -n ingress-nginx svc/ingress-nginx-controller 8083:80
```

Then open:

```text
http://farmoria.localdev:8083
```

---

# farmoria.localdev Does Not Resolve

Verify the local hosts file contains:

```text
127.0.0.1 farmoria.localdev
```

On macOS, inspect the file:

```bash
cat /etc/hosts
```

Test name resolution:

```bash
ping farmoria.localdev
```

---

# MariaDB Persistent Storage
```