## Container > NHN Kubernetes Service (NKS) > API v2 Guide

This document describes the API for configuring a Kubernetes cluster.
NKS uses IaaS tokens for authentication and authorization when calling APIs. IaaS tokens are authentication tokens used in NHN Cloud's OpenStack-based infrastructure service (IaaS). For more information on IaaS token issuance and usage, see [IaaS Tokens](/nhncloud/en/public-api/iaas-token-gov).

All APIs are called using the `kubernetes` type endpoint.

| Type | Region | Endpoint |
|---|---|---|
| kubernetes | Korea (Pangyo) Region | https://kr1-api-kubernetes-infrastructure.gov-nhncloudservice.com |
| kubernetes | Korea (Pyeongchon) Region | https://kr2-api-kubernetes-infrastructure.gov-nhncloudservice.com |


Response fields not specified in this guide may appear in API responses. These fields are for internal use by NHN Cloud and may be changed without prior notice, so they should not be used.

## Check Resource Information Used in the API

NHN Kubernetes Service (NKS) API uses various resources for cluster and node group configuration. The following describes how to check resource information by resource.

### VPC Network UUID Connected to Internet Gateway

The VPC network connected to the internet gateway can be queried using the **router:external=True** query parameter in the VPC network list query API.

```
GET /v2.0/networks?router:external=True
```

For more detailed information about the network list query API, see [View network list](/Network/VPC/en/public-api-gov/#_2).


### Subnet UUID List Connected to Internet Gateway

Enter the subnet UUID connected to the VPC network connected to the internet gateway. If multiple subnets are queried, connect them with a colon (`:`). For more detailed information about the subnet list query API, see [View subnet list](/Network/VPC/en/public-api-gov/#_6).


### VPC Network UUID

Enter the UUID of the internal VPC network to connect to the node. For more detailed information about the network list query API, see [View network list](/Network/VPC/en/public-api-gov/#_2).

### VPC Subnet UUID

Enter the subnet UUID connected to the internal VPC network to connect to the node. For more detailed information about the subnet list query API, see [View subnet list](/Network/VPC/en/public-api-gov/#_6).

### Availability Zone UUID

Enter the UUID of the availability zone in which to create the node. For more detailed information about the availability zone list query API, see [View availability zones](/Compute/Instance/en/public-api-gov/#_9).

### Key Pair Name

Enter the key pair to use for node access. For more detailed information about the key pair list query API, see [View key pair list](/Compute/Instance/en/public-api-gov/#_13).

### Base Image UUID

Enter the base image UUID to use for node creation. To filter only the base images used for NKS node creation, enter the value `nhncloud_allow_nks_cpu_flavor=true&visibility=public` in the query string parameter when making the API call. For more detailed information about the base image list query API, see [Image list query](/Compute/Image/en/public-api-gov/#_2).

### Block Storage Type

Enter the UUID of the block storage to use for the node. For more detailed information about the block storage type list query API, see [View volume type list](/Storage/Block%20Storage/en/public-api-gov/#_2).

### Instance Type UUID

Enter the instance type UUID of the node to be created. For more detailed information about the instance type list query API, see [View instance type list](/Compute/Instance/en/public-api-gov/#_2).

## Cluster

### View Cluster List

View the cluster list.

```
GET /v1/clusters
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |

#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| clusters | Body | Array | List of cluster information objects |
| clusters.uuid | Body | UUID | Cluster UUID |
| clusters.name | Body | String | Cluster name |
| clusters.flavor_id | Body | UUID | Instance type UUID of the default worker node |
| clusters.keypair | Body | UUID | Keypair name applied to the default worker node group |
| clusters.node_count | Body | Integer | Total number of worker nodes |
| clusters.stack_id | Body | UUID | Heat stack UUID connected to the control plane |
| clusters.status | Body | String | Cluster task status |
| clusters.status_reason | Body | String | Cluster task status reason (may be null) |
| clusters.health_status | Body | String | Validity of the cluster's k8s API and k8s node status information. <br>* `FRESH`: k8s API and k8s node status information are valid <br>* `STALE`: k8s API and node status information have not been updated for a period of time, reducing information validity <br>* `ROTTEN`: k8s API and node status information have not been updated for a long time, making the information unreliable |
| clusters.health_status_reason | Body | Object | Object containing detailed information about the cluster's k8s API and k8s node status by worker node group |
| clusters.health_status_reason.timestamp | Body | String | Cluster k8s API and k8s node status information update time (UTC) |
| clusters.health_status_reason.cluster.api_status | Body | String | k8s API status information statistics. <br>* `NORMAL`: k8s API status is normal <br>* `STALED_DATA`: k8s API status information has not been updated for a period of time, reducing information validity <br>* `ROTTEN_DATA`: k8s API status information has not been updated for a long time, making the information unreliable <br>* `K8S_API_NOT_WORKING`: k8s API status is abnormal |
| clusters.health_status_reason.api | Body | String | k8s API status information. <br>* `OK`: k8s API status is normal <br>* `NOT_OK`: k8s API status is abnormal |
| clusters.health_status_reason.cluster.node_status | Body | String | Statistics of k8s node status information for all worker node groups. <br>* `NORMAL`: All k8s nodes are in Ready state <br>* `STALED_DATA`: k8s node status information has not been updated for a period of time, reducing information validity <br>* `ROTTEN_DATA`: k8s API status information has not been updated for a long time, making the information unreliable <br>* `NOT_READY_NODE_EXIST`: Not Ready k8s nodes exist in the cluster <br>* `ALL_NODES_NOT_READY`: All k8s nodes in the cluster are in Not Ready state |
| clusters.health_status_reason.nodegroup.node_status.{WORKER_NODEGROUP_NAME} | Body | String | Statistics of k8s node status information for a specific worker node group. <br>* `NORMAL`: All k8s nodes in the worker node group are in Ready state <br>* `STALED_DATA`: k8s node status information has not been updated for a period of time, reducing information validity <br>* `ROTTEN_DATA`: k8s API status information has not been updated for a long time, making the information unreliable <br>* `NOT_READY_NODE_EXIST`: Not Ready k8s nodes exist in the worker node group <br>* `ALL_NODES_NOT_READY`: All k8s nodes in the worker node group are in Not Ready state |
| clusters.health_status_reason.nodegroup-stats.{WORKER_NODEGROUP_NAME} | Body | String | k8s node status information for a specific worker node group. {Number of Ready nodes}:{Number of Not Ready nodes} |
| clusters.labels | Body | Object | Cluster labels |
| clusters.labels.kube_tag | Body | String | Control plane Kubernetes version |
| clusters.labels.availability_zone | Body | String | Default worker node group applied: Availability zone |
| clusters.labels.node_image | Body | UUID | Default worker node group applied: Base image UUID |
| clusters.labels.external_network_id | Body | String | VPC network UUID connected to the Internet Gateway |
| clusters.labels.external_subnet_id_list | Body | String | List of subnet UUIDs connected to the Internet Gateway (colon-separated) |
| clusters.labels.cert_manager_api | Body | String | Whether CSR (Certificate Signing Request) feature is enabled. Must be set to "True" |
| clusters.labels.master_lb_floating_ip_enabled | Body | String | Whether to create a public domain address for the Kubernetes API endpoint ("True" / "False") |
| clusters.labels.strict_sg_rules | Body | String | Create only essential security rules in the worker node security group ("True" / "False"), (available in clusters created after 2024.03.05) |
| clusters.labels.skm | Body | String | SKM symmetric key ID applied to etcd encryption. Outputs a JSON object in the format `{"key_id": "${SKM_KEY_ID}"}` as a string |
| clusters.labels.additional_network_id_list | Body | String | Default worker node group applied: List of VPC network UUIDs for additional networks (colon-separated) |
| clusters.labels.additional_subnet_id_list | Body | String | Default worker node group applied: List of VPC subnet UUIDs for additional networks (colon-separated) |
| clusters.labels.cni_driver | Body | String | Cluster CNI (available in clusters created after 2023.03.31) |
| clusters.labels.service_cluster_ip_range | Body | String | K8s service network, IP range assigned to ClusterIP when creating a service in the cluster (available in clusters created after 2023.05.30) |
| clusters.labels.pods_network_cidr | Body | String | Cluster pod network (available in clusters created after 2023.05.30) |
| clusters.labels.pods_network_subnet | Body | String | Cluster pod subnet size (available in clusters created after 2023.05.30) |
| clusters.labels.term_of_validity | Body | String | Certificate validity period |
| clusters.labels.certificate_expiry | Body | String | Certificate expiration date |

<details><summary>Example</summary>
<p>

```json
{
    "clusters": [
        {
            "cluster_template_id": "b4503d97-6012-499d-a31a-5200f94a7890",
            "create_timeout": 60,
            "docker_volume_size": null,
            "flavor_id": "6ef27f21-c774-4c0e-84ff-7dd4a762571f",
            "health_status": "HEALTHY",
            "keypair": "testkeypair",
            "labels": {
                "availability_zone": "kr2-pub-b",
                "cert_manager_api": "True",
                "clusterautoscale": "nodegroupfeature",
                "etcd_volume_size": "10",
                "external_network_id": "751b8227-7b45-440a-9349-dbf829d0aba5",
                "external_subnet_id_list": "59ddc195-76b1-431d-9693-f09880747dc6",
                "flavor_type": "core",
                "hypervisor_type": "qemu",
                "kube_tag": "v1.23.3",
                "login_username": "centos",
                "master_lb_floating_ip_enabled": "true",
                "strict_sg_rules": "True",
                "node_image": "f462a2a5-ba24-46d6-b7a1-9a9febcd3cfc",
                "os_arch": "amd64",
                "os_distro": "CentOS",
                "os_type": "linux",
                "os_version": "7.8",
                "project_domain": "NORMAL",
                "server_group_meta": "k8s_2b778d83-8b67-45b1-920e-b0c5ad5c2f30_561c3f55-a23f-4e1a-b2fa-a5459b2c0575",
                "service_cluster_ip_range": "10.254.0.0/16",
                "pods_network_cidr" : "10.100.0.0/16",
                "pods_network_subnet" : "24"
            },
            "links": [
                {
                    "href": "https://kr1-api-kubernetes-infrastructure.gov-nhncloudservice.com/v1/clusters/f0af4484-0a16-433a-a15c-295d9ba6537d",
                    "rel": "self"
                },
                {
                    "href": "https://kr1-api-kubernetes-infrastructure.gov-nhncloudservice.com/clusters/f0af4484-0a16-433a-a15c-295d9ba6537d",
                    "rel": "bookmark"
                }
            ],
            "name": "k8s-test",
            "node_count": 1,
            "stack_id": "7f497472-9729-4b89-9124-1c097335b856",
            "status": "CREATE_COMPLETE",
            "uuid": "2b778d83-8b67-45b1-920e-b0c5ad5c2f30"
        }
    ]
}
```

</p>
</details>

### Query Cluster

Query individual cluster information.

```
GET /v1/clusters/{CLUSTER_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_ID_OR_NAME| URL | UUID or String | O | Cluster UUID or cluster name |

#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| uuid | Body | UUID | Cluster UUID |
| name | Body | String | Cluster name |
| flavor_id | Body | UUID | Instance type UUID of the default worker node |
| keypair | Body | UUID | Keypair name applied to the default worker node group |
| node_count | Body | Integer | Total number of worker nodes |
| stack_id | Body | UUID | Heat stack UUID associated with the control plane |
| status | Body | String | Cluster task status |
| status_reason | Body | String | Cluster task status reason (can be null) |
| health_status | Body | String | Validity of the cluster's k8s API and k8s node status information. <br> "FRESH": k8s API and k8s node status information is valid <br> "STALE": k8s API and node status information updates have not been made for a certain period of time, reducing information validity <br> "ROTTEN": k8s API and node status information updates have not been made for a long period of time, making information unreliable |
| health_status_reason | Body | Object | Object containing detailed information about the cluster's k8s API and k8s node status for each worker node group |
| health_status_reason.timestamp | Body | String | Update time (UTC) of cluster k8s API and k8s node status information |
| health_status_reason.cluster.api_status | Body | String | Statistics of k8s API status information. <br> "NORMAL": k8s API status is normal <br> "STALED_DATA": k8s API status information updates have not been made for a certain period of time, reducing information validity <br> "ROTTEN_DATA": k8s API status information updates have not been made for a long period of time, making information unreliable <br> "K8S_API_NOT_WORKING": k8s API status is abnormal |
| health_status_reason.api | Body | String | k8s API status information. <br> "OK": k8s API status is normal <br> "NOT_OK": k8s API status is abnormal |
| health_status_reason.cluster.node_status | Body | String | Statistics of k8s node status information for all worker node groups. <br> "NORMAL": All k8s nodes are in Ready state <br> "STALED_DATA": k8s node status information updates have not been made for a certain period of time, reducing information validity <br> "ROTTEN_DATA": k8s API status information updates have not been made for a long period of time, making information unreliable <br> "NOT_READY_NODE_EXIST": k8s nodes in Not Ready state exist in the cluster <br> "ALL_NODES_NOT_READY": All k8s nodes in the cluster are in Not Ready state |
| health_status_reason.nodegroup.node_status.{WORKER_NODEGROUP_NAME} | Body | String | Statistics of k8s node status information for a specific worker node group. <br> "NORMAL": All k8s nodes in the corresponding worker node group are in Ready state <br> "STALED_DATA": k8s node status information updates have not been made for a certain period of time, reducing information validity <br> "ROTTEN_DATA": k8s API status information updates have not been made for a long period of time, making information unreliable <br> "NOT_READY_NODE_EXIST": k8s nodes in Not Ready state exist in the corresponding worker node group <br> "ALL_NODES_NOT_READY": All k8s nodes in the corresponding worker node group are in Not Ready state |
| health_status_reason.nodegroup-stats.{WORKER_NODEGROUP_NAME} | Body | String | k8s node status information for a specific worker node group. Represents {number of ready nodes}:{number of not ready nodes} |
| api_address | Body | String | Kubernetes API endpoint |
| project_id | Body | String | Project (tenant) ID |
| fixed_network | Body | UUID | VPC UUID |
| fixed_subnet | Body | UUID | VPC subnet UUID |
| node_addresses | Body | String List | Worker node IP address list |
| created_at | Body | String | Creation time (UTC) |
| updated_at | Body | String | Recent update time (UTC) |
| labels | Body | Object | Cluster labels |
| labels.kube_tag | Body | String | Control plane Kubernetes version |
| labels.availability_zone | Body | String | Applied to default worker node group: Availability zone |
| labels.node_image | Body | UUID | Applied to default worker node group: Base image UUID |
| labels.external_network_id | Body | String | VPC network UUID connected to the Internet gateway |
| labels.external_subnet_id_list | Body | String | List of subnet UUIDs connected to the Internet gateway (separated by colons) |
| labels.cert_manager_api | Body | String | Whether CSR (Certificate Signing Request) functionality is enabled. Must be set to "True" |
| labels.master_lb_floating_ip_enabled | Body | String | Whether to create a public domain address for the Kubernetes API endpoint ("True" / "False") |
| labels.strict_sg_rules | Body | String | Create only essential security rules in worker node security groups ("True" / "False") (can be verified in clusters created after 2024.03.05) |
| labels.skm | Body | String | SKM symmetric key ID applied to etcd encryption. Output as a string of a JSON object in the format `{"key_id": "${SKM_KEY_ID}"}` |
| labels.additional_network_id_list | Body | String | Applied to default worker node group: List of VPC network UUIDs for additional networks (separated by colons) |
| labels.additional_subnet_id_list | Body | String | Applied to default worker node group: List of VPC subnet UUIDs for additional networks (separated by colons) |
| labels.cni_driver | Body | String | Cluster CNI (can be verified in clusters created after 2023.03.31) |
| labels.service_cluster_ip_range | Body | String | K8s service network, IP range assigned to ClusterIP when creating a service in the cluster (can be verified in clusters created after 2023.05.30) |
| labels.pods_network_cidr | Body | String | Cluster pod network (can be verified in clusters created after 2023.05.30) |
| labels.pods_network_subnet | Body | String | Cluster pod subnet size (can be verified in clusters created after 2023.05.30) |
| labels.term_of_validity | Body | String | Term of validity of the certificate |
| labels.certificate_expiry | Body | String | Certificate expiration date |
| labels.platform_version | Body | String | Platform version |

<details><summary>Example</summary>
<p>

```json
{
    "api_address": "https://2b778d83-kr2-k8s.container.cloud.toast.com:6443",
    "cluster_template_id": "b4503d97-6012-499d-a31a-5200f94a7890",
    "coe_version": "v1.23.3",
    "container_version": "1.12.6",
    "create_timeout": 60,
    "created_at": "2021-08-05T01:48:39+00:00",
    "docker_volume_size": null,
    "fixed_network": "eb212079-b6ec-430c-ba57-14280a457bcb",
    "fixed_subnet": "4fdf5b80-3d35-43f5-a5c1-010a3b6c8e90",
    "flavor_id": "6ef27f21-c774-4c0e-84ff-7dd4a762571f",
    "floating_ip_enabled": false,
    "health_status": "HEALTHY",
    "health_status_reason": {
        {"test-k8s-default-w-bnga636xulqk-node-0.Ready": "True", "api": "ok"}
    },
    "keypair": "test-keypair",
    "labels": {
        "availability_zone": "kr2-pub-b",
        "cert_manager_api": "True",
        "clusterautoscale": "nodegroupfeature",
        "etcd_volume_size": "10",
        "external_network_id": "751b8227-7b45-440a-9349-dbf829d0aba5",
        "external_subnet_id_list": "59ddc195-76b1-431d-9693-f09880747dc6",
        "flavor_type": "core",
        "hypervisor_type": "qemu",
        "kube_tag": "v1.23.3",
        "kube_version_status": "NEED_UPGRADE",
        "platform_version": "1.202511.0",
        "login_username": "centos",
        "master_lb_floating_ip_enabled": "true",
        "strict_sg_rules": "True",
        "node_image": "f462a2a5-ba24-46d6-b7a1-9a9febcd3cfc",
        "os_arch": "amd64",
        "os_distro": "CentOS",
        "os_type": "linux",
        "os_version": "7.8",
        "project_domain": "NORMAL",
        "server_group_meta": "k8s_2b778d83-8b67-45b1-920e-b0c5ad5c2f30_561c3f55-a23f-4e1a-b2fa-a5459b2c0575",
        "service_cluster_ip_range": "10.254.0.0/16",
        "pods_network_cidr" : "10.100.0.0/16",
        "pods_network_subnet" : "24"
    },
    "links": [
        {
            "href": "https://kr1-api-kubernetes-infrastructure.gov-nhncloudservice.com/v1/clusters/2b778d83-8b67-45b1-920e-b0c5ad5c2f30",
            "rel": "self"
        },
        {
            "href": "https://kr1-api-kubernetes-infrastructure.gov-nhncloudservice.com/clusters/2b778d83-8b67-45b1-920e-b0c5ad5c2f30",
            "rel": "bookmark"
        }
    ],
    "name": "test-k8s",
    "node_addresses": [
        "192.168.0.5"
    ],
    "node_count": 1,
    "project_id": "1ffeaca9bbf94ab1aa9cffdec29a258a",
    "stack_id": "7f497472-9729-4b89-9124-1c097335b856",
    "status": "CREATE_COMPLETE",
    "status_reason": null,
    "updated_at": "2021-08-05T04:39:49+00:00",
    "user_id": "12ba32bebc414c4992a2c9be3952a64c",
    "uuid": "2b778d83-8b67-45b1-920e-b0c5ad5c2f30"
}
```

</p>
</details>

### View Task History List

View the task history list for the cluster.

```
GET /v1/clusters/{CLUSTER_UUID}/events
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_UUID | URL | UUID | O | Cluster UUID |


#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| events | Body | Array | List of event objects |
| events.id | Body | Integer | Task ID |
| events.uuid | Body | UUID | Task UUID |
| events.project_id | Body | String | Project (tenant) ID |
| events.cluster_uuid | Body | UUID | Cluster UUID |
| events.cluster_name | Body | String | Cluster name |
| events.resource_uuid | Body | UUID | Resource UUID |
| events.resource_name | Body | String | Resource name |
| events.resource_type | Body | String | Resource type ("cluster" / "nodegroup") |
| events.type | Body | String | Task type |
| events.state | Body | String | Task status ("SUCCESS" / "FAIL" / "IN_PROGRESS") |
| events.contents | Body | String | Task execution details (null on success) |
| events.details | Body | String | Task request information | 
| events.created_at | Body | String | Task start time (UTC) |
| events.updated_at | Body | String | Task completion time (UTC) |


<details><summary>Example</summary>
<p>

```json
{
    "events": [
        {
            "id": 3683,
            "uuid": "868f218a-9446-4500-b6b3-8c1a95e3d7c3",
            "project_id": "5d8cc67593754d5581f7e8986badf358",
            "cluster_uuid": "388794e6-14dc-48ee-9a4f-9c40df5d97ec",
            "cluster_name": "nks-cluster",
            "resource_uuid": "388794e6-14dc-48ee-9a4f-9c40df5d97ec",
            "resource_name": "nks-cluster",
            "resource_type": "cluster",
            "type": "CLUSTER_CREATE",
            "state": "SUCCESS",
            "contents": null,
            "created_at": "2024-01-30T01:31:06+00:00",
            "updated_at": "2024-01-30T01:42:39+00:00"
        }
    ]
}
```

</p>
</details>

### View Task History

Retrieves the task history of a cluster.

```
GET /v1/clusters/{CLUSTER_UUID}/events/{EVENT_UUID}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_UUID | URL | UUID | O | Cluster UUID |
| EVENT_UUID | URL | UUID | O | Task UUID |


#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| id | Body | Integer | Task ID |
| uuid | Body | UUID | Task UUID |
| project_id | Body | String | Project (tenant) ID |
| cluster_uuid | Body | UUID | Cluster UUID |
| cluster_name | Body | String | Cluster name |
| resource_uuid | Body | UUID | Target resource UUID |
| resource_name | Body | String | Target resource name |
| resource_type | Body | String | Target resource type ("cluster" / "nodegroup") |
| type | Body | String | Task type |
| state | Body | String | Task state ("SUCCESS" / "FAIL" / "IN_PROGRESS") |
| contents | Body | String | Task progress details (null on success) |
| events.details | Body | String | Task request information | 
| created_at | Body | String | Task start time (UTC) |
| updated_at | Body | String | Task completion time (UTC) |


<details><summary>Example</summary>
<p>

```json
{
    "id": 3683,
    "uuid": "868f218a-9446-4500-b6b3-8c1a95e3d7c3",
    "project_id": "5d8cc67593754d5581f7e8986badf358",
    "cluster_uuid": "388794e6-14dc-48ee-9a4f-9c40df5d97ec",
    "cluster_name": "nks-cluster",
    "resource_uuid": "388794e6-14dc-48ee-9a4f-9c40df5d97ec",
    "resource_name": "nks-cluster",
    "resource_type": "cluster",
    "type": "CLUSTER_CREATE",
    "state": "SUCCESS",
    "contents": null,
    "created_at": "2024-01-30T01:31:06+00:00",
    "updated_at": "2024-01-30T01:42:39+00:00"
}
```

</p>
</details>

### Create cluster

Create a cluster.

```
POST /v1/clusters
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| keypair | Body | String | O | Keypair name to be applied to the default worker node group |
| name | Body | String | O | Cluster name |
| cluster_template_id | Body | String | O | Cluster template ID. Must be set to "iaas_console" |
| node_count | Body | String | O | Number of nodes to be applied to the default worker node group |
| labels | Body | Object | O | Cluster creation information object |
| labels.availability_zone | Body | String | O | Default worker node group application: Availability Zone |
| labels.node_image | Body | UUID | O | Default worker node group application: Base image UUID |
| labels.boot_volume_type | Body | String | O | Default worker node group application: Block storage type |
| labels.boot_volume_size | Body | String | O | Default worker node group application: Block storage size (GB) |
| labels.external_network_id | Body | String | X | VPC network UUID connected to the internet gateway<br>Must be set if the router connected with VPC subnet is connected to the internet gateway |
| labels.external_subnet_id_list | Body | String | X | List of subnet UUIDs connected to the internet gateway (separated by colons)<br>Must be set if the router connected with VPC subnet is connected to the internet gateway |
| labels.cert_manager_api | Body | String | O | CSR (Certificate Signing Request) feature activation. Must be set to "True" |
| labels.ca_enable | Body | String | O | Default worker node group application: Cluster autoscaler: Feature activation ("True" / "False") |
| labels.ca_pod_replicas | Body | String | X | Default worker node group application: Cluster autoscaler: Number of pods |
| labels.ca_max_node_count | Body | String | X | Default worker node group application: Cluster autoscaler: Maximum number of nodes |
| labels.ca_min_node_count | Body | String | X | Default worker node group application: Cluster autoscaler: Minimum number of nodes |
| labels.ca_scale_down_enable | Body | String | X | Default worker node group application: Cluster autoscaler: Scale-down activation ("True" / "False") |
| labels.ca_scale_down_unneeded_time | Body | String | X | Default worker node group application: Cluster autoscaler: Threshold area maintenance time |
| labels.ca_scale_down_util_thresh | Body | String | X | Default worker node group application: Cluster autoscaler: Resource usage threshold |
| labels.ca_scale_down_delay_after_add | Body | String | X | Default worker node group application: Cluster autoscaler: Scale-down delay after expansion |
| labels.mba_scale_out | Body | String | X | Default node group application: Metric-based autoscaler scale-out policy configuration |
| labels.mba_scale_in | Body | String | X | Default node group application: Metric-based autoscaler scale-in policy configuration |
| labels.kube_tag | Body | String | O | Kubernetes version |
| labels.user_script | Body | String | X | User script (old) |
| labels.user_script_v2 | Body | String | X | User script |
| labels.master_lb_floating_ip_enabled | Body | String | O | Whether to create a public domain address for the Kubernetes API endpoint ("True" / "False")<br>Can be set to "True" only when labels.external_network_id and external_subnet_id_list are configured |
| labels.strict_sg_rules | Body | String | X | Create only essential security rules in worker node security groups ("True" / "False"), default: "False" |
| labels.additional_network_id_list | Body | String | X | Default worker node group application: List of VPC network UUIDs for additional networks (separated by colons) |
| labels.additional_subnet_id_list | Body | String | X | Default worker node group application: List of VPC subnet UUIDs for additional networks (separated by colons) |
| labels.service_cluster_ip_range | Body | String | X | K8s service network, IP range assigned to ClusterIP when creating a service in the cluster. See input rules for fixed_subnet, pods_network_cidr, service_cluster_ip_range |
| labels.pods_network_cidr | Body | String | X | Cluster pod network. See input rules for fixed_subnet, pods_network_cidr, service_cluster_ip_range |
| labels.pods_network_subnet | Body | Integer | X | Cluster pod subnet size. See input rules for pods_network_subnet |
| labels.extra_security_groups | Body | Array | X | Default worker node group application: List of additional security group objects |
| labels.extra_security_groups[].target_subnet | Body | String | X | Target subnet UUID for specifying additional security groups |
| labels.extra_security_groups[].security_group_ids | Body | String | X | List of additional security group UUIDs (separated by commas) |
| labels.extra_volumes | Body | Array | X | Default worker node group application: List of additional block storage objects |
| labels.extra_volumes[].volume_type | Body | String | X | Additional block storage type |
| labels.extra_volumes[].volume_size | Body | Integer | X | Additional block storage size (GB) |
| labels.extra_volumes[].volume_key_id | Body | String | X | (When using encrypted block storage) Symmetric key ID to be applied to encrypted block storage |
| labels.extra_volumes[].volume_appkey | Body | String | X | (When using encrypted block storage) App key of the symmetric key to be applied to encrypted block storage |
| labels.extra_volumes[].volume_mount_path | Body | String | X | Path where additional block storage will be mounted |
| labels.control_plane_log | Body | String | X | K8S Control plane log storage activation |
| labels.skm | Body | String | X | Apply etcd encryption by integrating with SKM. Use a value that is a JSON object in the format `{"key_id": "${SKM_KEY_ID}"}` output as a string |
| labels.fip_auto_bind_enable | Body | String | X | Floating IP auto-assignment: Feature activation ("True" / "False") |
| labels.fip_bind_subnet | Body | String | X | Floating IP auto-assignment: Subnet of the network interface to which the floating IP is connected |
| labels.fip_selector | Body | String | X | Floating IP auto-assignment: Identifier for selecting floating IPs to assign to nodes |
| labels.fip_auto_bind_enable | Body | String | X | Default worker node group application: Floating IP auto-assignment: Feature activation ("True" / "False") |
| labels.fip_bind_subnet | Body | String | X | Default worker node group application: Floating IP auto-assignment: Subnet of the network interface to which the floating IP is connected |
| labels.fip_selector | Body | String | X | Default worker node group application: Floating IP auto-assignment: Identifier for selecting floating IPs to assign to nodes |
| labels.k8s_node_labels | Body | String | X | Default worker node group application: Kubernetes labels configuration |
| flavor_id | Body | UUID | O | Default worker node group application: Node instance type UUID |
| fixed_network | Body | UUID | O | VPC network UUID |
| fixed_subnet | Body | UUID | O | VPC subnet UUID. See input rules for fixed_subnet, pods_network_cidr, service_cluster_ip_range |
| addons | Body | List of Object | X | List of addon information to install |
| addons.name | Body | String | O | Addon name |
| addons.version | Body | String | O | Addon version |
| addons.options | Body | Object | X | Options per addon |

> [Note]
> The CIDR for fixed_subnet, pods_network_cidr, and service_cluster_ip_range must be entered according to the following rules:
> - Cannot overlap with the link-local address range (169.254.0.0/16).
> - The fixed_subnet, pods_network_cidr, and service_cluster_ip_range ranges cannot overlap.
> - Cannot overlap with the IP range used internally in NKS (198.18.0.0/19).
> - CIDR blocks larger than /24 cannot be entered (the following CIDR blocks cannot be used: /26, /30).
> - For clusters of v1.23.3 or earlier, cannot overlap with docker BIP (bridged IP range) (172.17.0.0/16).
> pods_network_subnet must be entered according to the following rules:
> - Only values in the range of 20-28 (inclusive) can be entered.
> - The pods_network_subnet value must be at least 2 greater than the pods_network_cidr prefix value. Normal example (pod subnet size: 24, pod network: 10.100.0.0/22)


<details><summary>Example</summary>
<p>

```json
{
    "cluster_template_id": "iaas_console",
    "create_timeout": 60,
    "fixed_network": "eb212079-b6ec-430c-ba57-14280a457bcb",
    "fixed_subnet": "4fdf5b80-3d35-43f5-a5c1-010a3b6c8e90",
    "flavor_id": "6ef27f21-c774-4c0e-84ff-7dd4a762571f",
    "keypair": "test-keypair",
    "labels": {
        "availability_zone": "kr2-pub-b",
        "boot_volume_size": "20",
        "boot_volume_type": "General HDD",
        "ca_enable": "false",
        "ca_pod_replicas": "1",
        "ca_max_node_count": "10",
        "ca_min_node_count": "1",
        "ca_scale_down_delay_after_add": "3",
        "ca_scale_down_enable": "true",
        "ca_scale_down_unneeded_time": "3",
        "ca_scale_down_util_thresh": "50",
        "cert_manager_api": "True",
        "clusterautoscale": "nodegroupfeature",
        "external_network_id": "751b8227-7b45-440a-9349-dbf829d0aba5",
        "external_subnet_id_list": "59ddc195-76b1-431d-9693-f09880747dc6",
        "kube_tag": "v1.23.3",
        "master_lb_floating_ip_enabled": "true",
        "strict_sg_rules": "True",
        "node_image": "f462a2a5-ba24-46d6-b7a1-9a9febcd3cfc",
        "user_script_v2": "",
        "extra_security_groups": [
            {
                "target_subnet": "4fdf5b80-3d35-43f5-a5c1-010a3b6c8e90",
                "security_group_ids": "8669cca4-7904-4dc6-b1be-db49661cedb6,fa69d78d-bd04-4ab0-9ce6-c92a84b899c2"
            }
        ],
        "extra_volumes": [
            {
                "volume_type": "General HDD",
                "volume_size": 100
            }
        ]
    },
    "name": "test-k8s",
    "node_count": 1,
    "addons": [
        {"name": "calico", "version": "v3.28.2-nks1", "options": {"mode": "vxlan"}},
        {"name": "coredns", "version": "1.8.4-nks1"}
    ]
}
```

</p>
</details>

#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| uuid | Body | UUID | Cluster UUID |

<details><summary>Example</summary>
<p>

```json
{
    "uuid": "5801ef8a-3760-4858-b467-fc4c1201241d"
}
```

</p>
</details>

---

### Delete cluster

Delete a cluster.

```
DELETE /v1/clusters/{CLUSTER_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | Cluster UUID or cluster name |


#### Response

This API does not return a response body.

### Resize

Adjusts the number of nodes in a cluster.

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/actions/resize
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | Cluster UUID or cluster name | 
| nodegroup | Body | UUID | O | Target worker node group name / UUID |
| node_count | Body | Integer | O | Number of worker nodes to change |
| nodes_to_remove | Body | String List | X | Node UUIDs to delete |

* Notes
    * When reducing nodes (i.e., deleting some nodes), you must set **nodes_to_remove** to specify which nodes to delete. If you do not specify which nodes to delete, the nodes to be deleted are randomly selected.
    * node_count: minimum 1, maximum 10 (the maximum can be adjusted by quota)

<details><summary>Scale-up example</summary>
<p>

```json
{
    "node_count": 3,
    "nodegroup": "default-worker"
}
```

</p>
</details>

<details><summary>Scale-down example</summary>
<p>

```json
{
    "node_count": 1,
    "nodegroup": "default-worker",
    "nodes_to_remove": [
        "593e4aee-697f-4808-aa5b-d3c8703795ff",
        "ce2e2d2a-ddf5-41da-a338-72e7f5237088"
    ]
}
```

</p>
</details>

#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| uuid | Body | String | Target cluster UUID|

<details><summary>Example</summary>
<p>

```json
{
    "uuid": "5bac7acd-58b7-4cf5-95f5-a25d67da13a2"
}

```

</p>
</details>

### Retrieve cluster kubeconfig

Retrieves the cluster configuration file (kubeconfig).

```
GET /v1/clusters/{CLUSTER_ID_OR_NAME}/config
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | Cluster UUID or cluster name | 


#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| config | Body | String | kubeconfig file body |


<details><summary>Example</summary>
<p>

```json
{
    "config": "apiVersion: v1\nclusters:\n- cluster:\n    certificate-authority-data: LS0tLS1CRU... \n    server: https://96742ac4-kr2-k8s.container.cloud.toast.com:6443\n  name: \"toast-robot-e2e-1-18\"\ncontexts:\n- context:\n    cluster: \"toast-robot-e2e-1-18\"\n    user: admin\n  name: default\ncurrent-context: default\nkind: Config\npreferences: {}\nusers:\n- name: admin\n  user:\n    client-certificate-data: LS0tLS1CRU...\n    client-key-data: LS0tLS1CRU...\n"
}
```

</p>
</details>

### Apply IP Access Control to Cluster API Endpoint
You can apply or remove IP access control to the cluster API endpoint.
For more information about IP access control features, see the [IP Access Control](/Network/Load%20Balancer/en/overview-gov/#ip) document.
For more information about IP access control rules for the cluster API endpoint, see the [User Guide](/Container/NKS/en/user-guide-gov/#api_endpoint_ipacl).

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/api_ep_ipacl
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | Cluster UUID or cluster name |
| enable | Body | String | O | Can be set to 'true' or 'false'. Default: 'false'<br>true: Apply IP access control to the cluster API endpoint<br>false: Remove IP access control from the cluster API endpoint. When set to false, all subordinate settings are ignored |
| action | Body | String | O(when enable is set to true) | IP access control type, can be set to ALLOW or DENY |
| ipacl_targets | Body | List of Object | O(when enable is set to true) | IP access control target object |
| ipacl_targets.cidr_address | Body | String | O(when enable is set to true) | IP access control target. You can enter an IP address or CIDR format IP address range |
| ipacl_targets.descripion | Body | String | X | IP access control target description |


<details><summary>Example</summary>
<p>

```json
{
    "enable": "True",
    "action": "ALLOW",
    "ipacl_targets": [
        {
            "cidr_address" : "192.168.0.5"
        },
        {
            "cidr_address" : "10.10.22.3/24",
            "description": "Your Friends"
        }
    ]   
}
```

</p>
</details>


#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| uuid | Body | UUID | Cluster UUID |

<details><summary>Example</summary>
<p>

```json
{
    "uuid": "0641db9f-5e71-4df9-9571-089c7964d82e"
}
```

</p>
</details>

### Query Cluster API Endpoint IP Access Control
You can retrieve the IP access control information applied to the cluster API endpoint.

```
GET /v1/clusters/{CLUSTER_ID_OR_NAME}/api_ep_ipacl
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request
This API does not require a request body.

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | Cluster UUID or cluster name | 


#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| cluster_uuid | Body | UUID | Cluster UUID |
| enable | Body | String | true: IP access control is enabled for the cluster API endpoint, false: IP access control is disabled for the cluster API endpoint | 
| action | Body | String | IP access control type: ALLOW, DENY |
| ipacl_targets | Body | List of Object | IP access control target objects |
| ipacl_targets.cidr_address | Body | String | IP access control target. IP address or CIDR format IP address range |
| ipacl_targets.descripion | Body | String | IP access control target description |

<details><summary>Example when enable: true</summary>
<p>

```json
{
    "cluster_uuid" : "8be87215-9db7-45ed-a03c-38e6db939915",
    "enable": "true",
    "action": "ALLOW",
    "ipacl_targets": [
        {
            "cidr_address" : "192.168.0.5",
            "description": "My Friend"
        },
        {
            "cidr_address" : "10.10.22.3/24"
        }
    ]   
}
```

</p>
</details>

<details><summary>Example when enable: false</summary>
<p>

```json
{
    "cluster_uuid" : "8be87215-9db7-45ed-a03c-38e6db939915",
    "enable": "false"
}
```

</p>
</details>

### Cluster Certificate Renewal

Renews cluster certificates.
```
PATCH /v1/certificates/{CLUSTER_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request
| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | Cluster UUID or cluster name | 
| term_of_validity | Body | Integer | O | Certificate validity period. Can be specified in years. Minimum: 1, Maximum: 5 |

<details><summary>Example</summary>
<p>

```json
{
    "term_of_validity": 5
}
```

</p>
</details>

#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| uuid | Body | String | Target cluster UUID|

<details><summary>Example</summary>
<p>

```json
{
    "uuid": "5f6af7da-df9b-4edd-8284-02317b11e061"
}

```

</p>
</details>

## Control Plane Kubernetes Component Log Storage

Stores logs from major Kubernetes components running on the control plane of NHN Kubernetes Service (NKS) in Log & Crash Search or Object Storage.

```
PATCH /v1/clusters/{CLUSTER_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

### Request

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | Cluster UUID or cluster name | 
| type | Body | String | O | Set to `control_plane_log` |
| control_plane_log | Body | Object | O | control_plane_log object |
| control_plane_log.enable | Body | bool | O | Enable K8S control plane log storage |
| control_plane_log.type | Body | String | Required when enable: true | lncs: Send control plane logs to Log & Crash Search obs: Send control plane logs to Object Storage |
| control_plane_log.sgw | Body | UUID | Required when enable: true | Depends on control_plane_log.type<br>lncs: Log & Crash Search Service Gateway UUID<br>obs: Object Storage Service Gateway UUID |
| control_plane_log.upload_interval | Body | Integer | X | Set OBS log transmission interval (minutes)<br>min: 1<br>max: 60<br>default: 10 |
| control_plane_log.lncs_appkey | Body | String | Required when enable: true<br>and control_plane_log.type = lncs | Log & Crash Search Appkey information for the same project (tenant) as NKS |
| control_plane_log.obs_api_url | Body | String | Required when enable: true<br>and control_plane_log.type = obs | Full path of your OBS container<br>(OBS storage address + OBS container name + desired storage path) |
| control_plane_log.obs_store_as | Body | String | X | How OBS log files are provided (gzip, text) |

<details><summary>Enable Log & Crash Search log transmission</summary>
<p>

```json
{
    "type": "control_plane_log",
    "control_plane_log" : {
        "enable": true,
        "type": "lncs",
        "sgw": "b6f68830-e688-4d89-ac0a-2f1a5594177a",
        "upload_interval" : 10,
        "lncs_appkey" : "3e4jP4LlMGXitafx",
    }
}
```

</p>
</details>

<details><summary>Enable Object Storage log transmission</summary>
<p>

```json
{
    "type": "control_plane_log",
    "control_plane_log" : {
        "enable": true,
        "type": "obs",
        "sgw": "b6f68830-e688-4d89-ac03-2f1155a4177a",
        "upload_interval" : 60,
        "obs_api_url" :"https://kr1-api-object-storage.gov-nhncloudservice.com/v1/AUTH_d5b58ab0bb9340909bd7ff5a24f44313/iksoon-obs-container/testpath",
        "obs_store_as" : "gzip"
    }
}
```

</p>
</details>

<details><summary>Disable log transmission</summary>
<p>

```json
{
    "type": "control_plane_log",
    "control_plane_log" : {
        "enable": false,
    }
}
```

</p>
</details>

### Response

| Name | Type | Format | Description |
|---|---|---|---|
| uuid | Body | UUID | Cluster UUID |

<details><summary>Example</summary>
<p>

```json
{
    "uuid": "018b06c5-1293-4081-8242-167a1cb9f262"
}
```

</p>
</details>

## Node Group

### View Node Group List

Queries the node group list.

```
GET /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | Cluster UUID or cluster name | 


#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| nodegroups | Body | Array | Node group information object list |
| nodegroups.uuid | Body | UUID | Node group UUID |
| nodegroups.flavor_id | Body | UUID | Node group instance type UUID |
| nodegroups.image_id | Body | UUID | Node group base image UUID |
| nodegroups.max_node_count | Body | Integer | Node group maximum node count |
| nodegroups.min_node_count | Body | Integer | Node group minimum node count |
| nodegroups.name | Body | String | Node group name |
| nodegroups.node_count | Body | Integer | Node group node count |
| nodegroups.role | Body | String | Node group role |
| nodegroups.stack_id | Body | UUID | Heat stack UUID associated with the node group |
| nodegroups.status | Body | String | Node group status |

<details><summary>Example</summary>
<p>

```json
{
    "nodegroups": [
        {
            "flavor_id": "069bdcff-e9b6-42c8-83ce-4c743ea30394",
            "image_id": "96aff4ab-d221-4688-8364-2fcf02d50547",
            "is_default": false,
            "max_node_count": 10,
            "min_node_count": 1,
            "name": "default-worker",
            "node_count": 2,
            "role": "worker",
            "stack_id": "f04c157a-78e3-4bfc-a83e-fbe7c01ab616",
            "status": "UPDATE_COMPLETE",
            "uuid": "018b06c5-1293-4081-8242-167a1cb9f262"
        }
    ]
}
```

</p>
</details>

### View Node Group

Retrieve individual node group information.

```
GET /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | Cluster UUID or Cluster name | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | Node group UUID or Node group name | 

#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| uuid | Body | UUID | Node group UUID |
| name | Body | String | Node group name |
| cluster_id | Body  | UUID | Cluster UUID to which the node group belongs |
| flavor_id | Body | UUID | Instance type UUID used by the node |
| image_id | Body | UUID | Base image UUID used by the node |
| labels | Body | Object | Node group creation information object |
| labels.availability_zone | Body | String | Applied to Worker node group: Availability zone |
| labels.node_image | Body | UUID | Applied to Worker node group: Base image UUID |
| labels.boot_volume_type | Body | String | Applied to Worker node group: Block storage type|
| labels.boot_volume_size | Body | String | Applied to Worker node group: Block storage size (GB) |
| labels.external_network_id | Body | String | VPC network UUID connected to the Internet gateway |
| labels.external_subnet_id_list | Body | String | Subnet UUID list connected to the Internet gateway (colon-separated) |
| labels.cert_manager_api | Body | String | Whether to enable CSR (Certificate Signing Request) feature. Must be set to "True" |
| labels.ca_enable | Body | String | Applied to Worker node group: Cluster Autoscaler: Whether to enable the feature ("True" / "False") |
| labels.ca_pod_replicas | Body | String | Applied to Worker node group: Cluster Autoscaler: Number of pods |
| labels.ca_max_node_count | Body | String | Applied to Worker node group: Cluster Autoscaler: Maximum number of nodes |
| labels.ca_min_node_count | Body | String | Applied to Worker node group: Cluster Autoscaler: Minimum number of nodes |
| labels.ca_scale_down_enable | Body | String | Applied to Worker node group: Cluster Autoscaler: Whether to enable scale-down ("True" / "False") |
| labels.ca_scale_down_unneeded_time | Body | String | Applied to Worker node group: Cluster Autoscaler: Time to maintain threshold zone |
| labels.ca_scale_down_util_thresh | Body | String | Applied to Worker node group: Cluster Autoscaler: Resource utilization threshold  |
| labels.ca_scale_down_delay_after_add | Body | String | Applied to Worker node group: Cluster Autoscaler: Scale-down delay time after scale-up |
| labels.mba_scale_out | Body | String | Applied to Worker node group: Metrics-based Autoscaler scale-out policy setting |
| labels.mba_scale_in | Body | String |  Applied to Worker node group: Metrics-based Autoscaler scale-in policy setting |
| labels.kube_tag | Body | String | Worker node group Kubernetes version |
| labels.user_script | Body | String | User script (old) |
| labels.user_script_v2 | Body | String | User script |
| labels.additional_network_id_list | Body | String | Applied to Worker node group: VPC network UUID list of additional networks (colon-separated) |
| labels.additional_subnet_id_list | Body | String | Applied to Worker node group: VPC subnet UUID list of additional networks (colon-separated) |
| labels.strict_sg_rules | Body | String | Create only required security rules in the worker node security group ("True" / "False"), (Available in clusters created after 2024.03.05.) |
| labels.platform_version | Body | String | Platform version |
| max_node_count | Body | Integer | Maximum number of nodes |
| min_node_count | Body | Integer | Minimum number of nodes |
| node_addresses | Body | String list | List of node IP addresses |
| node_count | Body | Integer | Number of nodes |
| project_id | Body | String | Project (tenant) ID |
| role | Body | String | Node group role |
| stack_id | Body | UUID | Heat stack UUID connected to the node group |
| status | Body | String | Node group status |
| status_reason | Body | String | Node group status reason (null possible) |
| created_at | Body | String | Creation time (UTC) |
| updated_at | Body | String | Last update time (UTC) |

<details><summary>Example</summary>
<p>

```json
{
    "cluster_id": "96742ac4-02e7-4b1d-a242-02876c0bd3f8",
    "created_at": "2021-10-23T10:06:19+00:00",
    "docker_volume_size": null,
    "flavor_id": "069bdcff-e9b6-42c8-83ce-4c743ea30394",
    "id": 2697,
    "image_id": "96aff4ab-d221-4688-8364-2fcf02d50547",
    "is_default": false,
    "labels": {
        "availability_zone": "",
        "boot_volume_size": "20",
        "boot_volume_type": "General HDD",
        "ca_enable": "True",
        "ca_pod_replicas": "1",
        "ca_max_node_count": "10",
        "ca_min_node_count": "2",
        "ca_scale_down_delay_after_add": "10",
        "ca_scale_down_enable": "True",
        "ca_scale_down_unneeded_time": "10",
        "ca_scale_down_util_thresh": "50",
        "cert_manager_api": "true",
        "clusterautoscale": "nodegroupfeature",
        "external_network_id": "751b8227-7b45-440a-9349-dbf829d0aba5",
        "external_subnet_id_list": "59ddc195-76b1-431d-9693-f09880747dc6",
        "flavor_type": "memory",
        "hypervisor_type": "qemu",
        "kube_tag": "v1.19.13",
        "kube_version_status": "LATEST",
        "platform_version": "1.202511.0",
        "login_username": "centos",
        "master_lb_floating_ip_enabled": "true",
        "strict_sg_rules": "True",
        "node_image": "96aff4ab-d221-4688-8364-2fcf02d50547",
        "os_arch": "amd64",
        "os_distro": "CentOS",
        "os_type": "linux",
        "os_version": "7.8",
        "project_domain": "NORMAL",
        "server_group_meta": "k8s_96742ac4-02e7-4b1d-a242-02876c0bd3f8_018b06c5-1293-4081-8242-167a1cb9f262"
    },
    "links": [
        {
            "href": "https://kr1-api-kubernetes-infrastructure.gov-nhncloudservice.com/v1/clusters/96742ac4-02e7-4b1d-a242-02876c0bd3f8/nodegroups/018b06c5-1293-4081-8242-167a1cb9f262",
            "rel": "self"
        },
        {
            "href": "https://kr1-api-kubernetes-infrastructure.gov-nhncloudservice.com/clusters/96742ac4-02e7-4b1d-a242-02876c0bd3f8/nodegroups/018b06c5-1293-4081-8242-167a1cb9f262",
            "rel": "bookmark"
        }
    ],
    "max_node_count": 10,
    "min_node_count": 2,
    "name": "default-worker",
    "node_addresses": [
        "192.168.0.40",
        "192.168.0.19"
    ],
    "node_count": 2,
    "project_id": "1ffeaca9bbf94ab1aa9cffdec29a258a",
    "role": "worker",
    "stack_id": "f04c157a-78e3-4bfc-a83e-fbe7c01ab616",
    "status": "UPDATE_COMPLETE",
    "status_reason": "Stack UPDATE completed successfully",
    "updated_at": "2021-10-28T02:13:15+00:00",
    "uuid": "018b06c5-1293-4081-8242-167a1cb9f262",
    "version": null
}
```

</p>
</details>

### Create node groups

Create a node group.

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | Cluster UUID or cluster name | 
| flavor_id | Body | UUID | O | Instance type UUID used by the node |
| image_id | Body | UUID | O | Base image UUID used by the node |
| labels | Body | Object | O | Node group creation information object |
| labels.availability_zone | Body | String | O | Default worker node group setting: Availability Zone |
| labels.boot_volume_type | Body | String | O | Default worker node group setting: Block storage type |
| labels.boot_volume_size | Body | String | O | Default worker node group setting: Block storage size (GB) |
| labels.ca_enable | Body | String | O | Default worker node group setting: Cluster autoscaler: Enable feature ("True" / "False") |
| labels.ca_pod_replicas | Body | String | X | Default worker node group setting: Cluster autoscaler: Pod replicas |
| labels.ca_max_node_count | Body | String | X | Default worker node group setting: Cluster autoscaler: Maximum node count |
| labels.ca_min_node_count | Body | String | X | Default worker node group setting: Cluster autoscaler: Minimum node count |
| labels.ca_scale_down_enable | Body | String | X | Default worker node group setting: Cluster autoscaler: Enable scale down ("True" / "False") |
| labels.ca_scale_down_unneeded_time | Body | String | X | Default worker node group setting: Cluster autoscaler: Threshold retention time |
| labels.ca_scale_down_util_thresh | Body | String | X | Default worker node group setting: Cluster autoscaler: Resource usage threshold |
| labels.ca_scale_down_delay_after_add | Body | String | X | Default worker node group setting: Cluster autoscaler: Scale down delay after scale up |
| labels.mba_scale_out | Body | String | X | Default node group setting: Metric-based autoscaler scale-out policy |
| labels.mba_scale_in | Body | String | X | Default node group setting: Metric-based autoscaler scale-in policy |
| labels.user_script | Body | String | X | User script (old) |
| labels.user_script_v2 | Body | String | X | User script |
| labels.additional_network_id_list | Body | String | X | Worker node group setting: VPC network UUID list for additional networks (colon-separated) |
| labels.additional_subnet_id_list | Body | String | X | Worker node group setting: VPC subnet UUID list for additional networks (colon-separated) |
| labels.extra_security_groups | Body | Array | X | Additional security group object list |
| labels.extra_security_groups[].target_subnet | Body | String | X | Target subnet UUID for additional security group |
| labels.extra_security_groups[].security_group_ids | Body | String | X | Additional security group UUID list (comma-separated) |
| labels.extra_volumes | Body | Array | X | Additional block storage object list |
| labels.extra_volumes[].volume_type | Body | String | X | Additional block storage type |
| labels.extra_volumes[].volume_size | Body | Integer | X | Additional block storage size (GB) |
| labels.extra_volumes[].volume_key_id | Body | String | X | (When using encrypted block storage) Symmetric key ID to apply to encrypted block storage |
| labels.extra_volumes[].volume_appkey | Body | String | X | (When using encrypted block storage) App key of symmetric key to apply to encrypted block storage |
| labels.extra_volumes[].volume_mount_path | Body | String | X | Mount path for additional block storage |
| labels.fip_auto_bind_enable | Body | String | X | Floating IP auto-assignment: Enable feature ("True" / "False") |
| labels.fip_bind_subnet | Body | String | X | Floating IP auto-assignment: Subnet of network interface to which Floating IP is attached |
| labels.fip_selector | Body | String | X | Floating IP auto-assignment: Identifier to select Floating IP to assign to the node |
| labels.k8s_node_labels | Body | String | X | Kubernetes label setting |
| name | BODY | String | O | Node group name |
| node_count | Body | Integer | X | Number of nodes (default: 1) |


<details><summary>Example</summary>
<p>

```json
{
    "name": "added-nodegroup",
    "node_count": 1,
    "flavor_id": "6ef27f21-c774-4c0e-84ff-7dd4a762571f",
    "image_id": "f462a2a5-ba24-46d6-b7a1-9a9febcd3cfc",
    "labels": {
        "availability_zone": "kr2-pub-b",
        "boot_volume_size": "20",
        "boot_volume_type": "General HDD",
        "ca_enable": "false"
    }
}
```

</p>
</details>

#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| uuid | Body | UUID | Node group UUID |
| cluster_id | Body  | UUID | Cluster UUID to which the node group belongs |
| flavor_id | Body | UUID | Instance type UUID used by the node |
| image_id | Body | UUID | Base image UUID used by the node |
| labels | Body | Object | Node group creation information object |
| labels.availability_zone | Body | String | Default worker node group setting: Availability Zone |
| labels.boot_volume_type | Body | String | Default worker node group setting: Block storage type |
| labels.boot_volume_size | Body | String | Default worker node group setting: Block storage size (GB) |
| labels.ca_enable | Body | String | Default worker node group setting: Cluster autoscaler: Enable feature ("True" / "False") |
| labels.ca_pod_replicas | Body | String | Default worker node group setting: Cluster autoscaler: Pod replicas |
| labels.ca_max_node_count | Body | String | Default worker node group setting: Cluster autoscaler: Maximum node count |
| labels.ca_min_node_count | Body | String | Default worker node group setting: Cluster autoscaler: Minimum node count |
| labels.ca_scale_down_enable | Body | String | Default worker node group setting: Cluster autoscaler: Enable scale down ("True" / "False") |
| labels.ca_scale_down_unneeded_time | Body | String | Default worker node group setting: Cluster autoscaler: Threshold retention time |
| labels.ca_scale_down_util_thresh | Body | String | Default worker node group setting: Cluster autoscaler: Resource usage threshold |
| labels.ca_scale_down_delay_after_add | Body | String | Default worker node group setting: Cluster autoscaler: Scale down delay after scale up |
| labels.mba_scale_out | Body | String | Default node group setting: Metric-based autoscaler scale-out policy |
| labels.mba_scale_in | Body | String | Default node group setting: Metric-based autoscaler scale-in policy |
| labels.user_script | Body | String | User script (old) |
| labels.user_script_v2 | Body | String | User script |
| labels.additional_network_id_list | Body | String | Worker node group setting: VPC network UUID list for additional networks (colon-separated) |
| labels.additional_subnet_id_list | Body | String | Worker node group setting: VPC subnet UUID list for additional networks (colon-separated) |
| max_node_count | Body | Integer | Maximum node count |
| min_node_count | Body | Integer | Minimum node count |
| name | BODY | String | Node group name |
| node_count | Body | Integer | Number of nodes (default: 1) |
| project_id | Body | String | Project (tenant) ID |
| role | Body | String | Node group role |

<details><summary>Example</summary>
<p>

```json
{
    "cluster_id": "96742ac4-02e7-4b1d-a242-02876c0bd3f8",
    "flavor_id": "6ef27f21-c774-4c0e-84ff-7dd4a762571f",
    "image_id": "f462a2a5-ba24-46d6-b7a1-9a9febcd3cfc",
    "labels": {
        "availability_zone": "kr2-pub-b",
        "boot_volume_size": "20",
        "boot_volume_type": "General HDD",
        "ca_enable": "false",
        "ca_pod_replicas": "1",
        "ca_max_node_count": "10",
        "ca_min_node_count": "1",
        "ca_scale_down_enable": "true",
        "ca_scale_down_unneeded_time": "10",
        "ca_scale_down_util_thresh": "50",
        "clusterautoscale": "nodegroupfeature",
        "user_script_v2": ""
    },
    "links": [
        {
            "href": "https://kr1-api-kubernetes-infrastructure.gov-nhncloudservice.com/v1/clusters/96742ac4-02e7-4b1d-a242-02876c0bd3f8/nodegroups/a3366f2f-a1f3-45ef-8390-10536e8060ff",
            "rel": "self"
        },
        {
            "href": "https://kr1-api-kubernetes-infrastructure.gov-nhncloudservice.com/clusters/96742ac4-02e7-4b1d-a242-02876c0bd3f8/nodegroups/a3366f2f-a1f3-45ef-8390-10536e8060ff",
            "rel": "bookmark"
        }
    ],
    "max_node_count": null,
    "min_node_count": 1,
    "name": "added-nodegroup",
    "node_count": 1,
    "project_id": "1ffeaca9bbf94ab1aa9cffdec29a258a",
    "role": "worker",
    "uuid": "a3366f2f-a1f3-45ef-8390-10536e8060ff"
}
```

</p>
</details>

### Delete node groups

Delete the specified node group.
```
DELETE /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | Cluster UUID or cluster name | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | Node group UUID or node group name | 

#### Response

This API does not return a response body.

### Stop node

Stop the specified nodes.

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}/stop_node
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | Cluster UUID or cluster name | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | Node group UUID or node group name | 
| node_list | Body | String | O | List of node instance UUIDs separated by colon (`:`) |

<details><summary>Example</summary>
<p>

```json
{
    "node_list": "bdaa560c-7a30-4249-9438-2df27fa1e9d38:68ff49ee-4111-4212-8e9e-88835cb0ebaa"
}
```

</p>
</details>


#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| uuid | Body | UUID | Node group UUID |

<details><summary>Example</summary>
<p>

```json
{
    "uuid": "439aa682-398f-4061-a4d1-116da6b1154e"
}
```

</p>
</details>

### Start node

Starts the specified nodes.

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}/start_node
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | Cluster UUID or cluster name |
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | Node group UUID or node group name |
| node_list | Body | String | O | Node instance UUID list separated by colon (`:`) |

<details><summary>Example</summary>
<p>

```json
{
    "node_list": "bdaa560c-7a30-4249-9438-2df27fa1e9d38"
}
```

</p>
</details>

#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| uuid | Body | UUID | Node group UUID |

<details><summary>Example</summary>
<p>

```json
{
    "uuid": "439aa682-398f-4061-a4d1-116da6b1154e"
}
```

</p>
</details>

### View cluster autoscaler settings for a node group

View the cluster autoscaler settings for the node group.

```
GET /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}/autoscale
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | Cluster UUID or cluster name | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | Node group UUID or node group name | 


#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| ca_enable | Body | String | Enable status ("True" / "False") |
| ca_pod_replicas | Body | String | Number of pods |
| ca_max_node_count | Body | String | Maximum node count |
| ca_min_node_count | Body | String | Minimum node count |
| ca_scale_down_enable | Body | String | Scale-down enable status ("True" / "False") |
| ca_scale_down_unneeded_time | Body | String | Unneeded node holding time |
| ca_scale_down_util_thresh | Body | String | Resource usage threshold |
| ca_scale_down_delay_after_add | Body | String | Scale-down delay after node addition |

<details><summary>Example</summary>
<p>

```json
{
    "ca_enable": true,
    "ca_pod_replicas": 1,
    "ca_max_node_count": 10,
    "ca_min_node_count": 2,
    "ca_scale_down_delay_after_add": 10,
    "ca_scale_down_enable": true,
    "ca_scale_down_unneeded_time": 10,
    "ca_scale_down_util_thresh": 50,
    "clusterautoscale": "nodegroupfeature"
}
```

</p>
</details>

### Modifying Cluster Autoscaler Settings for Node Groups

Modify the cluster autoscaler settings for a node group.

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}/autoscale
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | Cluster UUID or cluster name | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | Node group UUID or node group name | 
| ca_enable | Body | String | O | Whether to enable the feature ("True" / "False") |
| ca_pod_replicas | Body | String | X | Number of pods |
| ca_max_node_count | Body | String | X | Maximum node count |
| ca_min_node_count | Body | String | X | Minimum node count |
| ca_scale_down_enable | Body | String | X | Whether to enable scale-down ("True" / "False") |
| ca_scale_down_unneeded_time | Body | String | X | Unneeded node retention duration |
| ca_scale_down_util_thresh | Body | String | X | Resource usage threshold |
| ca_scale_down_delay_after_add | Body | String | X | Scale-down delay time after scale-up |

<details><summary>Example</summary>
<p>

```json
{
    "ca_enable": true,
    "ca_pod_replicas": 1,
    "ca_max_node_count": 10,
    "ca_min_node_count": 1,
    "ca_scale_down_delay_after_add": 30,
    "ca_scale_down_enable": true,
    "ca_scale_down_unneeded_time": 20,
    "ca_scale_down_util_thresh": 40
}
```

</p>
</details>



#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| uuid | Body | UUID | Node group UUID |

<details><summary>Example</summary>
<p>

```json
{
    "uuid": "018b06c5-1293-4081-8242-167a1cb9f262"
}
```

</p>
</details>

### Changing Metric-Based Autoscaler Configuration for Node Groups

Change the metric-based autoscaler configuration for node groups.

```
PATCH /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | Cluster UUID or cluster name | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | Node group UUID or node group name | 
| type | Body | String | O | metric_base_autoscale configuration |
| mba_scale_out | Body | Object | X | Metric-based autoscale scale-out policy configuration |
| mba_scale_out.enable | Body | Boolean | X | Whether to enable the feature (true / false) |
| mba_scale_out.max_node_count | Body | Integer | X | Maximum node count for the node group |
| mba_scale_out.rules_operator | Body | String | X | Operator applied between scale-out rules ("AND" / "OR") |
| mba_scale_out.delay | Body | Integer | X | Scale-out waiting time (1 ~ 60) minutes |
| mba_scale_out.adjustment_count | Body | Integer | X | Number of nodes to scale out (1 ~ 10) |
| mba_scale_out.rules | Body | List of Object | At least one rule is required when mba_scale_out.enable is set to true | Scale-out rule configuration |
| mba_scale_out.rules.metric | Body | Integer | Required when mba_scale_out.enable is set to true | Metric configuration |
| mba_scale_out.rules.threshold | Body | Integer | Required when mba_scale_out.enable is set to true | Threshold configuration |
| mba_scale_out.rules.duration | Body | Integer | Required when mba_scale_out.enable is set to true | Duration to maintain threshold zone (2 ~ 60) minutes |
| mba_scale_in | Body | Object | X | Metric-based autoscale scale-in policy configuration |
| mba_scale_in.enable | Body | Boolean | X | Whether to enable the feature (true / false) |
| mba_scale_in.min_node_count | Body | Integer | X | Minimum node count for the node group |
| mba_scale_in.rules_operator | Body | String | X | Operator applied between scale-in rules ("AND" / "OR") |
| mba_scale_in.delay | Body | Integer | X | Scale-in waiting time (1 ~ 60) minutes |
| mba_scale_in.adjustment_count | Body | Integer | X | Number of nodes to scale in (1 ~ 10) |
| mba_scale_in.rules | Body | List of Object | At least one rule is required when mba_scale_in.enable is set to true | Scale-in rule configuration |
| mba_scale_in.rules.metric | Body | Integer | Required when mba_scale_in.enable is set to true | Metric configuration |
| mba_scale_in.rules.threshold | Body | Integer | Required when mba_scale_in.enable is set to true | Threshold configuration |
| mba_scale_in.rules.duration | Body | Integer | Required when mba_scale_in.enable is set to true | Duration to maintain threshold zone (2 ~ 60) minutes |

##### Metric Configuration List

| System Resource | Configuration Value |
| --- | --- |
| CPU Usage | GROUP_CPU_USAGE |
| Memory Usage | GROUP_MEMORY_USAGE |
| Disk Transfer Rate (Read) | GROUP_DISK_READ_BYTES |
| Disk Transfer Rate (Write) | GROUP_DISK_WRITE_BYTES |
| Network Transfer Rate (Sent) | GROUP_NETWORK_SENT_BYTES |
| Network Transfer Rate (Received) | GROUP_NETWORK_RECV_BYTES |


<details><summary>Activation Example</summary>
<p>

```json
{
    "type": "metric_base_autoscale",
    "mba_scale_out": {
        "enable": "True",
        "max_node_count": 6,
        "rules_operator": "or",
        "delay": 10,
        "adjustment_count": 2,
        "rules": [
            {
                "metric": "GROUP_CPU_USAGE",
                "threshold": 80,
                "duration": 2
            }
        ]
    },
    "mba_scale_in": {
        "enable": "True",
        "min_node_count": 2,
        "rules_operator": "or",
        "delay": 5,
        "adjustment_count": 1,
        "rules": [
            {
                "metric": "GROUP_CPU_USAGE",
                "threshold": 50,
                "duration": 2
            }
        ]
    }
}
```

</p>
</details>


<details><summary>Deactivation Example</summary>
<p>

```json
{
    "type": "metric_base_autoscale",
    "mba_scale_out": {
        "enable": false
    },
    "mba_scale_in": {
        "enable": false
    }
}
```

</p>
</details>


#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| uuid | Body | UUID | Node group UUID |

<details><summary>Example</summary>
<p>

```json
{
    "uuid": "018b06c5-1293-4081-8242-167a1cb9f262"
}
```

</p>
</details>

### Cluster Upgrade

Upgrade the cluster.

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}/upgrade
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | Cluster UUID or cluster name | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | Node group UUID or node group name<br>For control plane upgrade, specify **default-master** | 
| version | Body | String | O | Kubernetes version |
| num_buffer_nodes | Body | Integer | X | Number of buffer nodes. Minimum: 0, Maximum: (maximum node quota per worker node group - current number of nodes in the corresponding worker node group), Default: 1 |
| num_max_unavailable_nodes | Body |  Integer | X | Maximum number of unavailable nodes. Minimum: 1, Maximum: current number of nodes in the corresponding worker node group, Default: 1 |

To upgrade the cluster, you must first upgrade the control plane and then upgrade worker components. Control plane and worker component upgrades are performed on a node group basis.

* Control plane component upgrade
    * Specify the node group name as **default-master**.

* Worker component upgrade
    * Specify the node group name to upgrade.


<details><summary>Example</summary>
<p>

```json
{
    "version": "v1.19.13"
}
```

</p>
</details>


#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| uuid | Body | UUID | Node group UUID |

<details><summary>Example</summary>
<p>

```json
{
    "uuid": "018b06c5-1293-4081-8242-167a1cb9f262"
}
```

</p>
</details>

### Changing User Scripts

Modifies the user script for a node group.

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}/userscript
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | Cluster UUID or cluster name | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | Node group UUID or node group name<br>When upgrading control plane components, specify as **default-master** | 
| contents | Body | String | O | User script contents |


<details><summary>Example</summary>
<p>

```json
{
    "contents": "user script contents here..."
}
```

</p>
</details>


#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| uuid | Body | UUID | Node group UUID |

<details><summary>Example</summary>
<p>

```json
{
    "uuid": "018b06c5-1293-4081-8242-167a1cb9f262"
}
```

</p>
</details>

### Change Instance Type

Change the instance type of a node group.

```
PATCH /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | Cluster UUID or cluster name | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | Node group UUID or node group name | 
| type | Body | String | O | Set to `flavor_id` |
| flavor_id | Body | String | O | Instance type UUID |
| num_buffer_nodes | Body | Integer | X | Number of buffer nodes. Minimum: 0, Maximum: (Maximum node quota per worker node group - Current number of nodes in the worker node group), Default: 1 |
| num_max_unavailable_nodes | Body |  Integer | X | Maximum unavailable nodes. Minimum: 1, Maximum: Current number of nodes in the worker node group, Default: 1 |


<details><summary>Example</summary>
<p>

```json
{
    "type": "flavor_id",
    "flavor_id": "1d0d6983-8e9d-44dc-810e-d7689afa372c",
    "num_buffer_nodes": 1,
    "num_max_unavailable_nodes":1
}
```

</p>
</details>


#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| uuid | Body | UUID | Node group UUID |

<details><summary>Example</summary>
<p>

```json
{
    "uuid": "018b06c5-1293-4081-8242-167a1cb9f262"
}
```

</p>
</details>

### Change Node Group Floating IP Auto Assignment Configuration

Change the floating IP auto assignment configuration for a node group.

```
PATCH /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | Cluster UUID or cluster name | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | Node group UUID or node group name | 
| type | Body | String | O | Set to `fip_auto_bind` |
| fip_auto_bind_update_info | Body | Object | O | Floating IP auto assignment configuration information object |
| fip_auto_bind_update_info.fip_auto_bind_enable | Body | Boolean | O | Whether to enable the feature (true/false) |
| fip_auto_bind_update_info.fip_bind_subnet | Body | String | X | Subnet of the network interface to which the floating IP is connected. <br> The subnet to connect must be either the cluster's default subnet or included in the node group's additional subnets. |
| fip_auto_bind_update_info.fip_selector | Body | String | X | Identifier to select floating IPs to be assigned to nodes |


<details><summary>Example</summary>
<p>

```json
{
    "type": "fip_auto_bind",
    "fip_auto_bind_update_info": {
        "fip_auto_bind_enable": true,
        "fip_selector": "nks-fip",
        "fip_bind_subnet": "7f3237f6-ce05-4e9c-bce8-bbaabd22e83a"
    }
}
```

</p>
</details>


#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| uuid | Body | UUID | Node group UUID |

<details><summary>Example</summary>
<p>

```json
{
    "uuid": "018b06c5-1293-4081-8242-167a1cb9f262"
}
```

</p>
</details>

### Change Kubernetes Label Settings for Node Groups

Change the Kubernetes label settings for node groups.

```
PATCH /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | Cluster UUID or cluster name | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | Node group UUID or node group name | 
| type | Body | String | O | Set to `k8s_node_labels` |
| k8s_node_labels | Body | Object | O | Configuration information object composed of Kubernetes label key-value pairs. Up to 20 settings can be configured |


<details><summary>Example</summary>
<p>

```json
{
    "type": "k8s_node_labels",
    "k8s_node_labels": {
        "node_type": "production",
        "pod_type": "opt"
    }
}
```

</p>
</details>


#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| uuid | Body | UUID | Node group UUID |

<details><summary>Example</summary>
<p>

```json
{
    "uuid": "018b06c5-1293-4081-8242-167a1cb9f262"
}
```

</p>
</details>

---

## Add-on Management Features

### View Add-on Types Provided by NHN Cloud
You can view the add-on types provided by NHN Cloud.

```
GET /v1/addon_types/${ADDON_TYPE_UUID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| ADDON_TYPE_UUID_OR_NAME | URL | UUID or String | O | UUID or name of the add-on type |

#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| uuid | Body | UUID | Add-on type UUID |
| name | Body | String | Add-on type name |
| mandatory | Body | boolean | Whether it is required |

<details><summary>Example</summary>
<p>

```json
{
    "uuid": "123e4567-e89b-12d3-a456-426614174001",
    "name": "cni",
    "mandatory": true
}
```

</p>
</details>

### View the list of add-on types provided by NHN Cloud
You can view the list of add-on types provided by NHN Cloud.

```
GET /v1/addon_types/
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request
| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |

#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| addon_types | Body | List of object | List of add-on type information |

<details><summary>Example</summary>
<p>

```json
{
    "addon_types": [
        {"uuid": "123e4567-e89b-12d3-a456-426614174001", "name": "cni", "mandatory": true},
        {"uuid": "123e4567-e89b-12d3-a456-426614174003", "name": "kube-dns", "mandatory": true}
    ]
}
```

</p>
</details>

### View NHN Cloud-Provided Add-ons
You can view add-ons provided by NHN Cloud.

```
GET /v1/addons/{ADDON_UUID}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| ADDON_UUID | URL | UUID | O | Add-on UUID |
| tokenId | Header | String | O | Token ID |


#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| uuid | Body | UUID | Add-on type UUID |
| type | Body | String | Add-on type name |
| version | Body | String | Add-on version |
| name | Body | String | Add-on name |
| deploy_target | Body | String | (Internal use) Add-on deployment type |
| k8s_min_version | Body | String | (Internal use) Supported minimum k8s version |
| k8s_max_version | Body | String | (Internal use) Supported maximum k8s version |
| description | Body | String | Add-on description |
| option_schemas | Body | List of object | List of option definitions |
| option_schemas.name | Body | String | Option name |
| option_schemas.data_type | Body | String | Option data type. One of `STRING`, `INTEGER`, or `SELECT` |
| option_schemas.default | Body | String | Option default value |
| option_schemas.updatable | Body | Boolean | Whether the option is updatable |
| option_schemas.mandatory | Body | Boolean | Whether the option is mandatory |
| option_schemas.choices | Body | List of String | List of selectable values |


<details><summary>Example</summary>
<p>

```json
{
    "uuid": "23454567-1234-12d3-a456-426614174001",
    "type": "cni",
    "version": "v3.28.2-nks1",
    "name": "calico",
    "option_schemas": [
        {
            "name": "mode",
            "data_type": "SELECT",
            "default": "vxlan",
            "updatable": false,
            "mandatory": false,
            "choices": ["vxlan", "ebpf"]
        }
    ],
    "k8s_min_version": "v1.26.0",
    "k8s_max_version": null,
    "description": "Calico is a CNI plugin for Kubernetes that provides networking and network security."
}
```

</p>
</details>

### View list of add-ons provided by NHN Cloud
You can view the list of add-ons provided by NHN Cloud.

```
GET /v1/addons/
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| k8s_version | Query | String | X | Kubernetes version (e.g., `v1.30.0`). If specified, only add-ons compatible with that version are returned. |
| image | Query | UUID | X | Base image UUID. If specified, only add-ons that can be installed on that image are returned. |
| platform_version | Query | String | X | Platform version (e.g., `1.202605.0`). If specified, only add-ons available for that platform version are returned. |

#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| addons | Body | List of object | List of add-on information |


<details><summary>Example</summary>
<p>

```json
{
    "addons": [
        {"uuid": "23454567-1234-12d3-a456-426614174001", "type": "cni", "version": "v3.28.2-nks1", "name": "calico", "option_schemas": [{"name": "mode", "data_type": "SELECT", "default": "vxlan", "updatable": false, "mandatory": false, "choices": ["vxlan", "ebpf"]}], "k8s_min_version": "v1.26.0", "k8s_max_version": null, "description": "Calico is a CNI plugin for Kubernetes that provides networking and network security."},
        {"uuid": "23454567-1234-12d3-a456-426614174005", "type": "kube-dns", "version": "1.8.4-nks1", "name": "coredns", "option_schemas": [], "k8s_min_version": "v1.26.0", "k8s_max_version": null, "description": "CoreDNS is the default DNS server for Kubernetes clusters."}
    ]
}


```

</p>
</details>

### View Add-ons Installed on a Cluster

You can view the add-ons installed on a cluster.

```
GET /v1/clusters/{CLUSTER_ID_OR_NAME}/addons/{ADDON_UUID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | Cluster UUID or cluster name |
| ADDON_UUID_OR_NAME | URL | UUID or String | O | Add-on UUID or add-on name |


#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| uuid | Body | UUID | Add-on type UUID |
| project_id | Body | String | Project ID |
| cluster_uuid | Body | UUID | Cluster UUID |
| cluster_name | Body | String | Cluster name |
| type | Body | String | Add-on type name |
| version | Body | String | Add-on version |
| options | Body | Object | Add-on-specific options |
| name | Body | String | Add-on name |
| status | Body | String | Add-on status |
| status_reason | Body | String | Add-on status reason |
| scope | Body | String | Scope of application |
| target_uuid | Body | UUID | Target UUID |
| created_at | Body | String | Creation time (UTC) |
| updated_at | Body | String | Last updated time (UTC) |

<details><summary>Example</summary>
<p>

```json
{
    "uuid": "0b29e253-fb0d-4888-a8fe-d287c65ba76b",
    "project_id": "1ffeaca9bbf94ab1aa9cffdec29a258a",
    "cluster_uuid": "6c1284e2-8ead-46a7-ace9-c19d6eec76b3",
    "cluster_name": "tw-addon3",
    "type": "cni",
    "version": "v3.28.2-nks1",
    "options": {"mode": "vxlan"},
    "name": "calico",
    "status": "UPDATE_COMPLETE",
    "status_reason": null,
    "scope": "cluster",
    "target_uuid": "6c1284e2-8ead-46a7-ace9-c19d6eec76b3",
    "created_at": "2025-04-25T15:11:48+00:00",
    "updated_at": "2025-04-25T15:17:16+00:00"
}
```

</p>
</details>

### View the list of add-ons installed in the cluster
You can view the list of add-ons installed in the cluster.

```
GET /v1/clusters/{CLUSTER_ID_OR_NAME}/addons/
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | Cluster UUID or cluster name |

#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| addons | Body | List of Object | List of installed add-on information |

<details><summary>Example</summary>
<p>

```json
{
    "addons": [
        {"uuid": "0b29e253-fb0d-4888-a8fe-d287c65ba76b", "project_id": "1ffeaca9bbf94ab1aa9cffdec29a258a", "cluster_uuid": "6c1284e2-8ead-46a7-ace9-c19d6eec76b3", "cluster_name": "tw-addon3", "type": "cni", "version": "v3.28.2-nks1", "options": {"mode": "vxlan"}, "name": "calico", "status": "UPDATE_COMPLETE", "status_reason": null, "scope": "cluster", "target_uuid": "6c1284e2-8ead-46a7-ace9-c19d6eec76b3", "created_at": "2025-04-25T15:11:48+00:00", "updated_at": "2025-04-25T15:17:16+00:00"},
        {"uuid": "be71a120-7596-4b25-bee5-d5317e5134ee", "project_id": "1ffeaca9bbf94ab1aa9cffdec29a258a", "cluster_uuid": "6c1284e2-8ead-46a7-ace9-c19d6eec76b3", "cluster_name": "tw-addon3", "type": "kube-dns", "version": "1.8.4-nks1", "options": {}, "name": "coredns", "status": "UPDATE_FAILED", "status_reason": null, "scope": "cluster", "target_uuid": "6c1284e2-8ead-46a7-ace9-c19d6eec76b3", "created_at": "2025-05-02T06:16:39+00:00", "updated_at": "2025-05-08T01:03:19+00:00"}
    ]
}
```

</p>
</details>

### Install add-ons on cluster
Install add-ons on the cluster.

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/addons/
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | Cluster UUID or cluster name |
| name | Body | String | O | Add-on name |
| version | Body | String | O | Add-on version |
| resolve_conflicts | Body | String | O | Conflict option. One of `none`, `overwrite`, `preserve` |


<details><summary>Example</summary>
<p>

```json
{"version": "1.8.4-nks1", "name": "coredns", "resolve_conflicts": "overwrite"}
```

</p>
</details>


#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| uuid | Body | UUID | Cluster UUID |

<details><summary>Example</summary>
<p>

```json
{
    "uuid": "6c1284e2-8ead-46a7-ace9-c19d6eec76b3"
}
```

</p>
</details>

### Update addon in cluster
Updates the addon installed in a cluster.

```
PATCH /v1/clusters/{CLUSTER_ID_OR_NAME}/addons/{ADDON_UUID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | Cluster UUID or cluster name |
| ADDON_UUID_OR_NAME | URL | UUID or String | O | Addon UUID or addon name |
| version | Body | String | O | Addon version |
| resolve_conflicts | Body | String | O | Conflict resolution option. One of `none`, `overwrite`, `preserve` |


<details><summary>Example</summary>
<p>

```json
{"version": "1.8.4-nks1", "resolve_conflicts": "none"}
```

</p>
</details>


#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| uuid | Body | UUID | Cluster UUID |

<details><summary>Example</summary>
<p>

```json
{
    "uuid": "6c1284e2-8ead-46a7-ace9-c19d6eec76b3"
}
```

</p>
</details>

### Remove addon from cluster
Removes the addon installed in the cluster.

```
DELETE /v1/clusters/{CLUSTER_ID_OR_NAME}/addons/{ADDON_UUID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | Cluster UUID or cluster name |
| ADDON_UUID_OR_NAME | URL | UUID or String | O | Addon UUID or addon name |

#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| uuid | Body | UUID | Cluster UUID |

<details><summary>Example</summary>
<p>

```json
{
    "uuid": "6c1284e2-8ead-46a7-ace9-c19d6eec76b3"
}
```

</p>
</details>

## Other Features

### View supported Kubernetes versions

Retrieve the Kubernetes versions supported by NHN Kubernetes Service (NKS).

```
GET /v1/supports
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |


#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| supported_k8s | Body | Object | Supported Kubernetes versions object |
| supported_k8s."version name" | Body | String | Validity of the Kubernetes version (True/False) |
| supported_event_type."task type"| Body | Object | Supported task type object (cluster_events/nodegroup_events) |
| supported_event_type."task type"."task name"| Body | Object | Task type and description |

<details><summary>Example</summary>
<p>

```json
{
    "supported_k8s": {
        "v1.17.6": false,
        "v1.18.19": false,
        "v1.19.13": false,
        "v1.20.12": false,
        "v1.21.6": false,
        "v1.22.3": false,
        "v1.23.3": false,
        "v1.24.3": false,
        "v1.25.4": false,
        "v1.26.3": false,
        "v1.27.3": false,
        "v1.28.3": false,
        "v1.29.3": true,
        "v1.30.3": true,
        "v1.31.4": true,
        "v1.32.3": true
    },
    "supported_event_type": {
        "CLUSTER_CREATE": "Create cluster",
        "CLUSTER_DELETE": "Delete cluster",
        "CLUSTER_HANDOVER": "Change cluster OWNER",
        "CLUSTER_UPDATE_VM_AUTH_KEY": "Update key pair",
        "NODEGROUP_CREATE": "Create node group",
        "NODEGROUP_DELETE": "Delete node group",
        "CLUSTER_RESIZE": "Resize cluster",
        "NODEGROUP_SCALE_OUT": "Scale out nodes",
        "NODEGROUP_SCALE_IN": "Scale in nodes",
        "NODEGROUP_UPDATE_FLAVOR": "Change instance type",
        "NODEGROUP_UPGRADE": "Upgrade node group",
        "NODEGROUP_USERSCRIPT_UPDATE": "Update user script",
        "NODEGROUP_SET_CLUSTER_AUTOSCALER": "Change cluster autoscaler settings",
        "NODEGROUP_SET_METRIC_BASE_AUTOSCALER": "Change metric-based autoscaler settings",
        "NODEGROUP_METRIC_BASE_AUTOSCALER_SCALE_OUT": "Scale out nodes through metric-based autoscaler",
        "NODEGROUP_METRIC_BASE_AUTOSCALER_SCALE_IN": "Scale in nodes through metric-based autoscaler",
        "CLUSTER_API_EP_IPACL_UPDATE": "Change cluster API endpoint IP access control",
        "NODEGROUP_NODE_ACTION_START_NODE": "Start worker node",
        "NODEGROUP_NODE_ACTION_STOP_NODE": "Stop worker node",
        "CLUSTER_UPDATE_SGW": "Change cluster service gateway",
        "CLUSTER_ROTATE_CERTIFICATE": "Renew cluster certificate",
        "CLUSTER_UPDATE_NKS_REGISTRY": "Enable NKS registry",
        "NODEGROUP_UPDATE_EXTRA_VOLUME": "Change additional block storage",
        "NODEGROUP_UPDATE_EXTRA_SECURITY_GROUP": "Change additional security group",
        "CLUSTER_UPDATE_K8S_ARGS": "Change Kubernetes component options",
        "CLUSTER_UPDATE_OIDC_ARGS": "Change OIDC settings",
        "NODEGROUP_UPDATE_K8S_NODE_LABELS": "Change node group Kubernetes label settings",
        "CLUSTER_INSTALL_ADDON": "Install addon",
        "CLUSTER_UNINSTALL_ADDON": "Uninstall addon",
        "CLUSTER_UPDATE_ADDON": "Update addon",
        "CLUSTER_UPDATE_CONTROL_PLANE_LOG": "Update control plane log collection",
        "NODEGROUP_UPDATE_FIP_AUTO_BIND": "Change node group floating IP auto-assignment settings",
        "K8S_API_NOT_WORKING": "kube-apiserver stopped",
        "ALL_NODES_NOT_READY": "All nodes stopped",
        "AUTO_HEALING": "Auto healing"
    }
}
```

</p>
</details>