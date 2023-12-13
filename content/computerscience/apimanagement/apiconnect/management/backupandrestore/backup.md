---
title: API Connect Backup Configuration
description: API Connect Backup Configuration
tags: ["apiconnect"]
---

# API Connect Backup Configuration

Backup and restore for API Connect subsystems are daily tasks that you have to manage to keep the systems healthy. 

API Connect v10 uses Kubernetes operators to backup and restore  databases of the subsystems. 

Type of the backups are manuel and scheduled.


## Manuel backups
For the subsystems, custom resources(CR) are used for the backup configuration. 

Therefore, you create a backup custom resource(CR) manually.

An example for management subsystem in OpenShift:

1. Check the subsystem is healthy in OpenShift:

```bash
oc get <subsystem-name> 
```

2. Create a backup custom resource(CR) in a backup.yaml file:

```yaml
apiVersion: management.apiconnect.ibm.com/v1beta1
kind: ManagementBackup
metadata:
  generateName:<mnagement-subsystem-name>
spec:
  type: full
  crType: create
  clusterName: management
```
3.  Create custom resource in OpenShift in a yaml file:

```bash 
oc create -f backup.yaml -n <management-namaspace>
```

4. List backups by using OpenShift cli:

```bash
oc get managementbackup -n <management-namespace>
```
## Scheduled backups

In order to trigger backups whenever you would like to, you create a cron schedule and configure backup configurations

in the management custom resource(CR).

An example for management subsystem in OpenShift:

1. Create backup secret in OpenShift to provide credentials that is used in sftp connection:

```bash
oc create secret generic <management-backup-secret-name> --from-literal=username='<username>' --from-literal=password='<password>' -n <management-namespace>
```

2. Edit management subsystem custom resource(CR).

```bash 
oc edit <management-custom-resource-name>
```

3. Add scheduled backup configuration to management custom resource yaml file:

```bash
spec:
  management:
    databaseBackup:
      protocol: sftp
      host: <sftp-host-name>
      port: <sftp-port>
      path: <backup-location>
      retries: 0
      credentials: <management-backup-secret-name>
      schedule: "30 11 * * *"
```

4) Check the backup location if the scheduled backup configuration works as expected:

## Troubleshooting the backup problems

1. Find the pod name that includes string backrest-shared-repo in OpenShift. 

If you are not sure about the namespace, you can use -A option to search for all namespaces.

```bash
oc get po -n <management-namespace> | grep backrest-shared-repo
```
2. Open a remote shell to pod:

```bash
oc rsh <backrest-shared-repo-pod-name>
````
``
3. Create a tmp file in /tmp:

```bash
touch tmp_file
```

4. SFTP to sftp server:

```bash
sftp -P 22 <username>@<sftp-server-name>
```

5. Change directory to the backup location that we configured in custom resource file:

```bash
cd <backup-location>
```

6. Transfer tmp file to the sftp server backup location:

```bash
put tmp_file
```
7. Check if the file is transferred successfully:

```bash
ls -l
```

## Steps to check if you face a problem during sftp connection:

1. Check the user has write access to the backup location that you configured.

2. Check if the sftp daemon is running or not on the sftp server.

