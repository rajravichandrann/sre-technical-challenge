# SRE Technical Challenge

This repository contains the Helm chart and configuration files required to deploy the **SRE Technical Challenge** project, which includes a Phoenix application (`ghcr.io/stordco/sre-technical-challenge:latest`) and a PostgreSQL database.

## Table of Contents
- [Prerequisites](#prerequisites)
- [Installation](#installation)
  - [Step 1: Install PostgreSQL](#step-1-install-postgresql)
  - [Step 2: Install the Phoenix Application](#step-2-install-the-phoenix-application)
- [Validation](#validation)
  - [Validate PostgreSQL](#validate-postgresql)
  - [Validate the Phoenix Application](#validate-the-phoenix-application)
- [Helm Chart Structure](#helm-chart-structure)
- [Values Configuration](#values-configuration)

---

## Prerequisites

- Kubernetes cluster (EKS or any Kubernetes environment)
- Helm 3.x installed
- PostgreSQL client installed locally (for validation)

## Installation

### Step 1: Install PostgreSQL

The following command will install PostgreSQL in the `stord` namespace using the Bitnami Helm chart. It also sets the database name and password according to the requirements.

```bash
NAMESPACE=stord
helm install sre-db oci://registry-1.docker.io/bitnamicharts/postgresql \
  --create-namespace \
  --namespace $NAMESPACE \
  --set auth.database=sre-technical-challenge \
  --set auth.postgresPassword=password```

This will create the PostgreSQL database, which is required for the Phoenix application.

Step 2: Install the Phoenix Application
Next, use the Helm chart provided in this repository to deploy the Phoenix application. The values in values.yaml are already configured to connect to the PostgreSQL database.

```bash
NAMESPACE=stord
helm install sre-app ./sre-app \
  --debug \
  --namespace $NAMESPACE \
  --values ./sre-app/values.yaml```

### Validation
Once the services are deployed, validate that everything is working correctly by following these steps.

### PostgreSQL
To verify that PostgreSQL is running and accessible, you can forward the PostgreSQL port and connect using the psql command.
```bash
kubectl port-forward svc/sre-db-postgresql 55432:5432 --namespace stord
psql postgresql://postgres:password@127.0.0.1:55432/sre-technical-challenge
```

# Validate the Phoenix Application
To verify that the Phoenix application is running, use kubectl port-forward to access the application in your browser.
```bash 
kubectl port-forward svc/sre-app-sre-technical-challenge 8080:80 --namespace stord
```
Once the service is forwarded, open your browser and visit:

Todos page: http://localhost:8080/todos
Health check: http://localhost:8080/_health
