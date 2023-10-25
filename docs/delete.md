# Delete Percona Operator for PostgreSQL

You may want to delete any of the following objects:

* Database cluster managed by Percona Operator for PostgreSQL
* Percona Operator for PostgreSQL itself
* Custom Resource Definition deployed with the Operator

## Delete the database cluster

You can delete the Percona Distribution for PosgreSQL cluster managed by the
Operator by deleting the appropriate Custom Resource.

1. List Custom Resources, replacing the `<namespace>` placeholder with your
    namespace.
    
    ```{.bash data-prompt="$"}
    $ kubectl get pg -n <namespace>
    ```

    ??? example "Sample output"

        --8<-- "./docs/assets/code/kubectl-get-pg-response.txt"

2. Delete the Custom Resource with the name of your cluster (for example, let's
    use the default `cluster1` name).

    ```{.bash data-prompt="$"}
    $ kubectl delete pg cluster1 -n <namespace>
    ```

    ??? example "Sample output"

        ```{.text .no-copy}
        perconapgcluster.pgv2.percona.com "cluster1" deleted
        ```

3. Check that the cluster is deleted by listing the available Custom Resources
    once again.

    ```{.bash data-prompt="$"}
    $ kubectl get pg -n <namespace>
    ``` 

    ??? example "Sample output"

        ```{.text .no-copy}
        No resources found in <namespace> namespace.
        ```

## Delete the Operator

You can uninstall the Operator by deleting the [Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
related to it.

1. List the deployments. Replace the `<namespace>` placeholder with your
    namespace.
    
    ```{.bash data-prompt="$"}
    $ kubectl get deploy -n <namespace>
    ```

    ??? example "Sample output"

        ```{.text .no-copy}
        NAME                          READY   UP-TO-DATE   AVAILABLE   AGE
        percona-postgresql-operator   1/1     1            1           13m
        ```

2. Delete the `percona-*` deployment

    ```{.bash data-prompt="$"}
    $ kubectl delete deploy percona-postgresql-operator -n <namespace>
    ```

3. Check that the Operator is deleted by listing the Pods. As a result you
    should have no Pods related to it.

    ```{.bash data-prompt="$"}
    $ kubectl get pods -n <namespace>
    ``` 

    ??? example "Sample output"

        ```{.text .no-copy}
        No resources found in <namespace> namespace.
        ```

## Delete Custom Resource Definition

If you are not just deleting the Operator and PostgreSQL cluster from a specific
namespace, but want to completely clean up your Kubernetes cluster,
you can also delete the [CustomRecourceDefinitions (CRDs)](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions). 

CRDs in Kubernetes are non-namespaced but are available to the whole
environment. So you shouldn't delete CRD if you still have the Operator and
database cluster in some other namespace.

1. List the CRDs:

    ```{.bash data-prompt="$"}
    $ kubectl get crd
    ```

    ??? example "Sample output"

        ```{.text .no-copy}
        allowlistedv2workloads.auto.gke.io                   2023-09-07T14:15:30Z
        allowlistedworkloads.auto.gke.io                     2023-09-07T14:15:29Z
        audits.warden.gke.io                                 2023-09-07T14:15:32Z
        backendconfigs.cloud.google.com                      2023-09-07T14:15:41Z
        capacityrequests.internal.autoscaling.gke.io         2023-09-07T14:15:25Z
        frontendconfigs.networking.gke.io                    2023-09-07T14:15:41Z
        managedcertificates.networking.gke.io                2023-09-07T14:15:41Z
        memberships.hub.gke.io                               2023-09-07T14:15:30Z
        perconapgbackups.pgv2.percona.com                    2023-09-07T14:28:59Z
        perconapgclusters.pgv2.percona.com                   2023-09-07T14:29:02Z
        perconapgrestores.pgv2.percona.com                   2023-09-07T14:29:03Z
        postgresclusters.postgres-operator.crunchydata.com   2023-09-07T14:29:06Z
        serviceattachments.networking.gke.io                 2023-09-07T14:15:44Z
        servicenetworkendpointgroups.networking.gke.io       2023-09-07T14:15:43Z
        storagestates.migration.k8s.io                       2023-09-07T14:15:53Z
        storageversionmigrations.migration.k8s.io            2023-09-07T14:15:53Z
        updateinfos.nodemanagement.gke.io                    2023-09-07T14:15:55Z
        volumesnapshotclasses.snapshot.storage.k8s.io        2023-09-07T14:15:52Z
        volumesnapshotcontents.snapshot.storage.k8s.io       2023-09-07T14:15:52Z
        volumesnapshots.snapshot.storage.k8s.io              2023-09-07T14:15:52Z
        ```

2. Now delete the `percona*.pgv2.percona.com` CRDs:

    ```{.bash data-prompt="$"}
    $ kubectl delete crd perconapgbackups.pgv2.percona.com perconapgclusters.pgv2.percona.com perconapgrestores.pgv2.percona.com
    ```

    ??? example "Sample output"

        ```{.text .no-copy}
        customresourcedefinition.apiextensions.k8s.io "perconapgbackups.pgv2.percona.com" deleted
        customresourcedefinition.apiextensions.k8s.io "perconapgclusters.pgv2.percona.com" deleted
        customresourcedefinition.apiextensions.k8s.io "perconapgrestores.pgv2.percona.com" deleted
        ```
