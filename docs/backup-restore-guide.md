# Backup and Restore Guide

## Overview

This document describes the current backup and restore procedure for the Farmoria Ecommerce project.

At the current stage, backups are performed manually. Automated backup and disaster recovery are planned for the future AWS deployment.

---

# What Should Be Backed Up

The following components should be included in every backup:

- MariaDB database
- WordPress content
- Kubernetes manifests
- Docker configuration
- Project documentation

Sensitive files such as secrets and local environment variables should be stored securely and must never be committed to Git.

---

# MariaDB Backup

Access the MariaDB pod:

```bash
kubectl get pods -n farmoria
```

Create a database dump:

```bash
kubectl exec -it <mariadb-pod> -n farmoria -- \
mariadb-dump -u root -p <database-name> > farmoria-backup.sql
```

The password will be requested interactively.

Store the generated SQL file outside the Kubernetes cluster.

---

# MariaDB Restore

Copy the backup file to a safe location.

Restore the database:

```bash
kubectl exec -i <mariadb-pod> -n farmoria -- \
mariadb -u root -p <database-name> < farmoria-backup.sql
```

Verify that all tables and records have been restored successfully.

---

# WordPress Files

Important WordPress components include:

- Themes
- Plugins
- Media uploads
- Configuration files

These files should be backed up before performing major updates or migrations.

---

# Kubernetes Configuration

The following files should always be backed up:

```text
k8s/
docker/
docs/
README.md
```

These files allow the Kubernetes environment to be recreated.

---

# Verification

After restoring:

Verify the Pods:

```bash
kubectl get pods -n farmoria
```

Verify the Services:

```bash
kubectl get svc -n farmoria
```

Verify the Ingress:

```bash
kubectl get ingress -n farmoria
```

Verify the application:

- Open the homepage.
- Log in to the WordPress admin panel.
- Confirm products and pages are available.
- Verify database connectivity.
- Verify Redis object cache functionality.

---

# Best Practices

- Keep multiple backup versions.
- Test restore procedures regularly.
- Store backups outside the Kubernetes cluster.
- Protect backup files with restricted access.
- Never store passwords inside backup documentation.

---

# Future Backup Strategy

The future production deployment will include:

- Scheduled MariaDB backups
- Persistent volume snapshots
- Backup storage in AWS
- Automated backup verification
- Disaster recovery procedures