# Install Percona Distribution for PostgreSQL on Amazon Elastic Kubernetes Service (EKS)

This guide shows you how to deploy Percona Operator for PostgreSQL on Amazon
Elastic Kubernetes Service (EKS). The document assumes some experience with the
platform. For more information on the EKS, see the [Amazon EKS official documentation :octicons-link-external-16:](https://aws.amazon.com/eks/).

## Prerequisites

### Software installation

The following tools are used in this guide and therefore should be preinstalled:

1. **AWS Command Line Interface (AWS CLI)** for interacting with the different
    parts of AWS. You can install it following the [official installation instructions for your system :octicons-link-external-16:](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html).

2. **eksctl** to simplify cluster creation on EKS. It can be installed
    along its [installation notes on GitHub :octicons-link-external-16:](https://github.com/weaveworks/eksctl#installation).

3. **kubectl**  to manage and deploy applications on Kubernetes. Install
    it [following the official installation instructions :octicons-link-external-16:](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

Also, you need to configure AWS CLI with your credentials according to the
[official guide :octicons-link-external-16:](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html).

### Creating the EKS cluster {.power-number}

1. To create your cluster, you will need the following data:

    * name of your EKS cluster,
    * AWS region in which you wish to deploy your cluster,
    * the amount of nodes you would like tho have,
    * the desired ratio between [on-demand :octicons-link-external-16:](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-on-demand-instances.html)
        and [spot :octicons-link-external-16:](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-spot-instances.html)
        instances in the total number of nodes.

    !!! note

        [spot :octicons-link-external-16:](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-spot-instances.html)
        instances are not recommended for production environment, but may be useful
        e.g. for testing purposes.

    After you have settled all the needed details, create your EKS cluster [following the official cluster creation instructions :octicons-link-external-16:](https://docs.aws.amazon.com/eks/latest/userguide/create-cluster.html).

2. After you have created the EKS cluster, you also need to [install the Amazon EBS CSI driver :octicons-link-external-16:](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html) on your cluster. See the [official documentation :octicons-link-external-16:](https://docs.aws.amazon.com/eks/latest/userguide/managing-ebs-csi.html) on adding it as an Amazon EKS add-on.

    !!! note

        CSI driver is needed for the Operator to work propely, and is not included by default starting from the Amazon EKS version 1.22. Therefore sers with existing EKS cluster based on the version 1.22 or earlier need to install CSI driver before updating the EKS cluster to 1.23 or above.

## Install the Operator and Percona Distribution for PostgreSQL

The following steps are needed to deploy the Operator and Percona Distribution for PostgreSQL in
your Kubernetes environment:
{.power-number}

1. Create the Kubernetes namespace for your cluster if needed (for example,
   let's name it `postgres-operator`):

    ``` {.bash data-prompt="$" }
    $ kubectl create namespace postgres-operator
    ```

    ??? example "Expected output"

        ``` {.text .no-copy}
        namespace/postgres-operator was created
        ```

    !!! note

        To use different namespace, specify other name instead of
        `postgres-operator` in the above command, and modify the 
        `-n postgres-operator` parameter with it in the following two steps.
        You can also omit this parameter completely to deploy everything in the
        `default` namespace.

2. Deploy the Operator [using :octicons-link-external-16:](https://kubernetes.io/docs/reference/using-api/server-side-apply/)
    the following command:

    ``` {.bash data-prompt="$" }
    $ kubectl apply --server-side -f https://raw.githubusercontent.com/percona/percona-postgresql-operator/v{{ release }}/deploy/bundle.yaml -n postgres-operator
    ```

    ??? example "Expected output"

        ```{.text .no-copy}
        customresourcedefinition.apiextensions.k8s.io/perconapgbackups.pgv2.percona.com serverside-applied
        customresourcedefinition.apiextensions.k8s.io/perconapgclusters.pgv2.percona.com serverside-applied
        customresourcedefinition.apiextensions.k8s.io/perconapgrestores.pgv2.percona.com serverside-applied
        customresourcedefinition.apiextensions.k8s.io/postgresclusters.postgres-operator.crunchydata.com serverside-applied
        serviceaccount/percona-postgresql-operator serverside-applied
        role.rbac.authorization.k8s.io/percona-postgresql-operator serverside-applied
        rolebinding.rbac.authorization.k8s.io/service-account-percona-postgresql-operator serverside-applied
        deployment.apps/percona-postgresql-operator serverside-applied
        ```

    As the result you will have the Operator Pod up and running.

2. The operator has been started, and you can deploy your Percona Distribution
    for PostgreSQL cluster:

    ``` {.bash data-prompt="$" }
    $ kubectl apply -f https://raw.githubusercontent.com/percona/percona-postgresql-operator/v{{ release }}/deploy/cr.yaml -n postgres-operator
    ```

    ??? example "Expected output"

        ``` {.text .no-copy}
        perconapgcluster.pgv2.percona.com/cluster1 created
        ```

    !!! note

        This deploys default Percona Distribution for PostgreSQL configuration.
        Please see [deploy/cr.yaml :octicons-link-external-16:](https://raw.githubusercontent.com/percona/percona-postgresql-operator/v{{ release }}/deploy/cr.yaml)
        and [Custom Resource Options](operator.md) for the configuration
        options. You can clone the repository with all manifests and source code
        by executing the following command:

        ```{.bash data-prompt="$" }
        $ git clone -b v{{ release }} https://github.com/percona/percona-postgresql-operator
        ```

        After editing the needed options, apply your modified `deploy/cr.yaml`
        file as follows:

        ```{.bash data-prompt="$" }
        $ kubectl apply -f deploy/cr.yaml -n postgres-operator
        ```

    The creation process may take some time. When the process is over your
    cluster will obtain the `ready` status. You can check it with the following
    command:

    ``` {.bash data-prompt="$" }
    $ kubectl get pg
    ```

    ??? example "Expected output"

        --8<-- "kubectl-get-pg-response.txt"

## Verifying the cluster operation

When creation process is over, `kubectl get pg` command will show you the
cluster status as `ready`, and you can try to connect to the cluster.

{% include 'assets/fragments/connectivity.txt' %}

## Removing the cluster

If you need to delete the Operator and PostgreSQL cluster (for example, to clean
up the testing deployment before adopting it for production use), check
[this HowTo](delete.md).

To delete your Kubernetes cluster in EKS, you will need the following data:

* name of your EKS cluster,
* AWS region in which you have deployed your cluster.

You can clean up the cluster with the `eksctl` command as follows (with
real names instead of `<region>` and `<cluster name>` placeholders):

``` {.bash data-prompt="$" }
$ eksctl delete cluster --region=<region> --name="<cluster name>"
```

The cluster deletion may take time.

!!! warning

    After deleting the cluster, all data stored in it will be lost!
