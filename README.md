# Rustic

[Rustic](https://rustic.cli.rs/) is a backup tool that provides fast, encrypted and deduplicated backups.

It reads and writes the [restic](https://github.com/restic/restic) repo format described in the [design document](https://github.com/restic/restic/blob/master/doc/design.rst) and can be used as a restic replacement in most cases.

## Introduction

This chart installs `rustic backup` as [Kubernetes CronJob](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/), which will run a [Kubernetes Job](https://kubernetes.io/docs/concepts/workloads/controllers/job/) periodically on a given schedule. It can optionally initialize a new repository by creating a single Job running `rustic init`.

## Installing

### Prerequisites

* Kubernetes >=1.29 or 1.28 (with featureGate `SidecarContainers` enabled)
* Helm v3 (Tested with v3.11.2)
* Rustic's chart repository: `helm repo add rustic https://mueckinger.github.io/rustic-helm/charts`


Bare minimum `values.yml`:
```yaml
# Kubernetes assigns random hostnames for Pods by default. Please set the Pods hostname to a fixed value, otherwise incremental backups won't work.
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

This will trigger a daily backup of the nodes </path/to/backupfolder> to the given S3 bucket at 1:00 am (default schedule). 

### Deploying rustic

```
helm install rustic rustic/rustic --set rustic.init=true -f values.yml
```

If there is already an initialized restic repository in the Bucket omit `--set rustic.init=true`:

```
helm install rustic rustic/rustic -f values.yml
```

### Examples