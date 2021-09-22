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
# deploy example Postgres cluster (takes up to 8 minutes)
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

```bash
$ # services
$ kubectl -n postgres-operator get svc --selector=postgres-operator.crunchydata.com/cluster=hippo
NAME              TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)    AGE
hippo-ha          ClusterIP   10.43.25.41   <none>        5432/TCP   9m48s
hippo-ha-config   ClusterIP   None          <none>        <none>     9m48s
hippo-pods        ClusterIP   None          <none>        <none>     9m48s
hippo-primary     ClusterIP   None          <none>        5432/TCP   9m48s

$ # secrets
$ kubectl -n postgres-operator get secret --selector=postgres-operator.crunchydata.com/cluster=hippo
NAME                         TYPE     DATA   AGE
hippo-cluster-cert           Opaque   3      11m
hippo-instance1-s9q8-certs   Opaque   4      11m
hippo-pguser-hippo           Opaque   7      11m
hippo-replication-cert       Opaque   3      11m
hippo-ssh                    Opaque   3      11m

$ # get secret values in plain text
$ kubectl -n postgres-operator get secret hippo-pguser-hippo \
    --template={{.data.uri}} | base64 --decode

$ # get name of leader pod
$ export PRIMARY_POD=$(kubectl -n postgres-operator get pods \
  --selector=postgres-operator.crunchydata.com/role=master \
  -o jsonpath='{.items[*].metadata.labels.postgres-operator\.crunchydata\.com/instance}')

$ # set up port forwarding
$ kubectl port-forward "${PRIMARY_POD}-0" 5432:5432
```

```bash
# retrieve password from secret
export PGPASSWORD=$(kubectl get secret hippo-pguser-hippo \
  -o 'jsonpath={.data.password}' | base64 -d)

# enable SSL mode
export PGSSLMODE=require

# connect to database
psql -U hippo -h localhost -p 5432
```

```postgres
hippo=> SELECT version();
                                                version
--------------------------------------------------------------------------------------------------------
 PostgreSQL 13.4 on x86_64-pc-linux-gnu, compiled by gcc (GCC) 8.4.1 20200928 (Red Hat 8.4.1-1), 64-bit
(1 row)
```

### Scale up and down

```bash
# scale up to 2 instances
sed $'s/- name: instance1/- name: instance1\\\n      replicas: 2/g' kustomize/postgres/postgres.yaml | \
  kubectl replace -f -

# scale down again to 1 instance
kubectl replace -f kustomize/postgres/postgres.yaml

# remove a service
kubectl -n postgres-operator delete svc hippo-primary

# remove the primary StatefulSet
kubectl delete sts -n postgres-operator "${PRIMARY_POD}"
```

```postgres
hippo=> SELECT NOT pg_catalog.pg_is_in_recovery() is_primary;
 is_primary
------------
 t
(1 row)
```

The result `t` (or `true`) means the Postgres instance is a primary.

### Backup and restore

### Recover from failure
