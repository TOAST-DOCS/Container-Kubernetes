## Container > NHN Kubernetes Service (NKS) > API v2 Guide

This guide describes the API for configuring Kubernetes clusters.
To use the API, you need an API endpoint, token, etc. Refer to [API Preparations](/Compute/Compute/en/identity-api/) to prepare the necessary information to use the API.

All API calls are made using the `kubernetes` type endpoint.

| Type | Region | Endpoint |
|---|---|---|
| kubernetes | Korea (Pangyo) Region <br> Korea (Pyeongchon) Region | https://kr1-api-kubernetes.infrastructure.cloud.toast.com <br>https://kr2-api-kubernetes.infrastructure.cloud.toast.com |


Fields not specified in the guide may appear in API responses. These fields are used for internal use by NHN Cloud and are subject to change without prior notice, so we advise you not to use them.

## Check the Information of Resources Used in API

The NHN Kubernetes Service (NKS) API uses several resources for configuring clusters and node groups. You can check the information of each resource as follows.

### UUID of the VPC Network Attached to the Internet Gateway

You can query the VPC network attached to the internet gateway by using the **router:external=True** query parameter in the VPC network list query API.

```
GET /v2.0/networks?router:external=True
```

For more information about the network list query API, refer to [List Networks](/Network/VPC/en/public-api/#list-networks).


### List of UUIDs of Subnets Attached to the Internet Gateway

Enter the UUID of subnet associated with the VPC network attached to the internet gateway. If multiple subnets are found, enter them by concatenating them with a colon (`:`). For more information about the subnet list query API, refer to [List Subnets](/Network/VPC/en/public-api/#list-subnets).


### VPC Network UUID

Enter the UUID of internal VPC network to associate with the node. For more information about the network list query API, refer to [List Networks](/Network/VPC/en/public-api/#list-networks).

### VPC Subnet UUID

Enter the UUID of subnet associated with the internal VPC network to associate with the node. For more information about the subnet list query API, refer to [List Subnets](/Network/VPC/en/public-api/#list-subnets).

### Availability Zone UUID

Enter the UUID of availability zone in which to create the node. For more information about the availability zone list query API, see [List Availability Zones](/Compute/Instance/en/public-api/#list-availability-zones).

### Key Pair UUID

Enter the key pair to use when connecting to the node. For more information about the key pair list query API, refer to [List Key Pairs](/Compute/Instance/en/public-api/#list-key-pairs).

### Base Image UUID

Enter the base image UUID to use for creating a node. The base image name and UUID for each region is as follows:

| Region | Base Image Name | Base Image UUID |
|---|---|---|
| Korea (Pangyo) Region | CentOS 7.8 | 6013253a-50bf-4580-bf28-322e180a5eec |
|  | Ubuntu Server 18.04 LTS | 2717ec03-3a4d-4728-b372-183065facdba|
| Korea (Pyeongchon) Region | CentOS 7.8 | 5bb2452d-ee50-48e5-a9b1-ab6c2928fac3 |
|  | Ubuntu Server 18.04 LTS | b2f577f7-9d5e-4ef8-a2e0-94991a1c2d58 |

### Block Storage Type

Enter the block storage UUID to use for the node. For more information about the block storage type list query API, refer to [List Volume Types](/Storage/Block%20Storage/en/public-api/#list-volume-types) .

### Flavor UUID

Enter the UUID of flavor for the node to be created. For more information about the flavor list query API, refer to [List Flavors](/Compute/Instance/en/public-api/#list-flavors).



## Cluster

### List Clusters

Retrieves a list of clusters.

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
| clusters | Body | Array | Cluster information object list |
| clusters.uuid | Body | UUID | Cluster UUID |
| clusters.name | Body | String | Cluster name |
| clusters.flavor_id | Body | UUID | UUID of the flavor for the default worker node|
| clusters.keypair | Body | UUID | UUID of the key pair applied to the default worker node group |
| clusters.node_count | Body | Integer| Total number of worker nodes |
| clusters.stack_id | Body | UUID | UUID of the heat stack associated with the master node group |
| clusters.status | Body | String | Cluster status |
| clusters.labels | Body | Object | Cluster label |
| clusters.labels.kube_tag | Body |String | Kubernetes version of the master node group |
| clusters.labels.availability_zone | Body | String | Applied to the default worker node group: Availability zone |
| clusters.labels.node_image | Body | UUID | Applied to the default worker node group: Base image UUID |
| clusters.labels.boot_volume_type | Body | String | Applied to the default worker node group: Block storage type|
| clusters.labels.boot_volume_size | Body | String | Applied to the default worker node group: Block storage size (GB) |
| clusters.labels.external_network_id | Body | String | UUID of the VPC network attached to the internet gateway |
| clusters.labels.external_subnet_id_list | Body | String | List of UUIDs of subnets attached to the internet gateway (separated by colons) |
| clusters.labels.cert_manager_api | Body | String | Whether to enable the certificate signing request (CSR) feature. Must be set to "True" |
| clusters.labels.ca_enable | Body | String | Applied to the default worker node group: Autoscaler: Whether to enable the feature ("True" / "False") |
| clusters.labels.ca_max_node_count | Body | String | Applied to the default worker node group: Autoscaler: Maximum number of nodes |
| clusters.labels.ca_min_node_count | Body | String | Applied to the default worker node group: Autoscaler: Minimum number of nodes |
| clusters.labels.ca_scale_down_enable | Body | String | Applied to the default worker node group: Autoscaler: Whether to enable scale-down ("True" / "False") |
| clusters.labels.ca_scale_down_unneeded_time | Body | String | Applied to the default worker node group: Autoscaler: Scale down unneeded time |
| clusters.labels.ca_scale_down_util_thresh | Body | String | Applied to the default worker node group: Autoscaler: Scale down utilization threshold  |
| clusters.labels.ca_scale_down_delay_after_add | Body | String | Applied to the default worker node group: Auto Scaler: Scale down delay after add |
| clusters.labels.user_script | Body | String | User Script (old) |
| clusters.labels.user_script_v2 | Body | String | User Script |
| clusters.labels.master_lb_floating_ip_enabled | Body | String | Whether to create a public domain address for Kubernetes API endpoint ("True" / "False") |


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
                "boot_volume_size": "20",
                "boot_volume_type": "General HDD",
                "ca_enable": "false",
                "ca_max_node_count": "10",
                "ca_min_node_count": "1",
                "ca_scale_down_delay_after_add": "3",
                "ca_scale_down_enable": "true",
                "ca_scale_down_unneeded_time": "3",
                "ca_scale_down_util_thresh": "50",
                "cert_manager_api": "True",
                "clusterautoscale": "nodegroupfeature",
                "etcd_volume_size": "10",
                "external_network_id": "751b8227-7b45-440a-9349-dbf829d0aba5",
                "external_subnet_id_list": "59ddc195-76b1-431d-9693-f09880747dc6",
                "flavor_type": "core",
                "hypervisor_type": "qemu",
                "kube_tag": "v1.23.3",
                "kube_version_status": "NEED_UPGRADE",
                "login_username": "centos",
                "master_lb_floating_ip_enabled": "true",
                "node_image": "f462a2a5-ba24-46d6-b7a1-9a9febcd3cfc",
                "os_arch": "amd64",
                "os_distro": "CentOS",
                "os_type": "linux",
                "os_version": "7.8",
                "project_domain": "NORMAL",
                "server_group_meta": "k8s_2b778d83-8b67-45b1-920e-b0c5ad5c2f30_561c3f55-a23f-4e1a-b2fa-a5459b2c0575",
                "user_script_v2": ""
            },
            "links": [
                {
                    "href": "https://kr2-api-kubernetes.infrastructure.cloud.toast.com/v1/clusters/f0af4484-0a16-433a-a15c-295d9ba6537d",
                    "rel": "self"
                },
                {
                    "href": "https://kr2-api-kubernetes.infrastructure.cloud.toast.com/clusters/f0af4484-0a16-433a-a15c-295d9ba6537d",
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

---

### Get a Cluster

Retrieves information of an individual cluster.

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
| flavor_id | Body | UUID | UUID of the flavor for the default worker node|
| keypair | Body | UUID | UUID of the key pair applied to the default worker node group |
| node_count | Body | Integer| Total number of worker nodes |
| stack_id | Body | UUID | UUID of the heat stack associated with the master node group |
| status | Body | String | Cluster status |
| status_reason | Body | String | Reason for the node group status (can be null) |
| api_address | Body | String | Kubernetes API endpoint |
| project_id | Body | String | Project (tenant) ID |
| fixed_network | Body | UUID | VPC UUID|
| fixed_subnet | Body | UUID | VPC Subnet UUID |
| node_addresses | Body | String List | Worker node IP address list |
| created_at | Body | String | Created time (UTC) |
| updated_at | Body | String | Last updated time (UTC) |
| labels | Body | Object | Cluster label |
| labels.kube_tag | Body |String | Kubernetes version of the master node group |
| labels.availability_zone | Body | String | Applied to the default worker node group: Availability zone |
| labels.node_image | Body | UUID | Applied to the default worker node group: Base image UUID |
| labels.boot_volume_type | Body | String | Applied to the default worker node group: Block storage type|
| labels.boot_volume_size | Body | String | Applied to the default worker node group: Block storage size (GB) |
| labels.external_network_id | Body | String | UUID of the VPC network attached to the internet gateway |
| labels.external_subnet_id_list | Body | String | List of UUIDs of subnets attached to the internet gateway (separated by colons) |
| labels.cert_manager_api | Body | String | Whether to enable the certificate signing request (CSR) feature. Must be set to "True" |
| labels.ca_enable | Body | String | Applied to the default worker node group: Autoscaler: Whether to enable the feature ("True" / "False") |
| labels.ca_max_node_count | Body | String | Applied to the default worker node group: Autoscaler: Maximum number of nodes |
| labels.ca_min_node_count | Body | String | Applied to the default worker node group: Autoscaler: Minimum number of nodes |
| labels.ca_scale_down_enable | Body | String | Applied to the default worker node group: Autoscaler: Whether to enable scale-down ("True" / "False") |
| labels.ca_scale_down_unneeded_time | Body | String | Applied to the default worker node group: Autoscaler: Scale down unneeded time |
| labels.ca_scale_down_util_thresh | Body | String | Applied to the default worker node group: Autoscaler: Scale down utilization threshold  |
| labels.ca_scale_down_delay_after_add | Body | String | Applied to the default worker node group: Auto Scaler: Scale down delay after add |
| labels.user_script | Body | String | User Script (old) |
| labels.user_script_v2 | Body | String | User Script |
| labels.master_lb_floating_ip_enabled | Body | String | Whether to create a public domain address for Kubernetes API endpoint ("True" / "False") |

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
        "boot_volume_size": "20",
        "boot_volume_type": "General HDD",
        "ca_enable": "false",
        "ca_max_node_count": "10",
        "ca_min_node_count": "1",
        "ca_scale_down_delay_after_add": "3",
        "ca_scale_down_enable": "true",
        "ca_scale_down_unneeded_time": "3",
        "ca_scale_down_util_thresh": "50",
        "cert_manager_api": "True",
        "clusterautoscale": "nodegroupfeature",
        "etcd_volume_size": "10",
        "external_network_id": "751b8227-7b45-440a-9349-dbf829d0aba5",
        "external_subnet_id_list": "59ddc195-76b1-431d-9693-f09880747dc6",
        "flavor_type": "core",
        "hypervisor_type": "qemu",
        "kube_tag": "v1.23.3",
        "kube_version_status": "NEED_UPGRADE",
        "login_username": "centos",
        "master_lb_floating_ip_enabled": "true",
        "node_image": "f462a2a5-ba24-46d6-b7a1-9a9febcd3cfc",
        "os_arch": "amd64",
        "os_distro": "CentOS",
        "os_type": "linux",
        "os_version": "7.8",
        "project_domain": "NORMAL",
        "server_group_meta": "k8s_2b778d83-8b67-45b1-920e-b0c5ad5c2f30_561c3f55-a23f-4e1a-b2fa-a5459b2c0575",
        "user_script_v2": ""
    },
    "links": [
        {
            "href": "https://kr2-api-kubernetes.infrastructure.cloud.toast.com/v1/clusters/2b778d83-8b67-45b1-920e-b0c5ad5c2f30",
            "rel": "self"
        },
        {
            "href": "https://kr2-api-kubernetes.infrastructure.cloud.toast.com/clusters/2b778d83-8b67-45b1-920e-b0c5ad5c2f30",
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

---

### Create a Cluster

Creates a cluster.

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
| keypair | Body | String | O | UUID of the key pair applied to the default worker node group |
| name | Body | String | O | Cluster name |
| cluster_template_id | Body | String | O | Cluster template ID. Must be set to "iaas_console" |
| node_count | Body | String | O | Number of nodes to apply to the default worker node group |
| labels | Body | Object | O | Cluster creation information object |
| labels.availability_zone | Body | String | O | Applied to the default worker node group: Availability zone |
| labels.node_image | Body | UUID | O | Applied to the default worker node group: Base image UUID |
| labels.boot_volume_type | Body | String | O | Applied to the default worker node group: Block storage type|
| labels.boot_volume_size | Body | String | O | Applied to the default worker node group: Block storage size (GB) |
| labels.external_network_id | Body | String | X | UUID of the VPC network attached to the internet gateway<br>Must be set when a router associated with a VPC subnet is attached to the internet gateway |
| labels.external_subnet_id_list | Body | String | X | List of UUIDs of subnets attached to the internet gateway (separated by colon)<br>Must be set when a router associated with a VPC subnet is attached to the internet gateway |
| labels.cert_manager_api | Body | String | O | Whether to enable the certificate signing request (CSR) feature. Must be set to "True" |
| labels.ca_enable | Body | String | O | Applied to the default worker node group: Autoscaler: Whether to enable the feature ("True" / "False") |
| labels.ca_max_node_count | Body | String | X | Applied to the default worker node group: Autoscaler: Maximum number of nodes |
| labels.ca_min_node_count | Body | String | X | Applied to the default worker node group: Autoscaler: Minimum number of nodes |
| labels.ca_scale_down_enable | Body | String | X | Applied to the default worker node group: Autoscaler: Whether to enable scale-down ("True" / "False") |
| labels.ca_scale_down_unneeded_time | Body | String | X | Applied to the default worker node group: Autoscaler: Scale down unneeded time |
| labels.ca_scale_down_util_thresh | Body | String | X | Applied to the default worker node group: Autoscaler: Scale down utilization threshold  |
| labels.ca_scale_down_delay_after_add | Body | String | X | Applied to the default worker node group: Auto Scaler: Scale down delay after add |
| labels.kube_tag | Body | String | O | Kubernetes Version |
| labels.user_script | Body | String | X | User Script (old) |
| labels.user_script_v2 | Body | String | X | User Script |
| labels.master_lb_floating_ip_enabled | Body | String | O | Whether to create a public domain address for Kubernetes API endpoint ("True" / "False")<br>Can be set to "True" only when labels.external_network_id and labels.external_subnet_id_list are set |
| flavor_id | Body | UUID | O | Applied to the default worker node group: Node flavor UUID |
| fixed_network | Body | UUID | O | VPC Network UUID |
| fixed_subnet | Body | UUID | O | VPC Subnet UUID |

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
        "node_image": "f462a2a5-ba24-46d6-b7a1-9a9febcd3cfc",
        "user_script_v2": ""
    },
    "name": "test-k8s",
    "node_count": 1
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

### Delete a Cluster

Deletes a Cluster.

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

---

### Resize

Adjusts the number of nodes in the cluster.

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
| nodes_to_remove | Body | String List | X | UUID of the node to delete |

* Caution
    * If you are scaling down nodes (i.e., deleting some nodes), you must set **nodes_to_remove** to specify which nodes to delete. If you do not specify the nodes to delete, the nodes to be deleted are selected randomly.
    * node_count: min 1, max 10 (however, the max value can be adjusted with quota)

<details><summary>Scale-up Example</summary>
<p>

```json
{
    "node_count": 3,
    "nodegroup": "default-worker"
}
```

</p>
</details>

<details><summary>Scale-down Example</summary>
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

---

### Get kubeconfig of a Cluster

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

---

## Node Group

### List Node Groups

Retrieves a list of node groups.

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
| nodegroups.flavor_id | Body | UUID | Node group flavor UUID |
| nodegroups.image_id | Body | UUID | Node group base image UUID |
| nodegroups.max_node_count | Body | Integer | Maximum number of nodes in a node group |
| nodegroups.min_node_count | Body | Integer | Minimum number of nodes in a node group |
| nodegroups.name | Body | String | Node Group Name |
| nodegroups.node_count | Body | Integer | Number of nodes in a node group |
| nodegroups.role | Body | String | Node group role |
| nodegroups.stack_id | Body | UUID | UUID of the heat stack associated with the node group |
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

---

### Get a Node Group

Retrieves information of an individual node group.

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
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | Cluster UUID or cluster name | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | Node group UUID or node group name | 

#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| uuid | Body | UUID | Node group UUID |
| name | Body | String | Node Group Name |
| cluster_id | Body  | UUID | UUID of the cluster to which the node group belongs |
| flavor_id | Body | UUID | UUID of the flavor used by the node |
| image_id | Body | UUID | UUID of the base image used by the node |
| labels | Body | Object | Node group creation information object |
| labels.availability_zone | Body | String | Applied to the worker node group: Availability zone |
| labels.node_image | Body | UUID | Applied to the worker node group: Base image UUID |
| labels.boot_volume_type | Body | String | Applied to the worker node group: Block storage type|
| labels.boot_volume_size | Body | String | Applied to the worker node group: Block storage size (GB) |
| labels.external_network_id | Body | String | UUID of the VPC network attached to the internet gateway |
| labels.external_subnet_id_list | Body | String | List of UUIDs of subnets attached to the internet gateway (separated by colons) |
| labels.cert_manager_api | Body | String | Whether to enable the certificate signing request (CSR) feature. Must be set to "True" |
| labels.ca_enable | Body | String | Applied to the worker node group: Autoscaler: Whether to enable the feature ("True" / "False") |
| labels.ca_max_node_count | Body | String | Applied to the worker node group: Autoscaler: Maximum number of nodes |
| labels.ca_min_node_count | Body | String | Applied to the worker node group: Autoscaler: Minimum number of nodes |
| labels.ca_scale_down_enable | Body | String | Applied to the worker node group: Autoscaler: Whether to enable scale-down ("True" / "False") |
| labels.ca_scale_down_unneeded_time | Body | String | Applied to the worker node group: Autoscaler: Scale down unneeded time |
| labels.ca_scale_down_util_thresh | Body | String | Applied to the worker node group: Autoscaler: Scale down utilization threshold  |
| labels.ca_scale_down_delay_after_add | Body | String | Applied to the worker node group: Auto Scaler: Scale down delay after add |
| labels.kube_tag | Body | String | Kubernetes version of the worker node group |
| labels.user_script | Body | String | User Script (old) |
| labels.user_script_v2 | Body | String | User Script |
| max_node_count | Body | Integer | Maximum Node Count |
| min_node_count | Body | Integer | Minimum Node Count |
| node_addresses | Body | String list | List of node IP addresses |
| node_count | Body | Integer | Node Count |
| project_id | Body | String | Project (tenant) ID |
| role | Body | String | Node group role |
| stack_id | Body | UUID | UUID of the heat stack associated with the node group |
| status | Body | String | Node group status |
| status_reason | Body | String | Reason for the node group status (can be null) |
| created_at | Body | String | Created time (UTC) |
| updated_at | Body | String | Last updated time (UTC) |

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
        "ca_image": "k8s.gcr.io/autoscaling/cluster-autoscaler:v1.19.0",
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
        "login_username": "centos",
        "master_lb_floating_ip_enabled": "true",
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
            "href": "https://kr2-api-kubernetes.infrastructure.cloud.toast.com/v1/clusters/96742ac4-02e7-4b1d-a242-02876c0bd3f8/nodegroups/018b06c5-1293-4081-8242-167a1cb9f262",
            "rel": "self"
        },
        {
            "href": "https://kr2-api-kubernetes.infrastructure.cloud.toast.com/clusters/96742ac4-02e7-4b1d-a242-02876c0bd3f8/nodegroups/018b06c5-1293-4081-8242-167a1cb9f262",
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

---

### Create a Node Group

Creates a node group.

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
| flavor_id | Body | UUID | O |  UUID of the flavor used by the node |
| image_id | Body | UUID | O | UUID of the base image used by the node |
| labels | Body | Object | O | Node group creation information object |
| labels.availability_zone | Body | String | O | Applied to the default worker node group: Availability zone |
| labels.boot_volume_type | Body | String | O | Applied to the default worker node group: Block storage type|
| labels.boot_volume_size | Body | String | O | Applied to the default worker node group: Block storage size (GB) |
| labels.ca_enable | Body | String | O | Applied to the default worker node group: Autoscaler: Whether to enable the feature ("True" / "False") |
| labels.ca_max_node_count | Body | String | X | Applied to the default worker node group: Autoscaler: Maximum number of nodes |
| labels.ca_min_node_count | Body | String | X | Applied to the default worker node group: Autoscaler: Minimum number of nodes |
| labels.ca_scale_down_enable | Body | String | X | Applied to the default worker node group: Autoscaler: Whether to enable scale-down ("True" / "False") |
| labels.ca_scale_down_unneeded_time | Body | String | X | Applied to the default worker node group: Autoscaler: Scale down unneeded time |
| labels.ca_scale_down_util_thresh | Body | String | X | Applied to the default worker node group: Autoscaler: Scale down utilization threshold  |
| labels.ca_scale_down_delay_after_add | Body | String | X | Applied to the default worker node group: Auto Scaler: Scale down delay after add |
| labels.user_script | Body | String | X | User Script (old) |
| labels.user_script_v2 | Body | String | X | User Script |
| name | Body | String | O | Node Group Name |
| node_count | Body | Integer | X | Number of nodes (Default: 1) |


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
| cluster_id | Body  | UUID | UUID of the cluster to which the node group belongs |
| flavor_id | Body | UUID |  UUID of the flavor used by the node |
| image_id | Body | UUID | UUID of the base image used by the node |
| labels | Body | Object | Node group creation information object |
| labels.availability_zone | Body | String | Applied to the default worker node group: Availability zone |
| labels.boot_volume_type | Body | String | Applied to the default worker node group: Block storage type|
| labels.boot_volume_size | Body | String | Applied to the default worker node group: Block storage size (GB) |
| labels.ca_enable | Body | String | Applied to the default worker node group: Autoscaler: Whether to enable the feature ("True" / "False") |
| labels.ca_max_node_count | Body | String | Applied to the default worker node group: Autoscaler: Maximum number of nodes |
| labels.ca_min_node_count | Body | String | Applied to the default worker node group: Autoscaler: Minimum number of nodes |
| labels.ca_scale_down_enable | Body | String | Applied to the default worker node group: Autoscaler: Whether to enable scale-down ("True" / "False") |
| labels.ca_scale_down_unneeded_time | Body | String | Applied to the default worker node group: Autoscaler: Scale down unneeded time |
| labels.ca_scale_down_util_thresh | Body | String | Applied to the default worker node group: Autoscaler: Scale down utilization threshold  |
| labels.ca_scale_down_delay_after_add | Body | String | Applied to the default worker node group: Auto Scaler: Scale down delay after add |
| labels.user_script | Body | String | User Script (old) |
| labels.user_script_v2 | Body | String | User Script |
| max_node_count | Body | Integer | Maximum Node Count |
| min_node_count | Body | Integer | Minimum Node Count |
| name | Body | String | Node Group Name |
| node_count | Body | Integer | Number of nodes (Default: 1) |
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
            "href": "https://kr2-api-kubernetes.infrastructure.cloud.toast.com/v1/clusters/96742ac4-02e7-4b1d-a242-02876c0bd3f8/nodegroups/a3366f2f-a1f3-45ef-8390-10536e8060ff",
            "rel": "self"
        },
        {
            "href": "https://kr2-api-kubernetes.infrastructure.cloud.toast.com/clusters/96742ac4-02e7-4b1d-a242-02876c0bd3f8/nodegroups/a3366f2f-a1f3-45ef-8390-10536e8060ff",
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

---

### Delete a Node Group

Deletes the specified node group.
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

---

### Get Autoscaler Configuration of a Node Group

Retrieves the autoscaler configuration of a node group.

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
| ca_enable | Body | String | Whether to enable the feature ("True" / "False") |
| ca_max_node_count | Body | String | Maximum Node Count |
| ca_min_node_count | Body | String | Minimum Node Count |
| ca_scale_down_enable | Body | String | Whether to enable scale-down ("True" / "False") |
| ca_scale_down_unneeded_time | Body | String | Scale Down Unneeded Time |
| ca_scale_down_util_thresh | Body | String | Scale Down Utilization Threshold  |
| ca_scale_down_delay_after_add | Body | String | Scale Down Delay After Add |

<details><summary>Example</summary>
<p>

```json
{
    "ca_enable": true,
    "ca_image": "k8s.gcr.io/autoscaling/cluster-autoscaler:v1.19.0",
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

---

### Change Autoscaler Configuration of a Node Group

Changes the autoscaler configuration of a node group.

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
| ca_max_node_count | Body | String |X| Maximum Node Count |
| ca_min_node_count | Body | String |X| Minimum Node Count |
| ca_scale_down_enable | Body | String |X| Whether to enable scale-down ("True" / "False") |
| ca_scale_down_unneeded_time | Body | String |X| Scale Down Unneeded Time |
| ca_scale_down_util_thresh | Body | String | X |Scale Down Utilization Threshold  |
| ca_scale_down_delay_after_add | Body | String | X |Scale Down Delay After Add |

<details><summary>Example</summary>
<p>

```json
{
    "ca_enable": true,
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

---

### Upgrade a Cluster

Upgrades a cluster.

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
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | Node group UUID or node group name<br>Set to **default-master** when upgrading the master components | 
| version | Body | String | O | Kubernetes Version |
| num_buffer_nodes | Body | Integer | X | Number of buffer nodes. Minimum value: 0, Maximum value: (Maximum number of nodes per the worker node group - the current number of nodes for the worker node group), Default value: 1 |
| num_max_unavailable_nodes | Body |  Integer | X | Maximum number of unavailable nodes. Minimum value: 1, Maximum value: The current number of nodes for the worker node group, Default value: 1 |

To upgrade a cluster, you must upgrade the master components and then upgrade the worker components. Master and worker component upgrades are performed on a per node group basis.

* Upgrading master components
    * Set the node group name to **default-master**.

* Upgrading worker components
    * Set the name of node group to upgrade.


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

---

### Change User Script

Changes the user script of the node group.

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
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | Node group UUID or node group name<br>Set to **default-master** when upgrading master components | 
| contents | Body | String | O | User script content |


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
| uuid | Body | UUID | Node Group UUID |

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


## Other Features

### See the Supported Kubernetes Versions

Retrieves the Kubernetes versions supported by NHN Kubernetes Service (NKS).

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
| supported_k8s | Body | Object | Supported Kubernetes version object |
| supported_k8s."version name" | Body | String | Whether the Kubernetes version is valid or not (True/False) |

<details><summary>Example</summary>
<p>

```json
{
    "supported_k8s": {
        "v1.17.6": false,
        "v1.18.19": false,
        "v1.19.13": false,
        "v1.20.12": true,
        "v1.21.6": true,
        "v1.22.3": true,
        "v1.23.3": true
    }
}
```

</p>
</details>

