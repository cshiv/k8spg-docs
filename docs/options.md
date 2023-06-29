# Changing PostgreSQL Options

Despite the Operator's ability to configure PostgreSQL and the large number of
Custom Resource options, there may be situations where you need to pass specific
options directly to your cluster's PostgreSQL instances. For this purpose, you
can use the [PostgreSQL dynamic configuration method](https://patroni.readthedocs.io/en/latest/dynamic_configuration.html)
provided by Patroni. You can pass PostgreSQL options to Patroni through the 
Operator Custom Resource, updating it with `deploy/cr.yaml` configuration file).

Custom PostgreSQL configuration options should be included into the
`patroni.dynamicConfiguration.postgresql.parameters` subsection as follows:

```yaml
...
patroni:
  dynamicConfiguration:
    postgresql:
      parameters:
        max_parallel_workers: 2
        max_worker_processes: 2
        shared_buffers: 1GB
        work_mem: 2MB
```

Please note that configuration changes will be automatically applied to the
running instances as soon as you apply Custom Resource changes in a usual way,
running the `kubectl apply -f deploy/cr.yaml` command.

You can apply custom configuration in this way for both new and existing clusters.

Normally, options should be applied to PostgreSQL instances dynamically without
restart, except [the options with the postmaster context](https://www.postgresql.org/docs/15/view-pg-settings.html).
Changing options which have `context=postmaster` will cause Patroni to initiate
restart of all PostgreSQL instances, one by one. You can check the context of
a specific option using the `SELECT name, context FROM pg_settings;` query to
to see if the change should cause a restart or not.

!!! note

    The Operator passes options to Patroni without validation, so there is a
    theoretical possibility of the cluster malfunction caused by wrongly
    configured PostgreSQL instances. Also, this configuration method is used
    for PostgreSQL options only and cannot be applied to change other 
    [Patroni dynamic configuration options](https://patroni.readthedocs.io/en/latest/dynamic_configuration.html).
    It means that options in the `parameters` subsection under
    `patroni.dynamicConfiguration.postgresql` will be applied, and everything
    else in `patroni.dynamicConfiguration.postgresql` will be ignored.
