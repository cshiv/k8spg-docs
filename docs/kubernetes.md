# Install Percona Distribution for PostgreSQL on Kubernetes

Following steps will allow you to install the Operator and use it to manage
Percona Distribution for PostgreSQL in a Kubernetes-based environment.

1. First of all, clone the percona-postgresql-operator repository:

    ``` {.bash data-prompt="$" }
    $ git clone -b v{{ release }} https://github.com/percona/percona-postgresql-operator
    $ cd percona-postgresql-operator
    ```

    !!! note

    It is crucial to specify the right branch with `-b` option while cloning the
    code on this step. Please be careful.


2. Create the Kubernetes namespace for your cluster if needed (for example,
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

3. Deploy the Operator [using](https://kubernetes.io/docs/reference/using-api/server-side-apply/) the following command:

    ``` {.bash data-prompt="$" }
    $ kubectl apply --server-side  -f deploy/bundle.yaml -n postgres-operator
    ```

4. After the Operator is started Percona Distribution for PostgreSQL can be
    created at any time with the following command:

    ``` {.bash data-prompt="$" }
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

## Deleting the cluster

If you need to delete the cluster (for example, to clean up the testing
deployment before adopting it for production use), check [this HowTo](delete.md).
