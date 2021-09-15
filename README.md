# postgres-k8s-operator-comparison

> Comparison of PostgreSQL HA Kubernetes Operators

This repository functions as a comparison of different Kubernetes Operators for PostgreSQL High Availability.

The operators that will be covered are, so far:

- [Zalando Postgres Operator](https://github.com/zalando/postgres-operator)
- [CrunchyData Postgres Operator](https://github.com/CrunchyData/postgres-operator)
- [StackGres](https://gitlab.com/ongresinc/stackgres)
- [KubeDB Operator](https://github.com/kubedb/operator)

These are the planned steps:

- [x] Document a typical database lifecyle
- [ ] Define show cases or tasks of that lifecycle to contain in the comparison
- [ ] Document a local setup for each operator based on `kind` or `minikube`
- [ ] Document the execution of each defined show case or task for each operator
- [ ] Create a data-driven comparison

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
