## Container > NHN Kubernetes Service (NKS) > Backup Guide

## Overview

If you need a backup of your NHN Kubernetes Service (NKS) cluster, you can use the Velero plugin to back it up to Object Storage.
This document describes how to back up and restore a cluster using Object Storage and Velero.

* Glossary
    * Backup cluster: A cluster to be backed up.
    * Restore cluster: A cluster that is restored using the backed up content.

For more information on Velero, refer to [Velero Docs](https://velero.io/docs/v1.9/).

## Cluster Backup and Restoration with Velero

### Prerequisites

To use the Object Storage API, you must check the tenant ID and API endpoint, and set the API password and create Temporary URL key.

#### Check the Tenant ID and API Endpoint

You can check the tenant ID and API endpoint by clicking the **Set API Endpoint** button on the Object Storage service page.

| Item | API Endpoint | Usage |
| --- | --- | --- |
| Identity | https://api-identity-infrastructure.nhncloudservice.com/v2.0 | Issue an authentication token |
| Tenant ID | 32 character string consisting of numbers and alphabets | Issue an authentication token |

#### Set an API password

You can set the API password by clicking the **Set API Endpoint** button on the Object Storage service page.

1. Click **Set API Endpoint**.
2. In the **Set API Password** input box under **API Endpoint Settings**, enter the password to be used when issuing tokens.
3. Click **Save**.

For more information about the Object Storage API, see the [Object Storage API Guide](/Storage/Object%20Storage/en/api-guide/).

#### Create Temporary URL Key

To use the `velero log` command in the Velero client, you must create a Temporary URL Key in Object Storage.

1. [Obtain bject Storage Authentication Token](/Storage/Object%20Storage/en/api-guide/#_2).
2. Click **Set API Endpoint** to check the Object Storage URL of the service.
3. Create Temporary URL Key using the API.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| X-Auth-Token | Header | String | O | Token ID |
| X-Account-Meta-Temp-Url-Key | Header | String | O | Key information used in Temporary |

```
$ curl -X POST {Object Store} -H "X-Auth-Token: {tokenId}" -H "X-Account-Meta-Temp-Url-Key: {key}"
```

### Install the Velero Client

The Velero client is a program where you can enter the cluster's backup and restore commands.
You can download the Velero client from the Velero Github repository and use it for cluster backup and restoration. Before running the downloaded Velero client command, you must download the kubeconfig file of the backup and restore clusters from the web console, and **set the KUBECONFIG environment variable to specify the target clusters for backup and restoration exactly**.
For more information on kubeconfig settings, see [Installing kubectl](/Container/NKS/en/user-guide/#kubectl).

#### Download the Velero Client

```
$ wget https://github.com/vmware-tanzu/velero/releases/download/v1.9.4/velero-v1.9.4-linux-amd64.tar.gz
```

#### Decompress the File

```
$ tar xzf velero-v1.9.4-linux-amd64.tar.gz
```

#### Change the Location or Set the Path

Move the file to the path specified in the environment variable so that you can run the Velero client from any path, or add the path where Velero is located to the environment variable.

* Change the location to the path specified in the environment variable

```
$ sudo mv velero-v1.9.4-linux-amd64/velero /usr/local/bin
```

* Add the path to environment variable

```
$ export PATH=$PATH:$(pwd)
```

### Install the Velero Server

Install the Velero server using Helm.

#### Download Helm

```
$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
```

#### Change a Permission

The downloaded file does not have execute permission by default. Add an execute permission.

```
$ chmod 700 get_helm.sh
```

#### Install Helm

```
$ ./get_helm.sh
```

#### Add the Helm Repository

To install the Velero server, you need to add the Helm repository.

```
$ helm repo add vmware-tanzu https://vmware-tanzu.github.io/helm-charts
```

#### Install the Velero Server

The Velero server must be installed on a `backup cluster` and a `restore cluster` respectively. We recommend that you install using `the same helm command on both clusters` to use the same Object Storage.

```
$ helm install velero vmware-tanzu/velero \
--namespace velero \
--create-namespace \
--version 2.32.6 \
--set configuration.provider=community.openstack.org/openstack \
--set initContainers[0].name=velero-plugin-for-openstack \
--set initContainers[0].image=lirt/velero-plugin-for-openstack:v0.3.0 \
--set initContainers[0].volumeMounts[0].mountPath=/target \
--set initContainers[0].volumeMounts[0].name=plugins \
--set deployRestic=true \
--set configuration.defaultVolumesToRestic=true \
--set configuration.defaultResticPruneFrequency=0h1m0s \
--set configuration.backupStorageLocation.bucket={Container} \
--set configuration.backupStorageLocation.config.region={Region} \
--set configuration.backupStorageLocation.config.resticRepoPrefix=swift:{Container}:/restic \
--set configuration.extraEnvVars.OS_AUTH_URL={Identity service (Identity)} \
--set configuration.extraEnvVars.OS_TENANT_ID={Tenant ID} \
--set configuration.extraEnvVars.OS_USERNAME={NHN Cloud ID} \
--set configuration.extraEnvVars.OS_PASSWORD={API password} \
--set configuration.extraEnvVars.OS_REGION_NAME={Region} \
--set configuration.extraEnvVars.OS_DOMAIN_ID=default
```

| Item | Description |
| --- | --- |
| Container | Name of the container used in Object Storage |
| Region | Korea (Pangyo) Region: `KR1`<br>Korea (Pyeongchon) Region: `KR2` |
| Identity service | Identity service in API Endpoint settings |
| Tenant ID | Tenant ID in API Endpoint Settings |
| NHN Cloud ID | NHN Cloud ID |
| API password | API password entered in API Endpoint settings |

#### Delete the Velero Server
You can uninstall the Velero server with the `velero uninstall` command.

### Back up a Cluster

You can configure a cluster backup with the `velero backup create` command.

```
$ export KUBECONFIG={kubeconfig file of the backup cluster}
$ velero backup create {name} --exclude-namespaces kube-system,velero
```

* Specify the name as the name of the backup you want.
* You can set up resource filtering when backing up a cluster. See [resource-filtering](https://velero.io/docs/v1.7/resource-filtering/) for details.

> [Caution]
> You must exclude the namespaces that do not require backup such as `kube-system` and `velero`.
> If such namespaces are included in a backup, a problem might occur while performing restoration.

You can check the cluster backup status with the `velero backup get` command.

```
$ velero backup get
NAME         STATUS      ERRORS   WARNINGS   CREATED                         EXPIRES   STORAGE LOCATION   SELECTOR
my-backup    Completed   0        0          2022-02-09 10:13:44 +0900 KST   29d       default            <none>
```

* You can view the backed up information on the Object Storage service page.

### Restore a Cluster

You can configure a cluster backup/restoration with the `velero restore create` command.

```
$ export KUBECONFIG={kubeconfig file of the restore cluster}
$ velero restore create --from-backup {name}
```

* If you specify a backup name in the name, the cluster is restored according to the content of the backup.

> [Caution]
> Since StorageClass resources are not backed up and restored, you must create a storage class with the same name as the one existing in the `backup cluster` in the `restore cluster` before restoration.

> [Caution]
> If the versions of the `backup cluster` and the `restore cluster` are different, problems may occur during restoration.

### Examples

#### Example of Cluster Backup and Restoration

* Perform backup using the velero backup create command on the backup cluster.

```
$ velero backup create my-backup --exclude-namespaces kube-system,velero
```

* Check the backup status using the velero backup get command.

```
$ velero backup get
NAME         STATUS      ERRORS   WARNINGS   CREATED                         EXPIRES   STORAGE LOCATION   SELECTOR
my-backup    Completed   0        0          2022-02-09 13:23:13 +0900 KST   29d       default            <none>
```

* Perform restoration using the velero restore create command on the restore cluster.

```
$ velero restore create --from-backup my-backup
```

* Use kubectl to check restoration information.

```
$ kubectl get pod --all-namespaces
```

#### Example of Setting Periodic Backups

You can configure periodic backups with the `velero schedule create` command. See [schedule-a-backup](https://velero.io/docs/v1.7/backup-reference/#schedule-a-backup) for details.

* In the backup cluster, use the velero schedule create command to configure periodic backups. (Example is every 10 minutes)

```
$ velero schedule create my-schedule --schedule="*/10 * * * *" --exclude-namespaces kube-system,velero
```

* You can use the velero backup get command to check that the backup is performed at the set time interval.

```
$ velero backup get
NAME                          STATUS      ERRORS   WARNINGS   CREATED                         EXPIRES   STORAGE LOCATION   SELECTOR
my-schedule-20220209044049    Completed   0        0          2022-02-09 13:40:49 +0900 KST   29d       default            <none>
my-schedule-20220209043115    Completed   0        0          2022-02-09 13:31:15 +0900 KST   29d       default            <none>
```

#### Example of Clearing Periodic Backups
Periodic backups can be cleared with the `velero schedule delete` command.

* Check the configuration information using the velero schedule get command on the backup cluster.

```
$ velero schedule get
NAME          STATUS    CREATED                         SCHEDULE       BACKUP TTL   LAST BACKUP   SELECTOR
my-schedule   Enabled   2022-03-17 13:48:53 +0900 KST   */10 * * * *   720h0m0s     4s ago        <none>
```


* Clear the periodic backup setting using the velero schedule delete command.

```
$ velero schedule delete my-schedule
Are you sure you want to continue (Y/N)? y
Schedule deleted: my-schedule
```
