# Percona Operator for PostgreSQL 2.2.0 (Tech preview)

* **Date**

    June 29, 2023

* **Installation**

    [Installing Percona Operator for PostgreSQL](https://docs.percona.com/percona-operator-for-postgresql/2.0/index.html#installation-guide) 


The Percona Operator built with best practices of configuration and setup of
[Percona Distribution for PostgreSQL on Kubernetes](https://www.percona.com/doc/postgresql/LATEST/index.html).

Percona Operator for PostgreSQL helps create and manage highly available, enterprise-ready PostgreSQL clusters on Kubernetes. It is 100% open source, free from vendor lock-in, usage restrictions and expensive contracts, and includes enterprise-ready features: backup/restore, high availability, replication, logging, and more.

The benefits of using Percona Operator for PostgreSQL include saving time on database operations via automation of Day-1 and Day-2 operations and deployment of consistent and vetted environment on Kubernetes.

**Percona announces the general availability of Percona Distribution for PostgreSQL Operator 2.2.0.**

## Release Highlights

* Starting with this release, Percona Operator for PostgreSQL version 2 is out of technical preview and can be used in production with all the improvements it brings over version 1 in terms of architecture, backup and recovery features, and overall flexibility

* We prepared a detailed [migration guide](../upgrade.md) which allows moving existing PostgreSQL clusters previously managed by the Operator 1.x to the new Operator version.

## Improvements

* {{ k8spgjira(378) }}: Add a field version or crVersion for identifying operator version

* {{ k8spgjira(359) }}: The new `users.secretName` option allows to define a custom Secret name for the users defined in the Custom Resource (thanks to Vishal Anarase for contributing)

* {{ k8spgjira(301) }}: The Operator [can now be installed](../eks.md) on [Amazon Elastic Container Service for Kubernetes (EKS)](https://aws.amazon.com) was added to the list of officially supported platforms

* {{ k8spgjira(302) }}: [Minikube](https://github.com/kubernetes/minikube) platform is now [officially supported by the Operator](../minikube.md)

* {{ k8spgjira(326) }}: Both the Operator and database [can be now installed](../helm.md) with the Helm package manager

* {{ k8spgjira(342) }}: There is now no need in manual restart of PostgreSQL Pods after the monitor user password changed in Secrets 

* {{ k8spgjira(345) }}: The new `proxy.pgBouncer.exposeSuperusers` Custom Resource option [makes it possible](../users.md) for administrative users to connect to PostgreSQL through PgBouncer


## Bugs Fixed

* {{ k8spgjira(373) }}: Fix the bug due to which the Operator did not not create Secrets for the `pguser` user if PMM was enabled in the Custom Resource

* {{ k8spgjira(362) }}: It was impossible to install Custom Resource Definitions for both 1.x and 2.x Operators in one environment, preventing the migration of a cluster to the newer Operator version

* {{ k8spgjira(360) }}: Fix a bug due to which manual password changing or resetting via Secret didn't work

* {{ k8spgjira(351) }}: Migration via PVC reutilisation fails REMOVE

* {{ k8spgjira(355) }}: The Operator can now be deployed in multi-namespace ("cluster-wide") mode to track Custom Resources and manage database clusters in several namespaces

## Supported platforms

The following platforms were tested and are officially supported by the Operator
2.2.0:

* [Google Kubernetes Engine (GKE)](https://cloud.google.com/kubernetes-engine) 1.23 - 1.25

* [Amazon Elastic Container Service for Kubernetes (EKS)](https://aws.amazon.com) 1.23 - 1.25

* [Minikube](https://github.com/kubernetes/minikube) 1.29

This list only includes the platforms that the Percona Operators are specifically tested on as part of the release process. Other Kubernetes flavors and versions depend on the backward compatibility offered by Kubernetes itself.

