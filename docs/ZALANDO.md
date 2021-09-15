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

## Create a Postgres cluster

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

## Connect using psql

```bash

```

## Delete a Postgres cluster

```bash
kubectl delete postgresql acid-minimal-cluster
```

## Cleanup

```bash
# delete Operator UI
kubectl delete deployment.apps/postgres-operator-ui
kubectl delete service/postgres-operator-ui

# delete Operator
kubectl delete deployment.apps/postgres-operator
kubectl delete service/postgres-operator
```
