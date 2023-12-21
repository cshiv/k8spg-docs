# Use Docker images from a custom registry

Using images from a private Docker registry may be required for privacy, security
or other reasons. In these cases, Percona Operator for PostgreSQL allows the use
of a custom registry. The following example illustrates how this can be done by
the example of the Operator deployed in the OpenShift environment.

## Prerequisites 

1. First of all login to the OpenShift and create project.

    ``` {.bash data-prompt="$" }
    $ oc login
    Authentication required for https://192.168.1.100:8443 (openshift)
    Username: admin
    Password:
    Login successful.
    $ oc new-project pg
    Now using project "pg" on server "https://192.168.1.100:8443".
    ```

2. There are two things you will need to configure your custom registry access:

    * the token for your user,

    * your registry IP address.

     The token can be found with the following command:

    ``` {.bash data-prompt="$" }
    $ oc whoami -t
    ADO8CqCDappWR4hxjfDqwijEHei31yXAvWg61Jg210s
    ```

    And the following one tells you the registry IP address:

    ``` {.bash data-prompt="$" }
    $ kubectl get services/docker-registry -n default
    NAME              TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
    docker-registry   ClusterIP   172.30.162.173   <none>        5000/TCP   1d
    ```

3. Use the user token and the registry IP address to login to the registry:

    ``` {.bash data-prompt="$" }
    $ docker login -u admin -p ADO8CqCDappWR4hxjfDqwijEHei31yXAvWg61Jg210s 172.30.162.173:5000
    ```

    ??? example "Expected output"

        ``` {.text .no-copy}
        Login Succeeded
        ```

4. Use the Docker commands to pull the needed image by its SHA digest:

    ``` {.bash data-prompt="$" }
    $ docker pull docker.io/perconalab/percona-postgresql-operator@sha256:991d6049059e5eb1a74981290d829a5fb4ab0554993748fde1e67b2f46f26bf0
    ```

    ??? example "Expected output"

        ``` {.text .no-copy}
        Trying to pull repository docker.io/perconalab/percona-postgresql-operator ...
        sha256:991d6049059e5eb1a74981290d829a5fb4ab0554993748fde1e67b2f46f26bf0: Pulling from docker.io/perconalab/percona-server-mongodb
        Digest: sha256:991d6049059e5eb1a74981290d829a5fb4ab0554993748fde1e67b2f46f26bf0
        Status: Image is up to date for docker.io/perconalab/percona-postgresql-operator@sha256:991d6049059e5eb1a74981290d829a5fb4ab0554993748fde1e67b2f46f26bf0
        ```

    You can find correct names and SHA digests in the
    [current list of the Operator-related images officially certified by Percona](images.md#custom-registry-images).

5. The following method can push an image to the custom registry for the example
    OpenShift `pg` project:

    ``` {.bash data-prompt="$" }
    $ docker tag \
        docker.io/perconalab/percona-postgresql-operator@sha256:991d6049059e5eb1a74981290d829a5fb4ab0554993748fde1e67b2f46f26bf0 \
        172.30.162.173:5000/psmdb/percona-postgresql-operator:{{ postgresrecommended }}
    $ docker push 172.30.162.173:5000/pg/percona-postgresql-operator:{{ postgresrecommended }}
    ```

6. Verify the image is available in the OpenShift registry with the following command:

    ``` {.bash data-prompt="$" }
    $ oc get is
    ```

    ??? example "Expected output"

        ``` {.text .no-copy}
        NAME                              DOCKER REPO                                                             TAGS             UPDATED
        percona-postgresql-operator       docker-registry.default.svc:5000/pg/percona-postgresql-operator  {{ postgresrecommended }}  2 hours ago
        ```

7. When the custom registry image is available, edit the the `image:` option in
    `deploy/operator.yaml` configuration file with a Docker Repo + Tag string
    (it should look like `docker-registry.default.svc:5000/pg/percona-postgresql-operator:{{ postgresrecommended }}`)

    !!! note

        If the registry requires authentication, you can specify the `imagePullSecrets` option for all images.

8. Repeat steps 3-5 for other images, and update corresponding options in the
    `deploy/cr.yaml` file.

9. Now follow the standard Percona Operator for PostgreSQL [installation instruction](./openshift.md).
