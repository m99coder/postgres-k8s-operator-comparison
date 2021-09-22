# CrunchyData Postgres Operator

> Comparison of PostgreSQL HA Kubernetes Operators

## Setup

```bash
# change into the respective directory
cd crunchydata-examples

# install the operator
kubectl apply -k kustomize/install
```

### Check

```bash
# check if operator is running
kubectl -n postgres-operator get pods \
  --selector=postgres-operator.crunchydata.com/control-plane=postgres-operator \
  --field-selector=status.phase=Running
```

### Create a Postgres cluster

```bash
# deploy example Postgres cluster
kubectl apply -k kustomize/postgres

# check the deployed cluster
kubectl -n postgres-operator describe postgresclusters.postgres-operator.crunchydata.com hippo

# check created pods
kubectl -n postgres-operator get pods \
  --selector=postgres-operator.crunchydata.com/cluster=hippo,postgres-operator.crunchydata.com/instance
```

### Delete a Postgres cluster

```bash
kubectl delete -k kustomize/postgres
```

### Cleanup

```bash
kubectl delete -k kustomize/install
```

## Tasks

### Connect to database and execute statements

### Scale up and down

### Backup and restore

### Recover from failure
