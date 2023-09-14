# About backups

In this section you will learn how to set up and manage backups of your data using the Operator.

You can make backups in two ways:

* _On-demand_. You can do them manually at any moment.
* _Schedule backups_. Configure backups and their schedule in the
[deploy/cr.yaml](https://github.com/percona/percona-postgresql-operator/blob/main/deploy/cr.yaml)
file. The Operator makes them automatically according to the schedule. 

## What you need to know

### Backup repositories

To make backups, the Operator uses the open source [pgBackRest](https://pgbackrest.org/) backup
and restore utility. 

When the Operator creates a new PostgreSQL cluster, it also creates a special *pgBackRest repository* to facilitate the usage of the pgBackRest
features. You can notice an additional `repo-host` Pod after the cluster
creation.

A pgBackRest repository consists of the following Kubernetes objects:

* A Deployment,
* A Secret that contains information specific to the PostgreSQL cluster
    (e.g. SSH keys, AWS S3 keys, etc.),
* A Pod with a number of supporting scripts,
* A Service.

In the `/deploy/cr.yml` file, pgBackRest repositories are listed in the `backups.pgbackrest.repos` subsection. You can have up to 4 repositories as `repo1`, `repo2`, `repo3`,
and `repo4`.


### Backup types

You can make the following types of backups:

* `full`: A full backup of all the contents of the PostgreSQL cluster,
* `differential`: A backup of only the files that have changed since
the last full backup,
* `incremental`: Default. A backup of only the files that have changed since the
last full or differential backup. 

### Backup storage

You have the following options to store PostgreSQL backups outside the
Kubernetes cluster:

* Cloud storage:

    * [Amazon S3](https://aws.amazon.com/s3/), or [any S3-compatible storage](https://en.wikipedia.org/wiki/Amazon_S3#S3_API_and_competing_services),
    * [Google Cloud Storage](https://cloud.google.com/storage), 
    * [Azure Blob Storage](https://azure.microsoft.com/en-us/services/storage/blobs/)

* A [Persistent Volume](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) attached to the pgBackRest Pod.

## Next steps

Ready to move forward? [Configure backup storage](backups-storage.md)





