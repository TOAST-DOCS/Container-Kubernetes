## Container > NHN Kubernetes Service (NKS) > API v2 Guide

This guide describes the API for configuring Kubernetes clusters.
To use the API, you need an API endpoint, token, etc. Refer to [API Preparations](/Compute/Compute/en/identity-api/) to prepare the necessary information to use the API.

All API calls are made using the `kubernetes` type endpoint.

| Type | Region | Endpoint |
|---|---|---|
| kubernetes | Korea (Pangyo) Region <br> Korea (Pyeongchon) Region | https://kr1-api-kubernetes-infrastructure.nhncloudservice.com <br>https://kr2-api-kubernetes-infrastructure.nhncloudservice.com |


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

Enter the UUID of subnet associated with the VPC network attached to the internet gateway. If multiple subnets are found, enter them by concatenating them with a colon (`:`). For more information about the subnet list query API, refer to [List Subnets](/Network/VPC/en/public-api/#list-vpc-subnets).


### VPC Network UUID

Enter the UUID of internal VPC network to associate with the node. For more information about the network list query API, refer to [List Networks](/Network/VPC/en/public-api/#view-vpc-list).

### VPC Subnet UUID

Enter the UUID of subnet associated with the internal VPC network to associate with the node. For more information about the subnet list query API, refer to [List Subnets](/Network/VPC/en/public-api/#list-vpc-subnets).

### Availability Zone UUID

Enter the UUID of availability zone in which to create the node. For more information about the availability zone list query API, see [List Availability Zones](/Compute/Instance/en/public-api/#list-availability-zones).

### Key Pair UUID

Enter the key pair to use when connecting to the node. For more information about the key pair list query API, refer to [List Key Pairs](/Compute/Instance/en/public-api/#list-key-pairs).

### Base Image UUID

Enter the base image UUID to use for node creation. To filter only the base images used to create NKS nodes, enter the value `nhncloud_product=container&visibility=public` in the query string parameter when calling the API. For more information on the retrieve base image list API, see [Retrieve Image list](/Compute/Image/en/public-api/#_2).

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
| clusters.stack_id | Body | UUID | UUID of the heat stack associated with the control plane |
| clusters.status | Body | String | Cluster status |
| clusters.labels | Body | Object | Cluster label |
| clusters.labels.kube_tag | Body |String | Kubernetes version of the control plane |
| clusters.labels.availability_zone | Body | String | Applied to the default worker node group: Availability zone |
| clusters.labels.node_image | Body | UUID | Applied to the default worker node group: Base image UUID |
| clusters.labels.external_network_id | Body | String | UUID of VPC network attached to the internet gateway |
| clusters.labels.external_subnet_id_list | Body | String | List of UUIDs of subnets attached to the internet gateway (separated by colons)|
| clusters.labels.cert_manager_api | Body | String | Whether to enable the certificate signing request (CSR) feature. Must be set to "True" |
| clusters.labels.master_lb_floating_ip_enabled | Body | String | Whether to create a public domain address for Kubernetes API endpoint ("True" / "False") |
| clusters.labels.strict_sg_rules | Body | String |Create only required security rules in worker node security groups ("True" / "False"), (available for clusters created on or after February 27, 2024) |
| clusters.labels.additional_network_id_list | Body | String | Applied to the default worker node group: List of VPC network UUIDs for additional networks (separated by colons) |
| clusters.labels.additional_subnet_id_list | Body | String | Applied to the default worker node group: List of VPC subnet UUIDs for additional networks (separated by colons) |
| clusters.labels.cni_driver | Body | String | Cluster CNI (Available for clusters created on or after 2023.03.31) |
| clusters.labels.service_cluster_ip_range | Body | String | IP range assigned to ClusterIP when creating a service from K8s service network and clusters (Available for clusters created on or after 2023.05.30) |
| clusters.labels.pods_network_cidr | Body | String | Cluster pod network (Available for clusters created on or after 2023.05.30) |
| clusters.labels.pods_network_subnet | Body | String | Cluster pod subnet size (Available for clusters created on or after 2023.05.30) |
| clusters.labels.ncr_sgw | Body | String | Service gateway UUID of NCR type |
| clusters.labels.obs_sgw | Body | String | Service gateway UUID of OBS type |
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
                    "href": "https://kr2-api-kubernetes-infrastructure.nhncloudservice.com/v1/clusters/f0af4484-0a16-433a-a15c-295d9ba6537d",
                    "rel": "self"
                },
                {
                    "href": "https://kr2-api-kubernetes-infrastructure.nhncloudservice.com/clusters/f0af4484-0a16-433a-a15c-295d9ba6537d",
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
| stack_id | Body | UUID | UUID of the heat stack associated with the control plane |
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
| labels.kube_tag | Body |String | Kubernetes version of the control plane |
| labels.availability_zone | Body | String | Applied to the default worker node group: Availability zone |
| labels.node_image | Body | UUID | Applied to the default worker node group: Base image UUID |
| labels.external_network_id | Body | String | UUID of the VPC network attached to the internet gateway |
| labels.external_subnet_id_list | Body | String | List of UUIDs of subnets attached to the internet gateway (separated by colons) |
| labels.cert_manager_api | Body | String | Whether to enable the certificate signing request (CSR) feature. Must be set to "True" |
| labels.master_lb_floating_ip_enabled | Body | String | Whether to create a public domain address for Kubernetes API endpoint ("True" / "False") |
| labels.strict_sg_rules | Body | String | Create only required security rules in worker node security groups ("True" / "False"), (available for clusters created on or after February 27, 2024) |
| labels.additional_network_id_list | Body | String | Applied to the default worker node group: List of VPC network UUIDs for additional networks (separated by colons) |
| labels.additional_subnet_id_list | Body | String | Applied to the default worker node group: List of VPC network UUIDs for additional networks (separated by colons) |
| labels.cni_driver  | Body | String | Cluster CNI (Available for clusters created on or after 2023.03.31) |
| labels.service_cluster_ip_range | Body | String | Cluster CIDR, IP range allocated to ClusterIP when creating the service in the cluster and K8s service networks (Available for clusters created on or after 2023.05.30) |
| labels.pods_network_cidr | Body | String | Cluster pod network (Available for clusters created on or after 2023.05.30) |
| labels.pods_network_subnet | Body | String | Cluster pod subnet size (Available for clusters created on or after 2023.05.30) |
| labels.ncr_sgw | Body | String | Service Gateway UUID of NCR type |
| labels.obs_sgw | Body | String | Service gateway UUID of OBS type |
| labels.term_of_validity | Body | String | Certificate validity period |
| labels.certificate_expiry | Body | String | Certificate expiration date | 

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
            "href": "https://kr2-api-kubernetes-infrastructure.nhncloudservice.com/v1/clusters/2b778d83-8b67-45b1-920e-b0c5ad5c2f30",
            "rel": "self"
        },
        {
            "href": "https://kr2-api-kubernetes-infrastructure.nhncloudservice.com/clusters/2b778d83-8b67-45b1-920e-b0c5ad5c2f30",
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

### View Task History List

Views a list of the cluster's job history.

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
| events | Body | Array | Task history object list |
| events.id | Body | Integer | Task ID |
| events.uuid | Body | UUID | Task UUID |
| events.project_id | Body | String | Project (tenant) ID |
| events.cluster_uuid | Body | String | Cluster UUID |
| events.cluster_name | Body | String | Cluster name |
| events.resource_uuid | Body | String | Target UUID for the task |
| events.resource_name | Body | String | Target name |
| events.resource_type | Body | String | Target type ("cluster" / "nodegroup") |
| events.type | Body | String | Task type |
| events.state | Body | String | Task status ("SUCCESS" / "FAIL" / "IN_PROGRESS") |
| events.contents | Body | String | Task progress (null when successful) |
| events.created_at | Body | String | Task start time (UTC) |
| events.updated_at | Body | String | Task end time (UTC) |


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

---

### List Task History

Lists the job history of a cluster.

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
| resource_uuid | Body | UUID | Target UUID for the task |
| resource.name | Body | String | Target name |
| resource_type | Body | String | Target type ("cluster" / "nodegroup") |
| type | Body | String | Task type |
| state | Body | String | Task status ("SUCCESS" / "FAIL" / "IN_PROGRESS") |
| contents | Body | String | Task progress (null when successful) |
| created_at | Body | String | Task start time (UTC) |
| updated_at | Body | String | Task end time (UTC) |


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
| labels.boot_volume_key_id | Body | String | X | (Symmetric key ID to apply to encrypted block storage (if using encrypted block storage) |
| labels.boot_volume_appkey | Body | String | X | The appkey for the symmetric key to apply to encrypted block storage (if using encrypted block storage) |
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
| labels.user_script | Body | String | X | User script (old) |
| labels.user_script_v2 | Body | String | X | User script |
| labels.master_lb_floating_ip_enabled | Body | String | O |  Whether to create a public domain address for Kubernetes API endpoint ("True" / "False") |
| labels.strict_sg_rules | Body | String | X | Create only required security rules in the worker node security group ("True" / "False"), default: "False" |
| labels.additional_network_id_list | Body | String | X |  Applied to the default worker node group: List of VPC network UUIDs for additional networks (separated by colons) |
| labels.additional_subnet_id_list | Body | String | X |  Applied to the default worker node group: List of VPC network UUIDs for additional networks (separated by colons) |
| labels.service_cluster_ip_range | Body | String  | X |  K8s service network, the IP band assigned to the ClusterIP when creating a service in the cluster. See fixed_subnet, pods_network_cidr, and service_cluster_ip_range input rules. |
| labels.pods_network_cidr | Body | String |  X |  Cluster Pod Network. See fixed_subnet, pods_network_cidr, service_cluster_ip_range input rules |
| labels.pods_network_subnet | Body | Integer | X |  Cluster Pod subnet size. See pods_network_subnet input rules |
| labels.ncr_sgw | Body | String | X | Service gateway UUID of NCR type<br>But, only created in the same VPC as the cluster VPC. |
| labels.obs_sgw | Body | String | X | Service gateway UUID of OBS type<br>But, only created in the same VPC as the cluster VPC. |
| labels.cni_driver | Body | String | X | Set up CNI, Selectable CNI list: calico (default),calico-ebpf<br>calico: Created as Calico-VXLAN<br>calico-ebpf: Created as Calico-eBPF |
| labels.extra_security_groups | UUID | Array | Cluster Pod subnet size. See pods_network_subnet input rules | List of additional security group objects |
| labels.extra_security_groups[].target_subnet | UUID | X | Cluster Pod subnet size. See pods_network_subnet input rules | The UUID of a subnet to be specified by additional security groups |
| labels.extra_security_groups[].security_group_ids | Body | String | X | List of additional security group UUIDs (comma-separated) |
| labels.extra_volumes | Body | Array | X | List of additional block storage objects |
| labels.extra_volumes[].volume_type | Body | String | X | Additional block storage types |
| labels.extra_volumes[].volume_size | Body | X | X | Additional block storage size (GB) |
| labels.extra_volumes[].volume_key_id | Body | String | X | (Symmetric key ID to apply to encrypted block storage (if using encrypted block storage) |
| labels.extra_volumes[].volume_appkey | Body | String | X | The appkey for the symmetric key to apply to encrypted block storage (if using encrypted block storage) |
| labels.extra_volumes[].volume_mount_path | Body | String | X | Path where additional block storage will be mounted |
| flavor_id | Body | UUID | O | Applied to the default worker node group: Node instance flavor UUID |
| fixed_network | Body | UUID | O | VPC Network UUID |
| fixed_subnet | Body | UUID | O | VPC subnet UUID. Note the rules for entering fixed_subnet, pods_network_cidr, and service_cluster_ip_range. |

> [Caution]
> The CIDRs for fixed_subnet, pods_network_cidr, and service_cluster_ip_range must be entered in the following conventions
>  - CIDR cannot overlap with the link-local address band (169.254.0.0/16).
>  - The fixed_subnet, pods_network_cidr, and service_cluster_ip_range bands cannot overlap.
>  - CIDR cannot overlap with the IP band (198.18.0.0/19) being used inside the NKS.
>  - You cannot enter a CIDR block greater than /24. (The following CIDR blocks are not available: /26, /30).
>  - For clusters of v1.23.3 or earlier, they cannot overlap with BIP (bridged IP range) (172.17.0.0/16).
> pod_network_subnet must be entered in the following rules.
>  - Values between 20 and 28 (included) are allowed.
> The pods_network_subnet value must be at least 2 greater than the pods_network_cidr prefix value. Normal example (Pod subnet size: 24, Pod network: 10.100.0.0/22)


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

### Change Cluster CNI
Changes the cluster CNI (container network interface). You can change Flannel CNI to a different CNI. For more information on the types of CNIs you can change and the conditions under which they can be changed, see [User Guide](/Container/NKS/en/user-guide/#cni).

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/actions/cni_update
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
| cni | Body | String | O | Configure a CNI to change (Selectable CNI list: calico)<br>calico: Changed to Calico-VXLAN | 
| num_buffer_nodes | Body | Integer | X | Number of buffer nodes. Default: 1, minimum: 0, maximum: number of additional nodes that can be created in each worker node group (maximum number of nodes per worker node group - current number of nodes in that worker node group) |
| num_max_unavailable_nodes | Body |  Integer | X | Maximum number of unavailable nodes. minimum: 1, maximum: current number of nodes for the cluster, default: 1 |
| pod_cidr | Body | String | O | calico pod cidr settings, see the input rules of pod_cidr |
| pod_subnet | Body | String | O | calico pod cidr subnet settings, Default: 24, see the input rules of pod_subnet |

pod_cidr must be entered in the following rules.
* CIDR cannot overlap with the link-local address band (169.254.0.0/16).
* CIDR cannot overlap with the service IP band (K8s service network) used in NKS clusters. 
* CIDR cannot overlap with the IP band (198.18.0.0/19) being used inside the NKS.
* CIDR cannot overlap with bands of the VPC network subnet or additional network subnets connected to NKS clusters.
* CIDR cannot overlap with a pod network band that is currently being used for NKS clusters.
* You cannot enter a CIDR block greater than /24. (The following CIDR blocks are not available: /26, /30)

pod_subnet must be entered in the following rules.
* Values between 20 and 28 (included) are allowed.
* The value of pod_subnet must be at least 2 greater than the prefix value of pod_cidr. Normal example (pod subnet size: 24, pod network: 10.100.0.0/22)



<details><summary>Example</summary>
<p>

```json
{
    "cni": "calico",
    "num_max_unavailable_nodes": 1,
    "num_buffer_nodes": 1,
    "pod_cidr": "10.200.0.0/16"
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

### Enforce IP Access Control to Cluster API Endpoints
You can enforce or disable IP access control to cluster API endpoints.
For more information about IP access control features, see [IP access control](/Network/Load%20Balancer/en/overview/#ip).
For more information about IP access control rules for Cluster API endpoints, see the [user guide](/Container/NKS/en/user-guide/#_67).

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
| enable | Body | String | O | Can be set to either 'true' or 'false'. Default: 'false'<br>Enforce IP Access Control to Cluster API Endpoints<br>false: Disable IP access control for cluster API endpoints, all sub-settings are ignored when set to false |
| Endpoint stage ID | Body | String | O (if the enable setting is true) | IP access control type, can be set to either ALLOW or DENY |
| ipacl_targets | Body | List of Object | O (if the enable setting is true) | IP access control target object |
| ipacl_targets.cidr_address | Body | String | O (if the enable setting is true) | IP access control target. You can enter an IP address or a range of IP addresses in CIDR format. |
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


### Get Cluster API Endpoint IP Access Control 
You can view IP access control information applied to your cluster API endpoints.

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
| enable | Body | String | true: IP access control is applied to the cluster API endpoint, false: IP access control is off for the cluster API endpoint | 
| Endpoint stage ID | Body | String | IP access control type ALLOW, DENY can be checked |
| ipacl_targets | Body | List of Object | IP access control target object |
| ipacl_targets.cidr_address | Body | String | IP access control target. You can enter an IP address or a range of IP addresses in CIDR format. |
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


---
### Renew Cluster Certificate

Renews the certificate for the cluster.
```
PATCH /v1/certificates/{CLUSTER_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### Name
| Format | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | Cluster UUID or cluster name | 
| term_of_validity | Body | Integer | O | Validity period of the certificate. Can be entered in years. Minimum: 1, maximum: 5 |

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
| UUID | Body | String | Target cluster UUID|

<details><summary>Example</summary>
<p>

```json
{
    "uuid": "5f6af7da-df9b-4edd-8284-02317b11e061"
}

```

</p>
</details>

---

### Change Service Gateway

If you set a service gateway when you created the cluster, you can change it to a different service gateway. 
```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/actions/update_sgw
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
| ncr_sgw | Body | UUID | O | Service Gateway UUID of the NCR type you want to change |
| obs_sgw | Body | UUID | O | Service Gateway UUID of the OBS type you want to change |

<details><summary>Example of changing the service gateway</summary>
<p>

```json
{
    "ncr_sgw": "48f4ff38-d14b-4f34-a40c-705524e6a755",
    "obs_sgw": "23c550cb-6a5b-4eaa-903a-711c13316d91"
}
```

</p>
</details>

#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| UUID | Body | String | Target cluster UUID|

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
| labels.boot_volume_key_id | Body | String | ID of symmetric key applied to block storage (when using encrypted block storage) |
| labels.boot_volume_appkey | Body | String | Appkey of symmetric key applied to block storage (when using encrypted block storage) |
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
| labels.additional_network_id_list | Body | String | Applied to the default worker node group: List of VPC network UUIDs for additional networks (separated by colons) |
| labels.additional_subnet_id_list | Body | String | Applied to the default worker node group: List of VPC subnet UUIDs for additional networks (separated by colons) |
| labels.strict_sg_rules | Body | String | Create only required security rules in worker node security groups ("True" / "False"), (available for clusters created on or after February 27, 2024) |
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
            "href": "https://kr2-api-kubernetes-infrastructure.nhncloudservice.com/v1/clusters/96742ac4-02e7-4b1d-a242-02876c0bd3f8/nodegroups/018b06c5-1293-4081-8242-167a1cb9f262",
            "rel": "self"
        },
        {
            "href": "https://kr2-api-kubernetes-infrastructure.nhncloudservice.com/clusters/96742ac4-02e7-4b1d-a242-02876c0bd3f8/nodegroups/018b06c5-1293-4081-8242-167a1cb9f262",
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
| labels.ca_scale_down_util_thresh | Body | String | X | Applied to the default worker node group: Autoscaler: Scale down utilization threshold  |
| labels.ca_scale_down_delay_after_add | Body | String | X | Applied to the default worker node group: Auto Scaler: Scale down delay after add |
| labels.user_script | Body | String | X | User Script (old) |
| labels.user_script_v2 | Body | String | X | User Script |
| labels.additional_network_id_list | Body | String | X | Applied to the default worker node group: List of VPC network UUIDs for additional networks (separated by colons) |
| labels.additional_subnet_id_list | Body | String | X | Applied to the default worker node group: List of VPC subnet UUIDs for additional networks (separated by colons)) |
| labels.extra_security_groups | Body | nodegroups.uuid | X | List of additional security group objects |
| labels.extra_security_groups[].target_subnet | Body | String | X | The UUID of a subnet to be specified by additional security groups |
| labels.extra_security_groups[].security_group_ids | Body | String | X | List of additional security group UUIDs (comma-separated) |
| labels.extra_volumes | Body | nodegroups.uuid | X | List of additional block storage objects |
| labels.extra_volumes[].volume_type | Body | String | X | Additional block storage type |
| labels.extra_volumes[].volume_size | Body | project_id | X | Additional block storage size (GB) |
| labels.extra_volumes[].volume_key_id | Body | String | X | (Symmetric key ID to apply to encrypted block storage (if using encrypted block storage) |
| labels.extra_volumes[].volume_appkey | Body | String | X | The appkey for the symmetric key to apply to encrypted block storage (if using encrypted block storage) |
| labels.extra_volumes[].volume_mount_path | Body | String | X | Path where additional block storage will be mounted |
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
| labels.additional_network_id_list | Body | String | Applied to the default worker node group: List of VPC network UUIDs for additional networks (separated by colons) |
| labels.additional_subnet_id_list | Body | String | Applied to the default worker node group: List of VPC subnet UUIDs for additional networks (separated by colons) |
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
            "href": "https://kr2-api-kubernetes-infrastructure.nhncloudservice.com/v1/clusters/96742ac4-02e7-4b1d-a242-02876c0bd3f8/nodegroups/a3366f2f-a1f3-45ef-8390-10536e8060ff",
            "rel": "self"
        },
        {
            "href": "https://kr2-api-kubernetes-infrastructure.nhncloudservice.com/clusters/96742ac4-02e7-4b1d-a242-02876c0bd3f8/nodegroups/a3366f2f-a1f3-45ef-8390-10536e8060ff",
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

### Stop a Node Group

Stops the specified node group.

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
| node_list | Body | String | O | List of UUIDs of node instances separated by colon (`:`) |

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
| uuid | Body | UUID | Node Group UUID |

<details><summary>Example</summary>
<p>

```json
{
    "uuid": "439aa682-398f-4061-a4d1-116da6b1154e"
}
```

</p>
</details>

---

### Start a Node

Start the specified node list.

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
| node_list | Body | String | O | List of UUIDs of node instances separated by colon (`:`) |

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
| uuid | Body | UUID | Node Group UUID |

<details><summary>Example</summary>
<p>

```json
{
    "uuid": "439aa682-398f-4061-a4d1-116da6b1154e"
}
```

</p>
</details>

---

### View Autoscaler Configuration of a Node Group

Views the autoscaler configuration of a node group.

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
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | Node group UUID or node group name<br>Set to **default-master** when upgrading the control plane | 
| version | Body | String | O | Kubernetes Version |
| num_buffer_nodes | Body | Integer | X | Number of buffer nodes. Minimum value: 0, Maximum value: (Maximum number of nodes per the worker node group - the current number of nodes for the worker node group), Default value: 1 |
| num_max_unavailable_nodes | Body |  Integer | X | Maximum number of unavailable nodes. Minimum value: 1, Maximum value: The current number of nodes for the worker node group, Default value: 1 |

To upgrade a cluster, you must upgrade the control plane and then upgrade the worker components. Control plane and worker component upgrades are performed on a per node group basis.

* Upgrading control plane components
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
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | Node group UUID or node group name<br>Set to **default-master** when upgrading control plane components | 
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

### Change Instance Flavor

You can change the instance flavor of a node group.

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
| flavor_id | Body | String | O | Flavor UUID |
| num_buffer_nodes | Body | Integer | X | Number of buffer nodes. Minimum: 0, Maximum: (maximum number of nodes per worker node group - current number of nodes in that worker node group), Default: 1 |
| num_max_unavailable_nodes | Body |  Integer | X | Maximum number of unavailable nodes. Minimum: 1, Maximum: Current number of nodes in the worker node group, Default: 1) |


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
| supported_event_type."task type"| Body | Object | Supported task type objects (cluster_events/nodegroup_events) |
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
        "v1.27.3": true
        "v1.28.3": true,
        "v1.29.3": true,
        "v1.30.3": true       
    },
    "supported_event_type": {
        "cluster_events": {
            "CLUSTER_CREATE": "Create a cluster",
            "CLUSTER_DELETE": "Delete a cluster",
            "CLUSTER_HANDOVER": "Cluster OWNER change",
            "CLUSTER_CNI_UPDATE": "Change CNI"
        },
        "nodegroup_events": {
            "NODEGROUP_CREATE": "Node group creation",
            "NODEGROUP_DELETE": "Delete a node group",
            "CLUSTER_RESIZE": "Resize the cluster",
            "NODEGROUP_UPDATE_FLAVOR": "Change instance type",
            "NODEGROUP_UPGRADE": "Upgrade a node group",
            "NODEGROUP_USERSCRIPT_UPDATE": "Change userscript",
            "NODEGROUP_SET_CLUSTER_AUTOSCALER": "Change Auto Scaler settings",
            "NODEGROUP_NODE_ACTION_NODE_START": "Start worker node",
            "NODEGROUP_NODE_ACTION_NODE_STOP": "Stop worker node"
        }
    }
}
```

</p>
</details>

