# postgres-k8s-operator-comparison

> Comparison of PostgreSQL HA Kubernetes Operators

This repository functions as a comparison of different Kubernetes Operators for PostgreSQL High Availability. [OperatorHub.io](https://operatorhub.io/?category=Database) provides a list of database-related operators.

The operators that will be covered are, so far:

- [Zalando Postgres Operator](https://github.com/zalando/postgres-operator)
- [CrunchyData Postgres Operator](https://github.com/CrunchyData/postgres-operator)
- [StackGres](https://gitlab.com/ongresinc/stackgres)
- [KubeDB Operator](https://github.com/kubedb/operator)

These are the planned steps:

- [x] Document a typical database lifecyle
- [x] Define show cases or tasks of that lifecycle to contain in the comparison
- [ ] Document a local setup for each operator based on `minikube`, `kind`, or `k3d`
- [ ] Document the execution of each defined show case or task for each operator
- [ ] Create a data-driven comparison

## Cloning

For each operator a [Git Submodule](https://git-scm.com/book/en/v2/Git-Tools-Submodules) was added. In order to also fetch the code from these repositories one needs to run the following commands:

```bash
# initialize local configuration file
git submodule init

# check if a submodule was updated
git diff --submodule

# fetch all the data
git submodule update

# alternatively clone this repository as follows
git clone --recurse-submodules \
  git@github.com:m99coder/postgres-k8s-operator-comparison.git
```

## Database Lifecycle Management

### Service Factors

- Codebase (Infrastructure as Code with CI/CD)
- Dependencies (IaC tool ⟶ VM ⟶ Containers ⟶ Postgres HA Cluster)
- Config (Initial configuration, undisruptive/disruptive configuration changes)
- Backing services (Docker images)
- Build, release, run (IaC tool)
- Processes (Schema and data migrations, DML/DDL, Security, Users & Permissions)
- Endpoints (Connection Pooler, VPC, Security Groups)
- Concurrency (Horizontal scaling using Containers – Scale up/down, Vertical scaling using VMs – Scale out/in, Replication, Partitioning, Network Partitioning and Split Brain Detection)
- Disposability (Backup/Restore, Shutdown, Data lifecycle decoupled by using attached block device ⟶ ephemeral VM, persistent disk)
- Dev/prod parity (e.g. [LocalStack](https://github.com/localstack/localstack), [Terratest](https://github.com/gruntwork-io/terratest))
- Logs (Monitoring, Logging, Benchmarking, …)
- Admin processes (Failover/Switchover, Upgrade)

### Lifecycle

A typical database lifecycle consists of the following steps:

1. Create
2. Modify
3. Upgrade
4. Backup/Restore
5. Terminate

### Administrative Tasks

- Provision database VM
- Install database software
- Configure database software
- Setup replication & cluster management
- Configure monitoring
- Configure availability monitoring
- Configure alerting
- Configure collection of logs
- Configure collection of metrics
- Create database
- Create database users
- Adapt configuration of individual database servers (e.g. enable extensions)
- Adapt configuration of individual databases
- Setup backup procedure
- Perform backups regularly
- Perform on-demand backups
- Perform schema migrations
- Query data
- Determine performance bottlenecks
- Perform scale-out of database server
- Recover from Leader DB failure
- Recover from Replica DB failure
- Recover from Availability Zone failure
- Recover from PostgreSQL process failure
- Restore data from backup
- Apply patch-level upgrade
- Apply major and minor level upgrade
- Perform data migration
- Destroy data
- Destroy database server

## Test Cases

In order to keep this comparison basic, only the following tasks are taken into consideration:

- Connect to database and execute statements
- Scale up and down
- Backup and restore, optionally with Point-in-time Recovery (PITR) if available
- Recover from Leader DB and Replica DB failure

More test cases might be added in the future.

## Local Setups

The first step to setup the operators locally is to start a Kubernetes cluster. For that purpose one can choose between [minikube](https://minikube.sigs.k8s.io/docs/) (single-node setup), [kind](https://kind.sigs.k8s.io/), or [k3d](https://k3d.io/).

To use `k3d` execute the following commands on macOS:

```bash
# install k3d using homebrew
brew install k3d

# create cluster
# - with 3 nodes to achieve quorum and fault tolerance
# - with 3 agents (formerly worker nodes)
# - with a mounted volume for data like backups
k3d cluster create mycluster \
  --servers 3 \
  --agents 3 \
  --volume /tmp/k3dvol:/tmp/k3dvol

# start cluster
k3d cluster start mycluster

# list clusters
k3d cluster list

# set correct context
kubectl config use-context k3d-mycluster

# check cluster
kubectl cluster-info

# check underlying containers
docker ps

# get list of nodes
kubectl get nodes

# stop cluster
k3d cluster stop mycluster

# delete cluster
k3d cluster delete mycluster
```

Operator-specific setup steps are documented in the respective README files:

- [Zalando Postgres Operator](./docs/ZALANDO.md)
- [CrunchyData Postgres Operator](./docs/CRUNCHYDATA.md)
- [StackGres](./docs/STACKGRES.md)
- [KubeDB Operator](./docs/KUBEDB.md)

## Resources

- [K3S + K3D = K8S: a new perfect match for dev and test](https://en.sokube.ch/post/k3s-k3d-k8s-a-new-perfect-match-for-dev-and-test-1)
- [Introduction to k3d: Run K3s in Docker](https://www.suse.com/c/introduction-k3d-run-k3s-docker-src/)
