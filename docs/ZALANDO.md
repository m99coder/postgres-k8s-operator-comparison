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

## Check

```bash
# check if operator is running
kubectl get pod -l name=postgres-operator

# checking logs of the respective pod
kubectl logs "$(kubectl get pod -l name=postgres-operator --output='name')"
```

## Operator UI

```bash
# deploy UI manifests
kubectl apply \
  -f ui/manifests/deployment.yaml \
  -f ui/manifests/ingress.yaml \
  -f ui/manifests/service.yaml \
  -f ui/manifests/ui-service-account-rbac.yaml

# check if UI pods are running
kubectl get pod -l name=postgres-operator-ui
```

> So far I was not able to get the Operator UI running as it probably requires the Operator service to be exposed.

## Connect using psql

```bash
# get name of master pod of acid-minimal-cluster
export PGMASTER=$(kubectl get pods -o jsonpath={.items..metadata.name} -l application=spilo,cluster-name=acid-minimal-cluster,spilo-role=master -n default)

# set up port forwarding
kubectl port-forward $PGMASTER 5432:5432
```

```bash
# retrieve password from secret
export PGPASSWORD=$(kubectl get secret postgres.acid-minimal-cluster.credentials -o 'jsonpath={.data.password}' | base64 -d)

# enable SSL mode
export PGSSLMODE=require

# connect to database
psql -U postgres -h localhost -p 5432
```

## Delete a Postgres cluster

```bash
kubectl delete postgresql acid-minimal-cluster
```

## Cleanup

```bash
# delete operator
kubectl delete deployment.apps/postgres-operator
kubectl delete service/postgres-operator
```
