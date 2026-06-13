# Farmoria Backup and Restore Guide

## Production Backup

The production site is hosted on Hostinger and should be backed up with UpdraftPlus.

Steps:

1. Open `https://farmoria.shop/wp-admin`.
2. Go to `Settings → UpdraftPlus Backups`.
3. Click `Backup Now`.
4. Include database, plugins, themes, uploads, and others.
5. Download all backup files locally.
6. Store a second copy in cloud storage.

## Restore Test

Restore to LocalWP, a temporary WordPress install, or Kubernetes staging. Validate homepage, products, images, admin login, cart, and checkout.

## Kubernetes MariaDB Backup

```bash
kubectl exec -n farmoria deployment/mariadb -- mariadb-dump -u root -proot_pass farmoria > farmoria-k8s-backup.sql
```

Restore:

```bash
cat farmoria-k8s-backup.sql | kubectl exec -i -n farmoria deployment/mariadb -- mariadb -u root -proot_pass farmoria
```

## Disaster Recovery

If Kubernetes breaks:

1. Recreate cluster.
2. Clone GitHub repo.
3. Apply manifests.
4. Restore MariaDB dump.
5. Verify WordPress, Redis, Ingress, and admin login.
