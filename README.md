[![Latest Version](https://img.shields.io/github/release/softonic/mysql-backups.svg)](https://github.com/softonic/mysql-backups/releases)
[![Software License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)
[![Average time to resolve an issue](http://isitmaintained.com/badge/resolution/softonic/mysql-backups.svg)](http://isitmaintained.com/project/softonic/mysql-backups "Average time to resolve an issue")
![GitHub issues](https://img.shields.io/github/issues-raw/softonic/mysql-backups)

# Mysql Backup

### Overview

This chart aims to backup target mysql instances into object storage.

It can read data from a mysql instance or through GCP cloud SQL proxy.

This charts curretnly supports only AWS S3 as object storage.

| Parameter                                           | Description                                                   | Default                            |
| --------------------------------------------------- | ------------------------------------------------------------- | ---------------------------------- |
| `mysql.port`                                        | MySQL instance port                                           | `3306`                             |
| `mysql.host`                                        | MySQL instance host                                           | `127.0.0.1`                        |
| `mysql.database`                                    | MySQL instance database name                                  | `null`                             |
| `mysql.secret.name`                                 | MySQL credentials Secret name                                 | `mysql-credentials`                |
| `mysql.secret.keys.user`                            | MySQL user keys in credentials secret                         | `user`                             |
| `mysql.secret.keys.password`                        | MySQL password keys in credentials secret                     | `password`                         |
| `s3.enabled`                                        | Enable AWS s3 backups                                         | `false`                            |
| `s3.bucket`                                         | AWS s3 bucket name                                            | `null`                             |
| `s3.filePrefix`                                     | AWS s3 file prefix used for backup                            | `null`                             |
| `s3.secret.name`                                    | AWS s3 credentials Secret name                                | `mysql-credentials`                |
| `s3.secret.keys.accessKeyId`                        | AWS s3 accessKeyId keys in credentials secret                 | `accessKeyId`                      |
| `s3.secret.keys.secretAccessKey`                    | AWS s3 secretAccessKey keys in credentials secret             | `secretAccessKey`                  |
| `gcs.enabled`                                       | Enable GCP gcs backups                                        | `false`                            |
| `gcs.bucket`                                        | GCP gcs bucket name                                           | `null`                             |
| `gcs.filePrefix`                                    | GCP gcs file prefix used for backup                           | `mysqldump`                        |
| `backup.schedule`                                   | When to run the backup, cron format                           | `0 1 * * *`                        |
| `backup.image.repository`                           | Image used to perform the backup                              | `softonic/mysql-backup-s3`         |
| `backup.image.tag`                                  | Tag used for the image                                        | `0.1.5`                            |
| `backup.image.imagePullSecret`                      | Image pull secret                                             | `null`                             |
| `backup.resources.limits.memory`                    | Resources limits for memory for mysql-backups container       | `1024Mi`                           |
| `backup.resources.limits.cpu`                       | Resources limits for CPU for mysql-backups container          | `1`                                |
| `backup.resources.requests.memory`                  | Resources requests for memory for mysql-backups container     | `512Mi`                            |
| `backup.resources.requests.cpu`                     | Resources requests for cpu for mysql-backups container        | `200m`                             |
| `cloudsqlProxy.enabled`                             | Cloud SQL deployment enabled                                  | `false`                            |
| `cloudsqlProxy.cloudSQL.secret.name`                | Cloud SQL secret name                                         | `cloudsql-credentials`             |
| `cloudsqlProxy.cloudSQL.secret.keys.serviceAccount` | Cloud SQL Service account key for secret                      | `serviceAccount.json`              |
| `cloudsqlProxy.cloudSQL.gcProject`                  | Google Cloud Project from where to perform the backup         | `null`                             |
| `cloudsqlProxy.cloudSQL.instanceId`                 | Google Cloud SQL instance ID from where to perform the backup | `null`                             |
| `cloudsqlProxy.cloudSQL.region`                     | Google Cloud Region where the SQL instance ID is located in   | `null`                             |
| `cloudsqlProxy.cloudSQL.image.repository`           | cloudsql proxy image                                          | `gcr.io/cloudsql-docker/gce-proxy` |
| `cloudsqlProxy.cloudSQL.image.tag`                  | cloudsql proxy image tag                                      | `1.13`                             |
| `cloudsqlProxy.resources.limits.memory`             | Resources limits for memory for cloudsql-proxy container      | `32Mi`                             |
| `cloudsqlProxy.resources.limits.cpu`                | Resources limits for CPU for cloudsql-proxy container         | `1`                                |
| `cloudsqlProxy.resources.requests.memory`           | Resources requests for memory for cloudsql-proxy container    | `16Mi`                             |
| `cloudsqlProxy.resources.requests.cpu`              | Resources requests for cpu for cloudsql-proxy container       | `10m`                              |

### Usage

If we want to backup database `example` in mysql instance reachable at `10.0.0.2` everyday at 4am into AWS s3 buckets `example-backup` we can deploy this chart with values as below.

Note that secrets `example-db-credentials` and `aws-credentials` are already existing secrets.

```bash
mysql-backup:
  enabled: true
  mysql:
    host: 10.0.0.2
    database: example
    secret:
      name: example-db-credentials
      keys:
        user: db.backup.user
        password: db.backup.pass
  s3:
    enabled: true
    bucket: example-bucket
    filePrefix: my-prefix
    secret:
      name: aws-credentials
      keys:
        accessKeyId: accessKeyId
        secretAccessKey: secretAccessKey
  backup:
    schedule: 0 4 * * *
```
