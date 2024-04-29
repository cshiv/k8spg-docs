# Configure backup encryption

Backup encryption is a security best practice that helps protect your organization's confidential information and prevents unauthorized access.

The pgBackRest tool used by the Operator allows encrypting backups using AES-256
encryption. The approach is **repository-based**: pgBackRest encrypts the whole
repository where it stores backups. Encryption is enabled if a
user-supplied encryption key was passed to pgBackRest with the
`-repo-cypher-pass` option *when configuring the backup storage*.

<i warning>:material-alert: Limitation:</i> You cannot change encryption settings after the backups are established. You must create a new repository to enable encryption or change the encryption key.

This document describes how to configure backup encryption.

## Generate the encryption key

You should use a long, random encryption key. You can generate it using OpenSSL as follows:

```{.bash data-prompt="$"}
$ openssl rand -base64 48
```

## Configure backup storage

Follow the general [backup storage configuration](backups-storage.md)
instruction relevant to the backup storage you are using. The only difference is in encoding your cloud credentials and the pgBackRest repository name to be used for backups: you also add the encryption key to the configuration file as the `repo-cipher-pass` option. The repo name within the option must match the pgBackRest repo name.

The following example shows the configuration for S3-compatible storage and the pgBackRest repo name `repo2` (other cloud storages are configured similarly).

1. Encode the storage configuration file.

    === ":simple-linux: Linux"         

          ``` {.bash data-prompt="$" }
          $ cat <<EOF | base64 --wrap=0
          [global]
          repo2-s3-key=<YOUR_AWS_S3_KEY>
          repo2-s3-key-secret=<YOUR_AWS_S3_KEY_SECRET>
          repo2-cipher-pass=<YOUR_ENCRYPTION_KEY>
          EOF
          ```         

    === ":simple-apple: macOS"         

         ``` {.bash data-prompt="$" }
         $ cat <<EOF | base64
         [global]
         repo2-s3-key=<YOUR_AWS_S3_KEY>
         repo2-s3-key-secret=<YOUR_AWS_S3_KEY_SECRET>
         repo2-cipher-pass=<YOUR_ENCRYPTION_KEY>
         EOF
         ``` 

2. Create the Secrets configuration file and the Secrets object as described in steps 2-3 of the [S3-compatible backup storage configuration](backups-storage.md). Follow the instructions relevant to the backup storage you are using. 

3. Update the `deploy/cr.yaml` configuration. Specify the following information:

     * The Secret name you created in the `backups.pgbackrest.configuration` subsection
     * All storage-related information in the `backups.pgbackrest.repos` subsection under the repository name that you intend to use for backups. This name must match the name you used when you encoded S3 credentials on step 1. 
     * The cipher type in the `pgbackrest.global` subsection

    The following example shows the configuration for the S3-compatible storage and the pgBackRest repo name `repo2`:

    ```yaml
    backups:
      pgbackrest:
        ...
        configuration:
          - secret:
              name: cluster1-pgbackrest-secrets
        ...
        repos:
        - name: repo2
          s3:
            bucket: "<YOUR_AWS_S3_BUCKET_NAME>"
            endpoint: "<YOUR_AWS_S3_ENDPOINT>"
            region: "<YOUR_AWS_S3_REGION>"
        global:
          cipher-type: aes-256-cbc
    ```

4. Apply the changes. Replace the `<namespace>` placeholder with your value.

    ```{.bash data-prompt="$"}
    $ kubectl apply -f deploy/cr.yaml -n <namespace>
    ```


## Make a backup

[Make an on-demand backup](backups-ondemand.md){.md-button}
[Make a scheduled backup](backups-schedule.md){.md-button}
