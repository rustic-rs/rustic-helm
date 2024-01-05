# Rustic

[Rustic](https://rustic.cli.rs/) is a backup tool that provides fast, encrypted and deduplicated backups.

It reads and writes the [restic](https://github.com/restic/restic) repo format described in the [design document](https://github.com/restic/restic/blob/master/doc/design.rst) and can be used as a restic replacement in most cases.

## Introduction

This chart will set up rustic as a Kubernetes CronJob

## Installing

### Prerequisites

1. Kubernetes >=1.29 or 1.28 (with featureGate `SidecarContainers` enabled)
2. Helm v3 (Tested with v3.11.2)
3. Rustic's chart repository: `helm repo add mueckinger https://mueckinger.github.io/charts`


Bare minimum `values.yml`:
```
hostname: <myHost>

rustic:
  encryption_secret: <encryptionSecret>
  backup_volume:
    hostPath:
      path: /path/to/backupfolder

s3:
  repo:
    endpoint: s3.fr-par.scw.cloud
    access_key_id: <myS3AccessKeyID>
    secret_access_key: <mySecretS3AccessKey>
    region: fr-par
    bucket: <bucketName>
```

### Deploying rustic

```
helm install rustic mueckinger/rustic --set rustic.init=true -f values.yml
```

If there is already an initialized restic repository in the Bucket omit `--set rustic.init=true`:

```
helm install rustic mueckinger/rustic -f values.yml
```

### Examples