# Zalando Postgres Operator

> Comparison of PostgreSQL HA Kubernetes Operators

## Setup

```bash
# change into the respective directory
cd zalando

# apply the manifests in the following order
# 1. configuration
kubectl create -f manifests/configmap.yaml
# 2. identity and permissions
kubectl create -f manifests/operator-service-account-rbac.yaml
# 3. deployment
kubectl create -f manifests/postgres-operator.yaml
# 4. operator API to be used by UI
kubectl create -f manifests/api-service.yaml
```

### Check

```bash
# check if operator is running
kubectl get pod -l name=postgres-operator

# checking logs of the respective pod
kubectl logs "$(kubectl get pod -l name=postgres-operator --output='name')"
```

### Create a Postgres cluster

```bash
# deploy minimal Postgres cluster
kubectl create -f manifests/minimal-postgres-manifest.yaml

# check the deployed cluster
kubectl get postgresql

# check created database pods
kubectl get pods -l application=spilo -L spilo-role

# check created service resources
kubectl get svc -l application=spilo -L spilo-role
```

### Delete a Postgres cluster

```bash
kubectl delete postgresql acid-minimal-cluster
```

### Cleanup

```bash
# delete operator
kubectl delete deployment.apps/postgres-operator
kubectl delete service/postgres-operator
```

## Tasks

### Connect to database and execute statements

```bash
# get name of leader pod of acid-minimal-cluster
export PGLEADER=$(kubectl get pods -o jsonpath={.items..metadata.name} -l application=spilo,cluster-name=acid-minimal-cluster,spilo-role=master -n default)

# set up port forwarding
kubectl port-forward $PGLEADER 5432:5432
```

```bash
# retrieve password from secret
export PGPASSWORD=$(kubectl get secret postgres.acid-minimal-cluster.credentials -o 'jsonpath={.data.password}' | base64 -d)

# enable SSL mode
export PGSSLMODE=require

# connect to database
psql -U postgres -h localhost -p 5432
```

```postgres
postgres=# SELECT version();
                                                             version
---------------------------------------------------------------------------------------------------------------------------------
 PostgreSQL 13.4 (Ubuntu 13.4-1.pgdg18.04+1) on x86_64-pc-linux-gnu, compiled by gcc (Ubuntu 7.5.0-3ubuntu1~18.04) 7.5.0, 64-bit
(1 row)
```
