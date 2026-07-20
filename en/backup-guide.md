<!-- pre-align:aligned sig=dd3a912dfca5 -->

<a id="container-nhn-kubernetes-service-nks-backup-guide"></a>
## Container > NHN Kubernetes Service (NKS) > Backup Guide { #container-nhn-kubernetes-service-nks-backup-guide }

<a id="overview"></a>
## Overview { #overview }

If you need a backup of your NHN Kubernetes Service (NKS) cluster, you can use the Velero plugin to back it up to Object Storage.
This document describes how to back up and restore a cluster using Object Storage and Velero.

* Glossary
    * Backup cluster: A cluster to be backed up.
    * Restore cluster: A cluster that is restored using the backed up content.

For more information on Velero, refer to [Velero Docs](https://velero.io/docs/v1.9/).

<a id="cluster-backup-and-restoration-with-velero"></a>
## Cluster Backup and Restoration with Velero { #cluster-backup-and-restoration-with-velero }

<a id="prerequisites"></a>
### Prerequisites { #prerequisites }

To use the Object Storage API, you must check the tenant ID and API endpoint, and set the API password and create Temporary URL key.

<a id="prerequisites-check-the-tenant-id-and-api-endpoint"></a>
#### Check the Tenant ID and API Endpoint

You can check the tenant ID and API endpoint by clicking the **Set API Endpoint** button on the Object Storage service page.

| Item | API Endpoint | Usage |
| --- | --- | --- |
| Identity | https://api-identity-infrastructure.nhncloudservice.com/v2.0 | Issue an authentication token |
| Tenant ID | 32 character string consisting of numbers and alphabets | Issue an authentication token |

<a id="prerequisites-set-an-api-password"></a>
#### Set an API password

You can set the API password by clicking the **Set API Endpoint** button on the Object Storage service page.

1. Click **Set API Endpoint**.
2. In the **Set API Password** input box under **API Endpoint Settings**, enter the password to be used when issuing tokens.
3. Click **Save**.

For more information about the Object Storage API, see the [Object Storage API Guide](/Storage/Object%20Storage/en/api-guide/).

<a id="prerequisites-create-temporary-url-key"></a>
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

<a id="install-the-velero-client"></a>
### Install the Velero Client { #install-the-velero-client }

The Velero client is a program where you can enter the cluster's backup and restore commands.
You can download the Velero client from the Velero Github repository and use it for cluster backup and restoration. Before running the downloaded Velero client command, you must download the kubeconfig file of the backup and restore clusters from the web console, and **set the KUBECONFIG environment variable to specify the target clusters for backup and restoration exactly**.
For more information on kubeconfig settings, see [Installing kubectl](/Container/NKS/en/user-guide/#kubectl).

<a id="install-the-velero-client-download-the-velero-client"></a>
#### Download the Velero Client

```
$ wget https://github.com/vmware-tanzu/velero/releases/download/v1.17.0/velero-v1.17.0-linux-amd64.tar.gz
```

<a id="install-the-velero-client-decompress-the-file"></a>
#### Decompress the File

```
$ tar xzf velero-v1.17.0-linux-amd64.tar.gz
```

<a id="install-the-velero-client-change-the-location-or-set-the-path"></a>
#### Change the Location or Set the Path

Move the file to the path specified in the environment variable so that you can run the Velero client from any path, or add the path where Velero is located to the environment variable.

* Change the location to the path specified in the environment variable

```
$ sudo mv velero-v1.17.0-linux-amd64/velero /usr/local/bin
```

* Add the path to environment variable

```
$ export PATH=$PATH:$(pwd)
```

<a id="install-the-velero-server"></a>
### Install the Velero Server { #install-the-velero-server }

Install the Velero server using Helm.

<a id="install-the-velero-server-download-helm"></a>
#### Download Helm

```
$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
```

<a id="install-the-velero-server-change-a-permission"></a>
#### Change a Permission

The downloaded file does not have execute permission by default. Add an execute permission.

```
$ chmod 700 get_helm.sh
```

<a id="install-the-velero-server-install-helm"></a>
#### Install Helm

```
$ ./get_helm.sh
```

<a id="install-the-velero-server-add-the-helm-repository"></a>
#### Add the Helm Repository

To install the Velero server, you need to add the Helm repository.

```
$ helm repo add vmware-tanzu https://vmware-tanzu.github.io/helm-charts
```

<a id="install-the-velero-server-2"></a>
#### Install the Velero Server

The Velero server must be installed on a `backup cluster` and a `restore cluster` respectively. We recommend that you install using `the same helm command on both clusters` to use the same Object Storage.

##### Create an Object Storage S3 authentication secret

In the Object Storage console, go to **S3 API Credentials** to create an access_key and secret_key, then create a file as follows:
```
cat > credentials-velero <<EOF
[default]
aws_access_key_id=${access_key 값}
aws_secret_access_key=${secret_key 값}
EOF
```

Creates a secret to be used for authentication when accessing Object Storage from Velero.
```
kubectl create namespace velero --dry-run=client -o yaml | kubectl apply -f -
kubectl create secret generic cloud-credentials \
  --namespace velero \
  --from-file=cloud=credentials-velero
```

##### velero install helm

```
$ helm install velero vmware-tanzu/velero \
--namespace velero --create-namespace \
--version 11.0.0 \
--set snapshotsEnabled=false \
--set credentials.useSecret=true \
--set credentials.existingSecret=cloud-credentials \
--set deployNodeAgent=true \
--set-string configuration.backupStorageLocation[0].config.checksumAlgorithm="" \
--set configuration.defaultVolumesToFsBackup=true \
--set configuration.uploaderType=kopia \
--set initContainers[0].name=velero-plugin-for-aws \
--set initContainers[0].image=velero/velero-plugin-for-aws:v1.13.2 \
--set initContainers[0].volumeMounts[0].name=plugins \
--set initContainers[0].volumeMounts[0].mountPath=/target \
--set configuration.defaultRepoMaintainFrequency=1m \
--set configuration.backupStorageLocation[0].name=default \
--set configuration.backupStorageLocation[0].provider=aws \
--set-string configuration.backupStorageLocation[0].bucket={Container} \
--set-string configuration.backupStorageLocation[0].config.region="{Region}" \
--set-string configuration.backupStorageLocation[0].config.s3Url="${OBS endpoint}" \   
--set-string configuration.backupStorageLocation[0].config.s3ForcePathStyle=true
```

| Item | Description |
| --- | --- |
| Container | Name of the container used in Object Storage |
| Region | Korea (Pangyo) Region: `KR1`<br>Korea (Pyeongchon) Region: `KR2`<br>Korea (Gwangju) Region: `KR3` |
| OBS endpoint | Object Storage API Endpoint |

<a id="install-the-velero-server-delete-the-velero-server"></a>
#### Delete the Velero Server
You can uninstall the Velero server with the `velero uninstall` command.

<a id="back-up-a-cluster"></a>
### Back up a Cluster { #back-up-a-cluster }

You can configure a cluster backup with the `velero backup create` command.

```
$ export KUBECONFIG={kubeconfig file of the backup cluster}
$ velero backup create {name} --exclude-namespaces kube-system,velero
```

* Specify the name as the name of the backup you want.
* You can set up resource filtering when backing up a cluster. See [resource-filtering](https://velero.io/docs/v1.17/resource-filtering/) for details.

> [Caution]
> You must exclude the namespaces that do not require backup such as `kube-system` and `velero`.
> If such namespaces are included in a backup, a problem might occur while performing restoration.

You can check the cluster backup status with the `velero backup get` command.

```
$ velero backup get
NAME         STATUS      ERRORS   WARNINGS   CREATED                         EXPIRES   STORAGE LOCATION   SELECTOR
my-backup    Completed   0        0          2025-10-13 11:01:53 +0900 KST   29d       default            <none>
```

* You can view the backed up information on the Object Storage service page.

<a id="restore-a-cluster"></a>
### Restore a Cluster { #restore-a-cluster }

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

<a id="examples"></a>
### Examples { #examples }

<a id="examples-example-of-cluster-backup-and-restoration"></a>
#### Example of Cluster Backup and Restoration

* Perform backup using the velero backup create command on the backup cluster.

```
$ velero backup create my-backup --exclude-namespaces kube-system,velero
```

* Check the backup status using the velero backup get command.

```
$ velero backup get
NAME         STATUS      ERRORS   WARNINGS   CREATED                         EXPIRES   STORAGE LOCATION   SELECTOR
my-backup    Completed   0        0          2025-10-13 11:01:53 +0900 KST   29d       default            <none>
```

* Perform restoration using the velero restore create command on the restore cluster.

```
$ velero restore create --from-backup my-backup
```

* Use kubectl to check restoration information.

```
$ kubectl get pod --all-namespaces
```

<a id="examples-example-of-setting-periodic-backups"></a>
#### Example of Setting Periodic Backups

You can configure periodic backups with the `velero schedule create` command. See [schedule-a-backup](https://velero.io/docs/v1.17/backup-reference/#schedule-a-backup) for details.

* In the backup cluster, use the velero schedule create command to configure periodic backups. (Example is every 10 minutes)

```
$ velero schedule create my-schedule --schedule="*/10 * * * *" --exclude-namespaces kube-system,velero
```

* You can use the velero backup get command to check that the backup is performed at the set time interval.

```
$ velero backup get
NAME                         STATUS      ERRORS   WARNINGS   CREATED                         EXPIRES   STORAGE LOCATION   SELECTOR
my-schedule-20251013055022   Completed   0        0          2025-10-13 14:50:22 +0900 KST   29d       default            <none>
my-schedule-20251013054022   Completed   0        0          2025-10-13 14:40:22 +0900 KST   29d       default            <none>
```

<a id="examples-example-of-clearing-periodic-backups"></a>
#### Example of Clearing Periodic Backups
Periodic backups can be cleared with the `velero schedule delete` command.

* Check the configuration information using the velero schedule get command on the backup cluster.

```
$ velero schedule get
NAME          STATUS    CREATED                         SCHEDULE       BACKUP TTL   LAST BACKUP   SELECTOR   PAUSED
my-schedule   Enabled   2025-10-13 14:38:11 +0900 KST   */10 * * * *   0s           18s ago       <none>     false
```


* Clear the periodic backup setting using the velero schedule delete command.

```
$ velero schedule delete my-schedule
Are you sure you want to continue (Y/N)? y
Schedule deleted: my-schedule
```
