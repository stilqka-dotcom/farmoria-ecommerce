# Farmoria Troubleshooting Guide

## Check everything

```bash
kubectl get all -n farmoria
```

## Pod logs

```bash
kubectl logs -n farmoria deployment/wordpress
kubectl logs -n farmoria deployment/mariadb
kubectl logs -n farmoria deployment/redis
```

## Ingress not opening

Check hosts:

```bash
cat /etc/hosts | grep farmoria
```

Expected:

```text
127.0.0.1 farmoria.localdev
```

Start tunnel:

```bash
kubectl port-forward -n ingress-nginx svc/ingress-nginx-controller 8082:80
```

Open `http://farmoria.localdev:8082`.

## Redis not connected

```bash
kubectl get pods -n farmoria | grep redis
kubectl get svc -n farmoria | grep redis
kubectl exec -it -n farmoria deployment/wordpress -- env | grep REDIS
```

Expected:

```text
WP_REDIS_HOST=redis
WP_REDIS_PORT=6379
```

## MariaDB PVC lock

If logs show `Unable to lock ./ibdata1`, use Recreate strategy:

```yaml
strategy:
  type: Recreate
```

## Metrics Server

Patch for Docker Desktop:

```bash
kubectl patch deployment metrics-server -n kube-system --type='json' -p='[
  {"op":"add","path":"/spec/template/spec/containers/0/args/-","value":"--kubelet-insecure-tls"}
]'
```
