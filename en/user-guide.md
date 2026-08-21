<!-- machine_translated: true -->

<!-- pre-align:aligned sig=98b43adf5be7 -->

<a id="container-nhn-kubernetes-service-nks-user-guide"></a>
## Container > NHN Kubernetes Service(NKS) > User Guide { #container-nhn-kubernetes-service-nks-user-guide }

<a id="cluster-headings"></a>
## Cluster { #cluster-headings }
A cluster is a group of instances that make up the user's Kubernetes.

<a id="cluster-create"></a>
### Create a Cluster { #cluster-create }
To use NHN Kubernetes Service (NKS), you must first create a cluster.

> [Caution] Permission settings required to use clusters<br>
> To create a cluster, the user must have **Infrastructure ADMIN**, **Infrastructure LoadBalancer ADMIN**, or **Infrastructure NKS ADMIN** permissions of basic infrastructure services for the project.
> Only with the permissions, the user can normally create and operate clusters running on basic infrastructure services. It is totally possible to add one of the two permissions when the other is already acquired.
> To learn more about setting up permissions, see [Manage Project Members](/nhncloud/en/console-user-guide/#_3).

On the **Container > NHN Kubernetes Service(NKS)** page, click **Create Cluster** to open the cluster creation page. The following items are required to create a cluster:

| Item | Description |
| --- | --- |
| Cluster name | Name of a Kubernetes cluster. It is limited to 20 characters, and only lowercase English letters, numbers, and '-' can be entered. It must start with a lowercase letter and end with a lowercase letter or number. RFC 4122-compliant UUID formats cannot be used. |
| Kubernetes version | Kubernetes version to use |
| VPC | VPC network to connect to the cluster |
| Subnet | Subnet to connect to the instances that make up the cluster, selected from the subnets defined in the VPC |
| NCR service gateway | Service gateway of the NCR type<br>(Only when an internet gateway is not connected to the subnet) |
| OBS service gateway | Service gateway of the OBS type<br>(Only when an internet gateway is not connected to the subnet) |
| K8s service network | Service object CIDR configuration for the cluster |
| Pod network | Pod network configuration for the cluster |
| Pod subnet size | Pod subnet size configuration for the cluster |
| Kubernetes API endpoint | Public: Assigns a domain address to the endpoint and connects a floating IP<br>Private: Sets the endpoint to an internal network address |
| Enhanced security rules | When creating worker node security groups, only essential security rules are created. See the cluster worker node essential security rules.<br>True: Create only essential security rules<br>False: Create essential security rules and security rules that allow all ports |
| Image | Image to use for the instances that make up the cluster |
| Availability zone | Zone in which to create the default node group instances |
| Instance type | Specifications of the default node group instances |
| Number of nodes | Number of default node group instances |
| Key pair | Key pair to use for accessing the default node group |
| Block storage type | Block storage type for the default node group instances |
| Block storage size | Block storage size for the default node group instances |
| Additional network | Additional network/subnet to create in the default worker node group |

Enter information as required and click **Create Cluster**, and a cluster begins to be created. You can check the status in the cluster list. It takes about 10 minutes to create; more time may be required depending on the cluster configuration.

> [Caution]
> The CIDRs for the VPC network subnet, K8s service network, and pod network must be configured so that they do not fall under the following constraints:
>  - They cannot overlap with the link-local address range (169.254.0.0/16).
>  - The VPC network subnet, additional network subnet, pod network, and K8s service network ranges cannot overlap with each other.
>  - They cannot overlap with the IP range used internally by NKS (198.18.0.0/19).
>  - CIDR blocks larger than /24 cannot be entered (the following CIDR blocks cannot be used: /26, /30).
>  - For clusters at v1.23.3 or earlier, they cannot overlap with the Docker BIP (bridged IP range) (172.17.0.0/16).
>
> The service gateway configured when the cluster was created must not be deleted.
>  - If the selected subnet is not connected to an internet gateway, the NCR service gateway and OBS service gateway must be configured.
>  - These two service gateways are used to retrieve images and binaries required for NKS cluster configuration and basic functionality.
>  - If the service gateway configured when the cluster was created is deleted, the cluster will not work properly.
>  - If the service gateway configured when the cluster was created has been deleted, you must reconfigure the service gateway for each cluster.
>  - The cluster's service gateway can be reconfigured by clicking the **Change** button next to the service gateway query result on the cluster's **Basic Information** tab.
>
> The internet gateway connection status of the subnet configured when the cluster was created must not be changed.
>  - The registry from which images and binaries are retrieved differs depending on whether the subnet configured when the cluster was created is connected to an internet gateway.
>  - If the internet gateway connection status of the subnet changes after the cluster is created, the cluster will not work properly because it cannot connect to the configured registry.

> [Maximum number of nodes that can be created]
> The maximum number of nodes that can be created when creating a cluster is determined by the pod network and pod subnet size settings.
> Calculation: 2 ^ (pod subnet size - host bits of pod network) - 3
> Example:
>  - Pod subnet size = 24
>  - Pod network = 10.100.0.0/16
>  - Calculation: 2 ^ (24 - 16) - 3 = up to 253 nodes can be created

> [Maximum number of IPs assignable to pods per node]
> The maximum number of IPs available on a single node is determined by the pod subnet size setting.
> Calculation: 2 ^ (32 - pods_network_subnet) - 2
> Example:
>  - Pod subnet size = 24
>  - Calculation: 2 ^ (32 - 24) - 2 = up to 254 IPs available

> [Maximum number of IPs assignable to pods in the cluster]
> Calculation: Maximum number of IPs assignable to pods per node * Maximum number of nodes that can be created
> Example:
>  - Pod subnet size = 24
>  - Pod network = 10.100.0.0/16
>  - Calculation: 254 (maximum IPs assignable to pods per node) * 253 (maximum nodes that can be created) = up to 64,262 IPs available

<a id="cluster-show"></a>
### Query Clusters { #cluster-show }
You can check the clusters you have created on the **Container > NHN Kubernetes Service(NKS)** page. The cluster list shows brief information about each cluster.

| Item | Description |
| --- | --- |
| Cluster name | Name of the cluster |
| Number of nodes | Total number of worker nodes in the cluster |
| Kubernetes version | Kubernetes version information |
| kubeconfig file | Button to download the kubeconfig file for controlling the cluster |
| Task status | Task status for commands issued to the cluster |
| k8s API status | Operating status of the Kubernetes API endpoint |
| k8s Node status | Status of Kubernetes Node resources |

The meaning of each icon in the task status is as follows:

| Icon | Meaning |
| --- | --- |
| Green solid icon | Normal end of task |
| Circular rotating icon | Task in progress |
| Red solid icon | Task failed |
| Gray solid icon | Cluster unavailable |

The meaning of each icon in the k8s API status is as follows:

| Icon | Meaning |
| --- | --- |
| Green solid icon | Working properly |
| Yellow solid icon | Information is not accurate because it nears expiration (5 minutes) |
| Red solid icon | Kubernetes API endpoint is not working properly or information expired |

The meaning of each icon in k8s Node status is as follows.

| Icon | Meaning |
| --- | --- |
| Green solid icon | All nodes in the cluster are in the Ready state |
| Yellow solid icon | Kubernetes API endpoints are not working properly or there are nodes in NotReady in the cluster |
| Red solid icon | All nodes in cluster are in NotReady |

When you select a cluster, the cluster information appears at the bottom.

| Item | Description |
| --- | --- |
| Cluster name | Name and ID of the Kubernetes cluster |
| Number of nodes | Total number of node instances that make up the cluster |
| Kubernetes version | Kubernetes version in use |
| Kubernetes certificate | Validity period and expiration date of the cluster certificate |
| CNI | Type of Kubernetes CNI in use |
| K8s service network | Service object CIDR configuration for the cluster |
| Pod network | Kubernetes pod network configuration in use |
| Pod subnet size | Kubernetes pod subnet size configuration in use |
| VPC | VPC network connected to the cluster |
| Subnet | Subnet connected to the node instances that make up the cluster |
| API endpoint | API endpoint URI for accessing and managing the cluster |
| Configuration file | Button to download the configuration file required for accessing and managing the cluster |

<a id="cluster-delete"></a>
### Delete Clusters { #cluster-delete }
Select the cluster to delete and click **Delete Cluster** to proceed with deletion. It takes about 5 minutes to delete; more time may be required depending on the cluster status.

<a id="change-keypair"></a>
### Change Cluster Key Pair { #change-keypair }

Changes the key pair for all worker nodes in the cluster. Select one of the key pairs belonging to the logged-in user. When the key pair is changed, the following takes effect:

* The selected key pair is set on all worker node VMs.
* You can access all worker node VMs via SSH by using the configured key pair.
* The key pair for each worker node instance is displayed as `managed-by-nks`.

A cluster with a key pair configured operates under the permissions of the service user. The service user is an internal user managed at the NKS service level, and NKS feature operations and service integrations run under the service user's permissions. A cluster operating under the service user's permissions does not require managing or changing the owner.

> [Caution]
> * A cluster with a regular user set as the owner can be changed to operate under the service user's permissions by using the key pair change feature.
> * The cluster owner change feature is no longer provided. To allow a cluster to operate under the service user's permissions, use the key pair change feature.

<a id="nodegroup-headings"></a>
## Node Group { #nodegroup-headings }
A node group is a group of worker node instances that make up Kubernetes.

<a id="nodegroup-show"></a>
### Query Node Groups { #nodegroup-show }
Click the cluster name in the cluster list to view the list of node groups. The node group list shows brief information about each node group.

| Item | Description |
| --- | --- |
| Node group name | Name of the node group |
| Number of nodes | Number of nodes in the node group |
| Kubernetes version | Kubernetes version applied to the node group |
| Availability zone | Availability zone information applied to the node group |
| Instance type | Instance type of the node group |
| Image type | Image type of the node group |
| Task status | Task status for commands issued to the node group |
| k8s Node status | Status of Kubernetes Node resources in the node group |

The meaning of each icon in the task status is as follows:

| Icon | Meaning |
| --- | --- |
| Green solid icon | Normal end of task |
| Circular rotating icon | Task in progress |
| Red solid icon | Task failed |
| Orange solid icon | Some nodes completed successfully |
| Gray solid icon | Cluster and node group unavailable |

The meaning of each icon of k8s Node status is as follows.

| Icon | Meaning |
| --- | --- |
| Green solid icon | All nodes in the node group are in the Ready state |
| Yellow solid icon | The Kubernetes API endpoint is not working properly or there are nodes in the NotReady status in the node group |
| Red solid icon | All nodes in the node group are in the NotReady status |

When you select a node group, the node group information appears at the bottom.

You can view the following information on the **Basic Information** tab:

| Item | Description |
| --- | --- |
| Node group name | Name and ID of the node group |
| Cluster name | Name and ID of the cluster to which the node group belongs |
| Kubernetes version | Kubernetes version in use |
| Availability zone | Zone in which the node group instances were created |
| Instance type | Specifications of the node group instances |
| Image type | Type of image used for the node group instances |
| Block storage size | Block storage size of the node group instances |
| Created on | Time when the node group was created |
| Modified on | Time when the node group was last modified |

* Node list
On the **Node List** tab, you can view the list of instances that make up the node group.

<a id="nodegroup-create"></a>
### Create Node Groups { #nodegroup-create }
Along with a new cluster, a default node group is created, but more node groups can be created depending on the needs. If higher specifications are required to run a container, or more worker node instances are required to scale out, node groups may be additionally created. Click **Create Node Groups** from the page of node group list, and the page of creating a node group shows up. The following items are required to create a node group:

| Item | Description |
| --- | --- |
| Availability zone | Zone in which to create the instances that make up the cluster |
| Node group name | Name of an additional node group. It is limited to 20 characters, and only lowercase English letters, numbers, and '-' can be entered. It must start with a lowercase letter and end with a lowercase letter or number. RFC 4122 compliant UUID formats cannot be used. |
| Instance type | Specifications of the additional node group instances |
| Number of nodes | Number of additional node group instances |
| Key pair | Key pair to use for accessing the additional node group |
| Block storage type | Block storage type for the additional node group instances |
| Block storage size | Block storage size for the additional node group instances |
| Additional network | Additional network/subnet to create in the default worker node group |

Enter information as required and click **Create Node Groups**, and a node group begins to be created. You can check the status in the list of node groups. It takes about 5 minutes to create; more time may be required depending on the node group setting.

> [Caution]
> Only the user who created the cluster can create node groups.

<a id="nodegroup-delete"></a>
### Delete Node Groups { #nodegroup-delete }
Select the node group to delete from the node group list and click **Delete Node Group** to proceed with deletion. It takes about 5 minutes to delete a node group; more time may be required depending on the node group status.

All nodes included in the node group are deleted in the following order:
* If the node is a member of a LoadBalancer type Service, the LB member is set to INACTIVE status. (Supported only for platform version 1.202602.0 or later)
* The node is drained.
* The node is deleted from the Kubernetes node resources.
* The node is deleted at the instance level.

<a id="nodegroup-scale-out"></a>
### Add Nodes to a Node Group { #nodegroup-scale-out }
You can add nodes to an operating node group. The current list of nodes will appear upon clicking the node list tab on the node group information query page. Click the Add Node button and enter the number of nodes to add them.

> [Caution]
> Nodes cannot be added manually to a node group with the autoscaler enabled.

<a id="nodegroup-scale-in"></a>
### Delete Nodes from a Node Group { #nodegroup-scale-in }
Nodes can be deleted from operating node groups. The current list of nodes will appear upon clicking the node list tab on the node group information query page. A confirmation dialog box will appear when a user selects nodes for deletion and clicks the Delete Node button. When the user confirms the node name and selects the OK button, the node will be deleted.

All nodes included in the node group are deleted in the following order:
* If the node is a member of a LoadBalancer type Service, the LB member is set to INACTIVE status. (Supported only for platform version 1.202602.0 or later)
* The node is drained.
* The node is deleted from the Kubernetes node resources.
* The node is deleted at the instance level.

> [Caution]
> Nodes cannot be deleted manually from a node group with the autoscaler enabled.

<a id="node-start-stop"></a>
### Stop and Start Nodes { #node-start-stop }
Nodes can be stopped from node groups and started again. The current list of nodes will appear upon clicking the node list tab on the node group information query page. Nodes can be stopped when a user selects nodes and clicks the stop button. The stopped nodes can be restarted when the user selects them and clicks the start button.

<a id="node-start-stop-action-process"></a>
#### Action Process

When you stop a node that is in the started state, the following sequence of actions occurs:

* If the node is a member of a LoadBalancer type Service, the member of that LB is set to INACTIVE status. (Supported only for platform version 1.202602.0 or later)
* The node is drained.
* The node is deleted from the Kubernetes node resources.
* The node is set to SHUTDOWN status at the instance level.

When you start a node that is in the stopped state, the following sequence of actions occurs:

* The node is set to ACTIVE status at the instance level.
* The node is added back to the Kubernetes node resources.


<a id="node-start-stop-constraints"></a>
#### Constraints

The stop and start node feature has the following constraints:

* A node in the started state can be stopped, and a node in the stopped state can be started.
* You cannot stop all nodes in a worker node group.
* You cannot stop nodes in a node group that has autoscaler enabled.
* You cannot enable the autoscaler for a node group that has stopped nodes.
* You cannot upgrade a node group that has stopped nodes.


<a id="node-start-stop-display-status"></a>
#### Status Display

The status icon on the node list tab is displayed based on the node's status. The status for each icon color is as follows:

* Green: Node in the started state
* Gray: Node in the stopped state
* Red: Node in an abnormal state

<a id="use-gpu-nodegroup"></a>
### Use GPU Node Groups { #use-gpu-nodegroup }
When you need to run GPU-based workloads through Kubernetes, you can create a node group composed of GPU instances.
Select the `g2` type when selecting a flavor while creating the clusters or node groups to create a GPU node group.

> [Note]
> GPU provided by NHN Cloud GPU instance is affiliated with NVIDIA. ([Identify available GPU specifications that can be used](/Compute/GPU%20Instance/en/overview/#gpu))
> nvidia-device-plugin required for Kubernetes to use an NVIDIA GPU will be installed automatically when creating a GPU node group.

You can check the basic configuration status of the created GPU nodes and run a simple operation test using the following methods.

<a id="use-gpu-nodegroup-node-level-status-check"></a>
#### Node-Level Status Check
Access the GPU node and run the `nvidia-smi` command.
The GPU driver is working normally if the output shows the following:

```
$ nvidia-smi
Mon Jul 27 14:38:07 2020
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 418.152.00   Driver Version: 418.152.00   CUDA Version: 10.1     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  Tesla T4            Off  | 00000000:00:05.0 Off |                    0 |
| N/A   30C    P8     9W /  70W |      0MiB / 15079MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+ 
```

<a id="use-gpu-nodegroup-kubernetes-level-status-check"></a>
#### Kubernetes-Level Status Check
Use the `kubectl` command to view information about available GPU resources at the cluster level.
Below are commands and execution results that displays the number of GPU cores available for each node.

```
$ kubectl get nodes -A -o custom-columns='NAME:.metadata.name,GPU Allocatable:.status.allocatable.nvidia\.com/gpu,GPU Capacity:.status.capacity.nvidia\.com/gpu'
NAME                                       GPU Allocatable   GPU Capacity
my-cluster-default-w-vdqxpwisjjsk-node-1   1                 1
```

<a id="use-gpu-nodegroup-sample-workload-execution-for-gpu-testing"></a>
#### Run Sample Workloads for GPU Testing
GPU nodes that belong to Kubernetes clusters provide resources such as `nvidia.com/gpu` in addition to CPU and memory.
To use GPU, enter as shown in the sample file below to be allocated with the `nvidia.com/gpu` resource.

* resnet.yaml
```
apiVersion: v1
kind: Pod
metadata:
  name: resnet-gpu-pod
spec:
  imagePullSecrets:
    - name: nvcr.dgxkey
  containers:
    - name: resnet
      image: nvcr.io/nvidia/tensorflow:18.07-py3
      command: ["mpiexec"]
      args: ["--allow-run-as-root", "--bind-to", "socket", "-np", "1", "python", "/opt/tensorflow/nvidia-examples/cnn/resnet.py", "--layers=50", "--precision=fp16", "--batch_size=64", "--num_iter=90"]
      resources:
        limits:
          nvidia.com/gpu: 1
``` 

Running the file above produces the following results:

```
$ kubectl create -f resnet.yaml
pod/resnet-gpu-pod created

$ kubectl get pods resnet-gpu-pod
NAME             READY   STATUS    RESTARTS   AGE
resnet-gpu-pod   0/1     Running   0          17s 

$ kubectl logs resnet-gpu-pod -n default -f
PY 3.5.2 (default, Nov 23 2017, 16:37:01)
[GCC 5.4.0 20160609]
TF 1.8.0
Script arguments:
  --layers 50
  --display_every 10
  --iter_unit epoch
  --batch_size 64
  --num_iter 100
  --precision fp16
Training
WARNING:tensorflow:Using temporary folder as model directory: /tmp/tmpjw90ypze
2020-07-31 00:57:23.020712: I tensorflow/stream_executor/cuda/cuda_gpu_executor.cc:898] successful NUMA node read from SysFS had negative value (-1), but there must be at least one NUMA node, so returning NUMA node zero
2020-07-31 00:57:23.023190: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1356] Found device 0 with properties:
name: Tesla T4 major: 7 minor: 5 memoryClockRate(GHz): 1.59
pciBusID: 0000:00:05.0
totalMemory: 14.73GiB freeMemory: 14.62GiB
2020-07-31 00:57:23.023226: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1435] Adding visible gpu devices: 0
2020-07-31 00:57:23.846680: I tensorflow/core/common_runtime/gpu/gpu_device.cc:923] Device interconnect StreamExecutor with strength 1 edge matrix:
2020-07-31 00:57:23.846743: I tensorflow/core/common_runtime/gpu/gpu_device.cc:929]      0
2020-07-31 00:57:23.846753: I tensorflow/core/common_runtime/gpu/gpu_device.cc:942] 0:   N
2020-07-31 00:57:23.847023: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1053] Created TensorFlow device (/job:localhost/replica:0/task:0/device:GPU:0 with 14151 MB memory) -> physical GPU (device: 0, name: Tesla T4, pci bus id: 0000:00:05.0, compute capability: 7.5)
  Step Epoch Img/sec   Loss  LR
     1   1.0     3.1  7.936  8.907 2.00000
    10  10.0    68.3  1.989  2.961 1.65620
    20  20.0   214.0  0.002  0.978 1.31220
    30  30.0   213.8  0.008  0.979 1.00820
    40  40.0   210.8  0.095  1.063 0.74420
    50  50.0   211.9  0.261  1.231 0.52020
    60  60.0   211.6  0.104  1.078 0.33620
    70  70.0   211.3  0.340  1.317 0.19220
    80  80.0   206.7  0.168  1.148 0.08820
    90  90.0   210.4  0.092  1.073 0.02420
   100 100.0   210.4  0.001  0.982 0.00020
```

> [Note]
> If you want to prevent workloads that do not require a GPU from being assigned to GPU nodes, refer to [Taints and Tolerations Overview](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/).

<a id="autoscaler"></a>
### Autoscaler { #autoscaler }
An autoscaler is a feature that automatically adjusts the number of nodes when a node group runs out of available resources or when node utilization stays below a certain threshold. It can be configured for each node group and operates independently. NKS supports two types of autoscalers.

* Metric-based autoscaler
* Cluster autoscaler

The autoscaler feature is configured and operates on a per-node-group basis. You can configure the feature through the following paths:

* Set for the default node group when creating a cluster
* Set for an additional node group when adding a node group
* Set for an existing node group

> [Caution]
> You cannot manually add or delete nodes in a node group that has autoscaler enabled.
> You cannot enable multiple autoscalers at the same time.

**Terminology**
The terms used in the autoscaler feature and their meanings are as follows:

| Term | Meaning |
| --- | --- |
| Scale-out | Increasing the number of nodes |
| Scale-in | Decreasing the number of nodes |

<a id="metric-base-autoscaler"></a>
#### Metric-Based Autoscaler
The metric-based autoscaler operates based on NHN Cloud's [Cloud Monitoring](/Monitoring/Cloud%20Monitoring/en/overview/) service. A metric collection agent installed on worker nodes sends system metrics to Cloud Monitoring every minute. If the collected metrics exceed or fall below the configured threshold, nodes are automatically added or removed. The scale-out and scale-in features can each be enabled independently.

<a id="metric-base-autoscaler-set"></a>
##### Metric-Based Autoscaler Settings
When the metric-based autoscaler is enabled, you can configure the following settings.

**Scale-Out Settings**

| Setting | Description | Valid Range | Default |
| --- | --- | --- | --- |
| Maximum node count | Maximum number of nodes that can be added | 1-10 | 10 |
| Enable | Enable or disable the node scale-out autoscaler | Enabled/Disabled | Disabled |

**Scale-In Settings**

| Setting | Description | Valid Range | Default |
| --- | --- | --- | --- |
| Minimum node count | Minimum number of nodes that can be removed | 1-10 | 10 |
| Enable | Enable or disable the node scale-in autoscaler | Enabled/Disabled | Disabled |

**Common Settings**

| Setting | Description | Valid Range | Default | Unit |
| --- | --- | --- | --- | --- |
| Rule operator | Operator applied between autoscaling trigger conditions<br>AND: triggers when all conditions are met<br>OR: triggers when any one condition is met | AND/OR | OR | - |
| Autoscaling wait time | Minimum wait time before the next scaling operation after the previous one completes (can be configured separately for scale-out and scale-in) | 1 - 60 | 10 | Minutes |
| Node performance metric | Metric to monitor (see table below) | Metric type | Required | - |
| Node adjustment count | Number of nodes to add or remove when autoscaling occurs | 1 - 10 | 1 | Nodes |
| Threshold | Metric threshold value for triggering a condition | Per metric | Required | - |
| Scale-down unneeded time | Run a scale action when a threshold condition lasts longer than a set time (2-60 minutes) | 2-60 | Required | Minutes |

**Node Performance Metrics**

| System Resource | Statistics Provided | Unit |
| --- | --- | --- |
| CPU utilization | Average CPU usage of all nodes in the node group | % |
| Memory utilization | Average memory usage of all nodes in the node group | % |
| Disk transfer rate (read) | Average amount of data read from disk per second across all nodes in the node group | Bytes/s |
| Disk transfer rate (write) | Average amount of data written to disk per second across all nodes in the node group | Bytes/s |
| Network transfer rate (outbound) | Average amount of network outbound data per second across all instances in the scaling group | Bytes/s |
| Network transfer rate (inbound) | Average amount of network inbound data per second across all instances in the scaling group | Bytes/s |

<a id="metric-base-autoscaler-resize"></a>
##### Scale-Out and Scale-In Conditions
Nodes are scaled out when all of the following conditions are met:

* Selected node performance metrics have exceeded the threshold for longer than the scale down unneeded time.
* Current node count < maximum node count
* Autoscaling wait time has elapsed

Nodes are scaled in when all of the following conditions are met:

* Node performance metrics have remained below the threshold for longer than the scale-down unneeded time
* Current node count > minimum node count
* Autoscaling wait time has elapsed

> [Note]
> The autoscaling wait time can be configured separately for the scale-out policy and the scale-in policy.
> In general, specifying a short scale-out wait time allows you to respond immediately to sudden load increases.
> Conversely, setting a longer scale-in wait time ensures stability by gradually reducing the number of instances.
> You must continuously monitor the service load and configure appropriate policies to prevent unnecessary instance usage.
> If only one specific node meets the condition, the policy will not be triggered. The evaluation is based on the average of all nodes in the node group.
> Whether a policy is triggered is determined by continuously checking whether the specified performance metric exceeds the threshold for the duration of the scale-down unneeded time.
> For example, if the condition is CPU utilization ≥ 90% and the scale-down unneeded time is 5 minutes, the policy will be triggered only if the CPU utilization stays at or above 90% for the full 5 minutes.

> [Note on node scale-in]
> When the metric-based autoscaler performs a scale-in, it removes nodes starting from the most recently created node.

<a id="metric-base-autoscaler-example"></a>
##### Operation Example

**Scale-Out Policy**

| Setting | Value |
| --- | --- |
| Maximum node count | 7 nodes |
| Node adjustment (scale-out) | 3 nodes |
| Wait time after scale-out | 5 minutes |
| Scale-out condition: metric | CPU |
| Scale-out condition: scale-down unneeded time | 5 minutes |
| Scale-out condition: threshold | ≥ 70% |

**Scale-In Policy**

| Setting | Value |
| --- | --- |
| Minimum node count | 3 nodes |
| Node adjustment (scale-in) | 1 node |
| Wait time after scale-in | 10 minutes |
| Scale-in condition: metric | CPU |
| Scale-in condition: scale-down unneeded time | 2 minutes |
| Scale-in condition: threshold | ≤ 30% |

**Operation Summary**

* Current node group node count: 5 nodes
* A scale-out is triggered when the average CPU usage of 5 nodes stays above 70% for 5 minutes
* Although the node adjustment setting in the scale-out policy is 3 nodes, only 2 nodes were actually added because the maximum node count is set to 7 (Node count: 5 → 7)
* 5 minutes after the node scale-out was completed, the average CPU usage of the 7 nodes stayed below 30% for 2 minutes, triggering a scale-in request
* The request is rejected because the wait time after scale-in is 10 minutes
* Node scale-in proceeds after 10 minutes have elapsed
* Since the scale-in policy is configured to remove 2 nodes, 1 node was removed (Node count: 7 → 6)
* No additional scale-in occurs during the 10-minute wait time after scale-in

**Detailed Operation Timeline**

| Time (min) | Avg CPU | Node Count | Scale Status | Description |
| ------ | ------ | ---- | ------ | --- |
| 0 – 3 | 65% | 5 | – | Below threshold (70%) |
| 4 | 72% | 5 | – | Scale-out threshold exceeded → scale-down unneeded time measurement of 5 minutes begins |
| 4 – 8 | 73% | 5 | – | Threshold exceeded for 5 consecutive minutes, scale-out condition met |
| 8 | 76% | 5 → 7 | Scale-out requested | Node adjustment is 3 nodes, but maximum node count is 7 → actually +2 nodes added<br>Node addition task starts |
| 8 – 13 | 65% | 7 | – | Node addition task completed<br>Minute 13, when the task ended, is set as the start point for the autoscaling wait time condition |
| 13 | 28% | 7 | – | Below scale-out threshold → scale-down unneeded time measurement of 2 minutes begins |
| 15 | 27% | 7 | Scale-in requested (rejected) | Threshold condition met for 2 consecutive minutes, scale-in condition satisfied<br>However, the request is rejected because the 10-minute wait time after scale-in (13 → 23) is still in progress |
| 15 – 23 | 27% | 7 | – | Wait time after scale-in continues |
| 23 | 27% | 7 → 6 | Scale-in | 10-minute wait time after scale-in expires, scale-in condition still met<br>Node adjustment is 1 node, so 1 node is removed |
| 24 | 28% | 6 |  | Node scale-in task completed<br>Minute 24, when the task ended, is set as the start point for the autoscaling wait time condition |
| 24 - | 28% | 6 | – | Below scale-out threshold → scale-down unneeded time measurement of 2 minutes begins<br>If the 10-minute wait time after scale-in (24 → 34) is satisfied thereafter, nodes will be removed one by one. |

<a id="cluster-autoscaler"></a>
#### Cluster Autoscaler
Cluster Autoscaler works based on the cluster-autoscaler feature, which is an officially supported feature of the Kubernetes project. For more information, see [Cluster Autoscaler](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler).

> [Note]
> The version of the `cluster-autoscaler` applied to NHN Kubernetes Service (NKS) is `1.19.0`.

<a id="cluster-autoscaler-set"></a>
##### Cluster Autoscaler Settings
When you enable Cluster Autoscaler, you can configure the following settings:

| Setting | Description | Valid range | Default | Unit |
| --- | --- | --- | --- | --- |
| Minimum node count | Minimum number of nodes that can be scaled down | 1-10 | 1 | Node |
| Maximum node count | Maximum number of nodes that can be scaled up | 1-10 | 10 | Node |
| Scale-down | Enable or disable node scale-down | Enabled/Disabled | Enabled | - |
| Resource utilization threshold | Threshold value for the resource utilization used as the basis for scale-down | 1-100 | 50 | % |
| Threshold area maintenance time | Duration for which a node must maintain resource utilization below the threshold to become a scale-down candidate | 1-1440 | 10 | Min |
| Scale-down delay after scale-up | Delay time before a node is monitored as a scale-down candidate after a scale-up event | 10-1440 | 10 | Min |

<a id="cluster-autoscaler-resize"></a>
##### Scale-Up and Scale-Down Conditions
Nodes are scaled up when all of the following conditions are met:

* There are no nodes available to schedule a pod.
* The current number of nodes is less than the maximum node count.

Nodes are scaled down when all of the following conditions are met:

* A node's resource utilization remains below the threshold for the threshold area maintenance time.
* The current number of nodes is greater than the minimum node count.

If some nodes contain at least one pod that meets the following conditions, then they will be excluded from the list of nodes to be scaled down:

* Pods constrained by a PodDisruptionBudget
* Pods in the `kube-system` namespace
* Pods not started by a control object such as `deployment` or `replicaset`
* Pods using local storage
* Pods that cannot be moved to another node due to constraints such as a node selector

For more detailed scale-up and scale-down conditions, see [Cluster Autoscaler FAQ](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md).

<a id="cluster-autoscaler-example"></a>
##### Usage Examples
The following examples illustrate how Autoscaler works.

**1. Enable Autoscaler**

Enables autoscaling on the default node group of the cluster you want. For this example, the number of nodes for the default group has been set to 1 and autoscaler settings are configured as follows:

| Setting | Value |
| --- | --- |
| Minimum node count | 1 |
| Maximum node count | 5 |
| Scale-down | Enabled |
| Resource utilization threshold | 50 |
| Threshold area maintenance time | 3 |
| Scale-down delay after scale-up | 10 |

**2. Deploy Pods**

Deploy pods using the following manifest:

> [Caution]
> The resource requests for the container must be specified, as shown in this manifest.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 15
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: "100m"
```

Because the total amount of CPU resources for the requested pods is bigger than the resources that a single node can handle, some of the pods are left behind in the `Pending` status, as shown below. In this case, the nodes will scale up.

```
# kubectl get pods
NAME                               READY   STATUS    RESTARTS   AGE
nginx-deployment-756fd4cdf-5gftm   1/1     Running   0          34s
nginx-deployment-756fd4cdf-64gtv   0/1     Pending   0          34s
nginx-deployment-756fd4cdf-7bsst   0/1     Pending   0          34s
nginx-deployment-756fd4cdf-8892p   1/1     Running   0          34s
nginx-deployment-756fd4cdf-8k4cc   1/1     Running   0          34s
nginx-deployment-756fd4cdf-cprp7   0/1     Pending   0          34s
nginx-deployment-756fd4cdf-cvs97   1/1     Running   0          34s
nginx-deployment-756fd4cdf-h7ftk   1/1     Running   0          34s
nginx-deployment-756fd4cdf-hv2fz   0/1     Pending   0          34s
nginx-deployment-756fd4cdf-j789l   0/1     Pending   0          34s
nginx-deployment-756fd4cdf-jrkfj   0/1     Pending   0          34s
nginx-deployment-756fd4cdf-m887q   0/1     Pending   0          34s
nginx-deployment-756fd4cdf-pvnfc   0/1     Pending   0          34s
nginx-deployment-756fd4cdf-wrj8b   1/1     Running   0          34s
nginx-deployment-756fd4cdf-x7ns5   0/1     Pending   0          34s
```

**3. Verify Node Scale-Up**

The following is the list of nodes before scale-up:

```
# kubectl get nodes
NAME                                            STATUS   ROLES    AGE   VERSION
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready    <none>   45m   v1.28.3
```

After about 5 to 10 minutes, you can see that the nodes have been scaled up as shown below:

```
# kubectl get nodes
NAME                                            STATUS   ROLES    AGE   VERSION
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready    <none>   48m   v1.28.3
autoscaler-test-default-w-ohw5ab5wpzug-node-1   Ready    <none>   77s   v1.28.3
autoscaler-test-default-w-ohw5ab5wpzug-node-2   Ready    <none>   78s   v1.28.3
```

You can confirm that the pods that were in the `Pending` status are now properly scheduled after the node scale-up.

```
# kubectl get pods -o wide
NAME                               READY   STATUS    RESTARTS   AGE     IP            NODE                                            NOMINATED NODE   READINESS GATES
nginx-deployment-756fd4cdf-5gftm   1/1     Running   0          4m29s   10.100.8.13   autoscaler-test-default-w-ohw5ab5wpzug-node-0   <none>           <none>
nginx-deployment-756fd4cdf-64gtv   1/1     Running   0          4m29s   10.100.22.5   autoscaler-test-default-w-ohw5ab5wpzug-node-1   <none>           <none>
nginx-deployment-756fd4cdf-7bsst   1/1     Running   0          4m29s   10.100.22.4   autoscaler-test-default-w-ohw5ab5wpzug-node-1   <none>           <none>
nginx-deployment-756fd4cdf-8892p   1/1     Running   0          4m29s   10.100.8.10   autoscaler-test-default-w-ohw5ab5wpzug-node-0   <none>           <none>
nginx-deployment-756fd4cdf-8k4cc   1/1     Running   0          4m29s   10.100.8.12   autoscaler-test-default-w-ohw5ab5wpzug-node-0   <none>           <none>
nginx-deployment-756fd4cdf-cprp7   1/1     Running   0          4m29s   10.100.12.7   autoscaler-test-default-w-ohw5ab5wpzug-node-2   <none>           <none>
nginx-deployment-756fd4cdf-cvs97   1/1     Running   0          4m29s   10.100.8.14   autoscaler-test-default-w-ohw5ab5wpzug-node-0   <none>           <none>
nginx-deployment-756fd4cdf-h7ftk   1/1     Running   0          4m29s   10.100.8.11   autoscaler-test-default-w-ohw5ab5wpzug-node-0   <none>           <none>
nginx-deployment-756fd4cdf-hv2fz   1/1     Running   0          4m29s   10.100.12.5   autoscaler-test-default-w-ohw5ab5wpzug-node-2   <none>           <none>
nginx-deployment-756fd4cdf-j789l   1/1     Running   0          4m29s   10.100.22.6   autoscaler-test-default-w-ohw5ab5wpzug-node-1   <none>           <none>
nginx-deployment-756fd4cdf-jrkfj   1/1     Running   0          4m29s   10.100.12.4   autoscaler-test-default-w-ohw5ab5wpzug-node-2   <none>           <none>
nginx-deployment-756fd4cdf-m887q   1/1     Running   0          4m29s   10.100.22.3   autoscaler-test-default-w-ohw5ab5wpzug-node-1   <none>           <none>
nginx-deployment-756fd4cdf-pvnfc   1/1     Running   0          4m29s   10.100.12.6   autoscaler-test-default-w-ohw5ab5wpzug-node-2   <none>           <none>
nginx-deployment-756fd4cdf-wrj8b   1/1     Running   0          4m29s   10.100.8.15   autoscaler-test-default-w-ohw5ab5wpzug-node-0   <none>           <none>
nginx-deployment-756fd4cdf-x7ns5   1/1     Running   0          4m29s   10.100.12.3   autoscaler-test-default-w-ohw5ab5wpzug-node-2   <none>           <none>
```

You can check scale-up events by running the following command:

```
# kubectl get events --field-selector reason="TriggeredScaleUp"
LAST SEEN   TYPE     REASON             OBJECT                                 MESSAGE
4m          Normal   TriggeredScaleUp   pod/nginx-deployment-756fd4cdf-64gtv   pod triggered scale-up: [{default-worker-bf5999ab 1->3 (max: 5)}]
4m          Normal   TriggeredScaleUp   pod/nginx-deployment-756fd4cdf-7bsst   pod triggered scale-up: [{default-worker-bf5999ab 1->3 (max: 5)}]
...
```

**4. Verify Node Scale-Down After Deleting Pods**

Deleting the deployed deployment removes all of the pods that were deployed.

```
# kubectl get pods
NAME                               READY   STATUS        RESTARTS   AGE
nginx-deployment-756fd4cdf-5gftm   0/1     Terminating   0          20m
nginx-deployment-756fd4cdf-64gtv   0/1     Terminating   0          20m
nginx-deployment-756fd4cdf-7bsst   0/1     Terminating   0          20m
nginx-deployment-756fd4cdf-8892p   0/1     Terminating   0          20m
nginx-deployment-756fd4cdf-8k4cc   0/1     Terminating   0          20m
nginx-deployment-756fd4cdf-cprp7   0/1     Terminating   0          20m
nginx-deployment-756fd4cdf-h7ftk   0/1     Terminating   0          20m
nginx-deployment-756fd4cdf-hv2fz   0/1     Terminating   0          20m
nginx-deployment-756fd4cdf-j789l   0/1     Terminating   0          20m
nginx-deployment-756fd4cdf-jrkfj   0/1     Terminating   0          20m
nginx-deployment-756fd4cdf-m887q   0/1     Terminating   0          20m
nginx-deployment-756fd4cdf-pvnfc   0/1     Terminating   0          20m
nginx-deployment-756fd4cdf-wrj8b   0/1     Terminating   0          20m
nginx-deployment-756fd4cdf-x7ns5   0/1     Terminating   0          20m
#
# kubectl get pods
No resources found in default namespace.
#
```

After a while, you can see nodes are scaled down to 1. The time it takes to scale down may vary depending on your settings.

```
# kubectl get nodes
NAME                                            STATUS   ROLES    AGE   VERSION
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready    <none>   71m   v1.28.3
```

You can check scale-down events by running the following command:

```
# kubectl get events --field-selector reason="ScaleDown"
LAST SEEN   TYPE     REASON      OBJECT                                               MESSAGE
13m         Normal   ScaleDown   node/autoscaler-test-default-w-ohw5ab5wpzug-node-1   node removed by cluster autoscaler
13m         Normal   ScaleDown   node/autoscaler-test-default-w-ohw5ab5wpzug-node-2   node removed by cluster autoscaler
```

You can check the status of each node group's autoscaler through `configmap/cluster-autoscaler-status`. Configmaps are created in different namespaces per node group. The following is the naming convention for namespace per node group created by the autoscaler:

* Format: nhn-ng-{node-group-name}
* {node-group-name} is replaced with the name of the node group.
* The node group name for the default node group is "default-worker".

The status of the default node group's autoscaler can be checked using the following method. For more information, see [Cluster Autoscaler FAQ](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md).

```
# kubectl get configmap/cluster-autoscaler-status -n nhn-ng-default-worker -o yaml
apiVersion: v1
data:
  status: |+
    Cluster-autoscaler status at 2020-11-03 12:39:12.190150095 +0000 UTC:
    Cluster-wide:
      Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0)
                   LastProbeTime:      2020-11-03 12:39:12.185954244 +0000 UTC m=+43.664545435
                   LastTransitionTime: 2020-11-03 12:38:41.705407217 +0000 UTC m=+13.183998415
      ScaleUp:     NoActivity (ready=1 registered=1)
                   LastProbeTime:      2020-11-03 12:39:12.185954244 +0000 UTC m=+43.664545435
                   LastTransitionTime: 2020-11-03 12:38:41.705407217 +0000 UTC m=+13.183998415
      ScaleDown:   NoCandidates (candidates=0)
                   LastProbeTime:      2020-11-03 12:39:12.185954244 +0000 UTC m=+43.664545435
                   LastTransitionTime: 2020-11-03 12:38:41.705407217 +0000 UTC m=+13.183998415

NodeGroups:
      Name:        default-worker-f9a9ee5e
      Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0 cloudProviderTarget=1 (minSize=1, maxSize=5))
                   LastProbeTime:      2020-11-03 12:39:12.185954244 +0000 UTC m=+43.664545435
                   LastTransitionTime: 2020-11-03 12:38:41.705407217 +0000 UTC m=+13.183998415
      ScaleUp:     NoActivity (ready=1 cloudProviderTarget=1)
                   LastProbeTime:      2020-11-03 12:39:12.185954244 +0000 UTC m=+43.664545435
                   LastTransitionTime: 2020-11-03 12:38:41.705407217 +0000 UTC m=+13.183998415
      ScaleDown:   NoCandidates (candidates=0)
                   LastProbeTime:      2020-11-03 12:39:12.185954244 +0000 UTC m=+43.664545435
                   LastTransitionTime: 2020-11-03 12:38:41.705407217 +0000 UTC m=+13.183998415

kind: ConfigMap
metadata:
  annotations:
    cluster-autoscaler.kubernetes.io/last-updated: 2020-11-03 12:39:12.190150095 +0000
      UTC
  creationTimestamp: "2020-11-03T12:38:28Z"
  name: cluster-autoscaler-status
  namespace: nhn-ng-default-worker
  resourceVersion: "1610"
  selfLink: /api/v1/namespaces/nhn-ng-default-worker/configmaps/cluster-autoscaler-status
  uid: e72bd1a2-a56f-41b4-92ee-d11600386558
```

> [Note]
> The content in the `Cluster-wide` area of the status information is the same as the content in the `NodeGroups` area.

<a id="cluster-autoscaler-with-hpa"></a>
##### Example of operation in conjunction with the HPA (HorizontalPodAutoscaler) feature
Horizontal Pod Autoscaler (HPA) observes resource usage, such as CPU, to auto-scale the number of pods in ReplicationController, Deployment, ReplicaSet, and StatefulSet. As the number of pods is adjusted, there could be too many or too few resources available in the node. At this moment, utilize the Autoscaler feature to increase or decrease the number of nodes. In this example, we will see how HPA and Autoscaler can work together to deal with this issue. For more information on HPA, see [Horizontal Pod Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/).

**1. Enabling Autoscaler**
Enable the Autoscaler as in the example above.

**2. Configuring HPA**
Deploy a container that creates CPU load for a certain amount of time after receiving a web request. Then, expose the service. The following is taken from the `php-apache.yaml` file.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: php-apache
spec:
  selector:
    matchLabels:
      run: php-apache
  replicas: 1
  template:
    metadata:
      labels:
        run: php-apache
    spec:
      containers:
      - name: php-apache
        image: k8s.gcr.io/hpa-example
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: 500m
          requests:
            cpu: 200m
---
apiVersion: v1
kind: Service
metadata:
  name: php-apache
  labels:
    run: php-apache
spec:
  ports:
  - port: 80
  selector:
    run: php-apache
```

```
# kubectl apply -f php-apache.yaml
deployment.apps/php-apache created
service/php-apache created
```

Now, set up HPA. For the php-apache deployment object just created in the previous step, set as follows: min pod count = 1, max pod count = 30, target CPU load = 50%.

```
# kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=30
horizontalpodautoscaler.autoscaling/php-apache autoscaled
```

If you look up the state of HPA, you can see the settings and the current state. Since no web request that causes CPU load has been sent yet, CPU load is still at 0%.

```
# kubectl get hpa
NAME         REFERENCE               TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   0%/50%    1         30        1          80s
```

**3. Authorizing Load**
Now run the pod that triggers load in the new terminal. This pod sends web requests without stopping. You can stop this requesting action with `Ctrl+C`.

```
# kubectl run -i --tty load-generator --rm --image=busybox --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache; done"
If you don't see a command prompt, try pressing enter.
OK!OK!OK!OK!OK!OK!OK!
```

Using the `kubectl top nodes` command, you can see the current resource usage of the node. You can observe the increase in CPU load as time goes after running the pod that causes load.

```
# kubectl top nodes
NAME                                            CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
autoscaler-test-default-w-ohw5ab5wpzug-node-0   66m          6%     1010Mi          58%

(After a moment)

# kubectl top nodes
NAME                                            CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
autoscaler-test-default-w-ohw5ab5wpzug-node-0   574m         57%    1013Mi          58%
```

If you look up the state of HPA, you can see that the CPU load has increased, and REPLICAS (= number of pods) has increased to accommodate it.

```
# kubectl get hpa
NAME         REFERENCE               TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   250%/50%   1         30        5          2m44s
```

**4. Checking the operation of Autoscaler**
If you look up pods, due to the increase in the number of pods, you can see some pods are running as they are scheduled to `node-0`, but some are still pending.

```
# kubectl get pods -o wide
NAME                          READY   STATUS    RESTARTS   AGE     IP            NODE                                            NOMINATED NODE   READINESS GATES
load-generator                1/1     Running   0          2m      10.100.8.39   autoscaler-test-default-w-ohw5ab5wpzug-node-0   <none>           <none>
php-apache-79544c9bd9-6f7nm   0/1     Pending   0          65s     <none>        <none>                                          <none>           <none>
php-apache-79544c9bd9-82xkn   1/1     Running   0          80s     10.100.8.41   autoscaler-test-default-w-ohw5ab5wpzug-node-0   <none>           <none>
php-apache-79544c9bd9-cjj9q   0/1     Pending   0          80s     <none>        <none>                                          <none>           <none>
php-apache-79544c9bd9-k6nnt   1/1     Running   0          4m27s   10.100.8.38   autoscaler-test-default-w-ohw5ab5wpzug-node-0   <none>           <none>
php-apache-79544c9bd9-mplnn   0/1     Pending   0          19s     <none>        <none>                                          <none>           <none>
php-apache-79544c9bd9-t2knw   1/1     Running   0          80s     10.100.8.40   autoscaler-test-default-w-ohw5ab5wpzug-node-0   <none>           <none>
```

This situation of not being able to schedule pods is the node extension condition for Autoscaler. If you look up the state information provided by Cluster Autoscaler pod, you can see that ScaleUp is in InProgress state.

```
# kubectl get cm/cluster-autoscaler-status -n nhn-ng-default-worker -o yaml
apiVersion: v1
data:
  status: |+
    Cluster-autoscaler status at 2020-11-24 13:00:40.210137143 +0000 UTC:
    Cluster-wide:
      Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0)
                   LastProbeTime:      2020-11-24 13:00:39.930763305 +0000 UTC m=+1246178.729396969
                   LastTransitionTime: 2020-11-10 02:51:14.353177175 +0000 UTC m=+13.151810823
      ScaleUp:     InProgress (ready=1 registered=1)
                   LastProbeTime:      2020-11-24 13:00:39.930763305 +0000 UTC m=+1246178.729396969
                   LastTransitionTime: 2020-11-24 12:58:34.83642035 +0000 UTC m=+1246053.635054003
      ScaleDown:   NoCandidates (candidates=0)
                   LastProbeTime:      2020-11-24 13:00:39.930763305 +0000 UTC m=+1246178.729396969
                   LastTransitionTime: 2020-11-20 01:42:32.287146552 +0000 UTC m=+859891.085780205

NodeGroups:
      Name:        default-worker-bf5999ab
      Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0 cloudProviderTarget=2 (minSize=1, maxSize=3))
                   LastProbeTime:      2020-11-24 13:00:39.930763305 +0000 UTC m=+1246178.729396969
                   LastTransitionTime: 2020-11-10 02:51:14.353177175 +0000 UTC m=+13.151810823
      ScaleUp:     InProgress (ready=1 cloudProviderTarget=2)
                   LastProbeTime:      2020-11-24 13:00:39.930763305 +0000 UTC m=+1246178.729396969
                   LastTransitionTime: 2020-11-24 12:58:34.83642035 +0000 UTC m=+1246053.635054003
      ScaleDown:   NoCandidates (candidates=0)
                   LastProbeTime:      2020-11-24 13:00:39.930763305 +0000 UTC m=+1246178.729396969
                   LastTransitionTime: 2020-11-20 01:42:32.287146552 +0000 UTC m=+859891.085780205
...
```

After a moment, you can see that one more node (node-8) has been added.

```
# kubectl get nodes
NAME                                            STATUS     ROLES    AGE   VERSION
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready      <none>   22d   v1.28.3
autoscaler-test-default-w-ohw5ab5wpzug-node-8   Ready      <none>   90s   v1.28.3
```

You can see that all pods that were in Pending state have been successfully scheduled and are now in Running state.

```
# kubectl get pods -o wide
NAME                          READY   STATUS    RESTARTS   AGE     IP            NODE                                            NOMINATED NODE   READINESS GATES
load-generator                1/1     Running   0          5m32s   10.100.8.39   autoscaler-test-default-w-ohw5ab5wpzug-node-0   <none>           <none>
php-apache-79544c9bd9-6f7nm   1/1     Running   0          4m37s   10.100.42.3   autoscaler-test-default-w-ohw5ab5wpzug-node-8   <none>           <none>
php-apache-79544c9bd9-82xkn   1/1     Running   0          4m52s   10.100.8.41   autoscaler-test-default-w-ohw5ab5wpzug-node-0   <none>           <none>
php-apache-79544c9bd9-cjj9q   1/1     Running   0          4m52s   10.100.42.5   autoscaler-test-default-w-ohw5ab5wpzug-node-8   <none>           <none>
php-apache-79544c9bd9-k6nnt   1/1     Running   0          7m59s   10.100.8.38   autoscaler-test-default-w-ohw5ab5wpzug-node-0   <none>           <none>
php-apache-79544c9bd9-mplnn   1/1     Running   0          3m51s   10.100.42.4   autoscaler-test-default-w-ohw5ab5wpzug-node-8   <none>           <none>
php-apache-79544c9bd9-t2knw   1/1     Running   0          4m52s   10.100.8.40   autoscaler-test-default-w-ohw5ab5wpzug-node-0   <none>           <none>
```

If you press `Ctrl+C` to stop the pod (`load-generator`) that was executed for load, load will decrease after a while. The lower the load, the lower the CPU usage occupied by the pod, which in return decrease the number of pods.

```
# kubectl get hpa
NAME         REFERENCE               TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   0%/50%    1         30        1          31m
```

As the number of pods decreases, the node's resource usage also decreases, resulting in fewer nodes. You can see the newly added node-8 has been reduced.

```
# kubectl get nodes
NAME                                            STATUS   ROLES    AGE   VERSION
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready    <none>   22d   v1.28.3
```

<a id="user-script-old"></a>
### User script (old) { #user-script-old }
You can register a user script when creating clusters and additional node groups. A user script has the following features.

* Feature settings
    * This feature can be configured for each worker node group.
    * The user script entered when creating a cluster is applied to the default worker node group.
    * The user script entered when creating an additional node group is applied to that worker node group.
    * **You cannot change the content of a user script after the worker node group has been created.**
* Script execution timing
    * The user script runs during the instance initialization process as part of worker node initialization.
    * After the user script runs, the instance is configured and registered as a worker node in the worker node group.
* Script content
    * The first line of a user script must start with `#!`.
    * The maximum script size is 64 KB.
    * The script runs with root privileges.
    * Script execution logs are stored in the following locations:
        * Script exit code: `/var/log/userscript.exitcode`
        * Script standard output and standard error stream: `/var/log/userscript.output`

<a id="user-script"></a>
### User script { #user-script }
The features of a new version of user script are included in the node groups created after July 26, 2022. The following features are found in the new version.

* **You can change the content of a user script even after the worker node group has been created.**
    * However, the changes are applied only to nodes created after the user script is updated.
* Script execution logs are stored in the following locations:
    * Script exit code: `/var/log/userscript_v2.exitcode`
    * Script standard output and standard error stream: `/var/log/userscript_v2.output`

* Relationship with the old version
    * The new version replaces the old version.
        * The user script set when creating node groups through the console and API is configured for the new version.
    * For the worker node group that sets the old version of a user script, the old- and new-version features operate independently.
        * The content of the user script set in the old version cannot be changed.
        * The content of the user script set in the new version can be changed.
    * If user scripts are set in both the old and new versions, they run in the following order:
        1. Old version user script
        2. New version user script

<a id="instance-flavor-update"></a>
### Change instance flavor { #instance-flavor-update }
Changes the instance flavor of a worker node group. The instance flavor of all worker nodes belonging to the worker node group is changed.


<a id="instance-flavor-update-process"></a>
#### Process

Instance flavor changes proceed in the following order:

1. Disable the cluster autoscaler feature.
2. Add a buffer node to the worker node group.
3. Perform the following tasks for all worker nodes within the worker node group, sequentially:
    1. Evict the pods running on the worker node and transition the node to an unschedulable state.
    2. Change the instance flavor of the worker node.
    3. Transition the node to a schedulable state.
4. Evict the pods running on the buffer node and delete the buffer node.
5. Re-enable the cluster autoscaler feature.

Instance flavor changes work similarly to worker component upgrades. For more details on creating and deleting buffer nodes and evicting pods, see [Upgrade a Cluster](/Container/NKS/en/user-guide/#cluster-upgrade).


<a id="instance-flavor-update-constraints"></a>
#### Constraints

The flavors that can be changed to depend on the current instance flavor.

* m2, c2, r2, t2, x1 flavor instances can be changed to m2, c2, r2, t2, or x1 flavors.
* m2, c2, r2, t2, x1, g2 flavor instances cannot be changed to u2 flavors.
* u2 flavor instances cannot have their flavor changed after creation. Changing to another u2 flavor is also not supported.

<a id="custom-image"></a>
### Use Custom Images as Worker Images { #custom-image }

You can create a worker node group using your custom images. This requires additional work (conversion to NKS worker node) in NHN Cloud Image Builder so that the custom image can be used as a worker node image. In Image Builder, you can create custom worker node images by creating image templates with the worker node application of NHN Kubernetes Service (NKS). For more information on Image Builder, see [Image Builder User Guide](/Compute/Image%20Builder/en/console-guide/#_1).

> [Caution]
> Conversion to NKS worker node involves installing packages and changing settings, so if you work with images that don't work properly, it may fail.
> You may be charged for using the Image Builder service.

<a id="custom-image-constraints"></a>
#### Constraints

The following table lists the supported OS images and the application version to select for each OS image. You must select the correct application version to match the image of the base instance from which you are creating your custom image.

| OS | Image | Application Version |
| --- | --- | --- |
| Rocky | Rocky Linux 8.10 (2024.08.20)  | 1.6 |
|  | Rocky Linux 8.10 (2024.11.19)  | 1.7 |
|  | Rocky Linux 8.10 (2025.02.25)  | 1.8 |
|  | Rocky Linux 9.5 (2025.11.18)   | 1.9 |
|  | Rocky Linux 9.7 (2026.03.10)   | 1.9 |
| Ubuntu | Ubuntu Server 22.04.3 LTS (2023.11.21) | 1.3 |
|  | Ubuntu Server 22.04.3 LTS (2024.02.20)  | 1.4 |
|  | Ubuntu Server 22.04.5 LTS (2024.05.21)  | 1.5 |
|  | Ubuntu Server 22.04.5 LTS (2024.11.19)  | 1.7 |
|  | Ubuntu Server 22.04.5 LTS (2025.02.25)  | 1.8 |
|  | Ubuntu Server 22.04.5 LTS (2025.11.18)  | 1.9 |
|  | Ubuntu Server 24.04.3 LTS (2025.11.18)  | 1.9 |
|  | Ubuntu Server 22.04.3 LTS (2026.03.10)  | 1.10 |
|  | Ubuntu Server 24.04.3 LTS (2026.03.10)  | 1.10 |


> [Note]
> During the process of converting a custom image to a worker node image, GPU drivers are installed according to the options selected.
> So even if you create a custom GPU worker node image, you don't need to create a custom image with a GPU instance.

<a id="custom-image-process"></a>
#### Process

To use a custom image as a worker node image, perform the following steps in the Image Builder service.

1. Click **Create Image Template**.
2. Select an application and fill in the **Image Template Name**, **OS**, **Minimum Block Storage (GB)**, **User Script**, and **Description**.
    * For a worker node group that does not use a GPU flavor, select the NHN Kubernetes Service (NKS) Worker Node application.
    * For a worker node group that uses a GPU flavor, select the NHN Kubernetes Service (NKS) Worker Node (GPU) application.
3. Click **Confirm** to create the image template.
4. Select the created image template and choose **Build Image**.
5. On the **Build Image** screen, select the **Personal Images** tab and choose the custom image to convert to an NKS worker node.
6. Click **Confirm** to proceed with the NKS worker node conversion and create a new image.
7. On the **Create Cluster** or **Create Node Group** screen, select the created custom image.

![nkscustom_image_1.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/nkscustom_image_1.png)

![nkscustom_image_2.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/nkscustom_image_2.png)

![nkscustom_image_3.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/nkscustom_image_3.png)

<a id="extra-volumes"></a>
### Additional Block Storage { #extra-volumes }
You can use additional block storage for a node group. You can specify additional block storage when creating a cluster or node group, or add additional block storage to an existing node group. Additional block storage has the following characteristics:

* You can configure up to 3 additional block storage volumes per node group, with each volume ranging from 1 to 2,048 GB in size.
* The additional block storage settings for a node group are applied equally to all worker nodes in the node group.
    * When additional block storage settings are changed, the changes are applied to all worker nodes in the node group.
* Changes to additional block storage support only resizing and mount path modifications.
    * Deleting created additional block storage is not possible.
    * You cannot resize to a value smaller than the currently configured size.
* The name of additional block storage is assigned in the format `{cluster name}-{node group name}-{node name}-extra-volume-{index}`.
* If a mount path is entered, the system attempts to mount the additional block storage at the specified path after it is created.
    * If no path is entered, no mounting is performed.
    * If mounting fails due to an invalid mount path, the feature does not work.

[Caution]
> Changing the settings of additional block storage involves unmounting existing volumes, which may affect services that are currently in use.

<a id="extra-security-groups"></a>
### Additional Security Groups { #extra-security-groups }
You can configure additional security groups for a node group. You can specify additional security groups when creating a cluster or node group, or configure additional security groups for an existing node group. Additional security groups have the following characteristics:

* You can configure up to 8 additional security groups per subnet.
* The additional security group settings for a node group are applied equally to all worker nodes in the node group.
* If no additional security groups are specified, only the cluster's default security group is applied.
* Security groups that a user has configured directly on individual nodes are not displayed in the node group's additional security group list.

[Note]
> Additional security groups specified when creating a node group in the console are applied to the default network and all additional networks. Changes to additional security groups for individual networks can be made after the node group is created.

[Caution]
> When additional security groups are configured for a node group, any security groups currently assigned to existing instances that are not defined in the additional security groups will be removed.
> Changing additional security groups modifies the network settings, which may temporarily affect communication while the settings are being applied.

<a id="fip-auto-bind"></a>
### Floating IP Auto-Assignment { #fip-auto-bind }
You can use the floating IP auto-assignment feature for a node group. When this feature is enabled, a floating IP is automatically assigned to each node when it is created. You can choose whether to enable this feature when creating a cluster or an additional node group. Once set, the option cannot be changed later. The following items are required to enable the floating IP auto-assignment feature:

| Item | Description | 
| --- | --- | 
| Subnet to connect | The subnet of the network interface to which the floating IP will be assigned. This subnet must be the cluster's default subnet or included in the node group's additional subnets. |
| Floating IP label | An identifier used to select the floating IP to assign to a node. If not specified, all floating IPs are eligible for assignment. |


The floating IP auto-assignment feature has the following characteristics:

* It does not create floating IPs.
  * It works by assigning floating IPs that the user has created in advance. If there are not enough available floating IPs, node scale-out may fail.
* Enabling or disabling the floating IP auto-assignment feature and changing its settings do not affect existing nodes.
  * Even if the feature is enabled for a node group that did not previously have it enabled, floating IPs are not assigned to existing nodes.
  * Even if the feature is disabled for a node group that had it enabled, the floating IPs assigned to existing nodes are not released.

<a id="cluster-management"></a>
## Cluster Management { #cluster-management }
To manage and operate a cluster from a remote host, you need `kubectl`, the command-line tool (CLI) provided by Kubernetes.

<a id="kubectl-install"></a>
### Install kubectl { #kubectl-install }
kubectl can be used immediately by downloading the executable file without any special installation process. The download commands for each operating system are as follows:

> [Caution]
> If you install Kubernetes-related components such as kubeadm, kubelet, and kubectl using the package manager on worker nodes, the cluster may malfunction. If you are installing kubectl on a worker node, please refer to the download command below to download the file.

| Operating System | Download Command |
| --- | --- |
| Linux | curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.15.7/bin/linux/amd64/kubectl |
| MacOS | curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.15.7/bin/darwin/amd64/kubectl |
| Windows | curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.15.7/bin/windows/amd64/kubectl.exe |

For more information on other installation methods and options, see the [Install and Set Up kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) documentation.

<a id="kubectl-install-change-permission"></a>
#### Change Permissions
Downloaded files do not have execute permissions by default. You must add execute permissions.

```
$ chmod +x kubectl
```

<a id="kubectl-install-change-the-location-or-set-the-path"></a>
#### Change Location or Set Path
To run kubectl from any directory, move it to a path specified in the environment variable, or add the path where kubectl is located to the environment variable.

* Move to a path specified in the environment variable
```
$ sudo mv kubectl /usr/local/bin/
```

* Add the path to the environment variable
```
// Run from the path where kubectl is located
$ export PATH=$PATH:$(pwd)
```

<a id="kubectl-set-kubeconfig"></a>
### Configuration { #kubectl-set-kubeconfig }
To access Kubernetes cluster with kubectl, cluster configuration file (kubeconfig) is required. On the NHN Cloud web console, open the **Container > NHN Kubernetes Service(NKS)** page and select a cluster to access. From **Basic Information**, click **Download** of **Configuration Files** to download a configuration file. Move the downloaded configuration file to a location of your choice to serve it as a reference for kubectl execution.

> [Caution]
> A configuration file downloaded from the NHN Cloud web console includes cluster information and token for authentication. With the file, you're authorized to access corresponding Kubernetes clusters. Take cautions for not losing configuration files.

kubectl requires a cluster configuration file every time it is executed, so a cluster configuration file must be specified by using the `--kubeconfig` option. However, if the environment variable includes specific path for a cluster configuration file, there is no need to specify each option.

```
$ export KUBECONFIG={cluster configuration file path}
```

You may copy cluster configuration file path to `$HOME/.kube/config`, which is the default configuration file of kubectl, if you don't want to save it to an environment variable. However, when there are many clusters, it is easier to change environment variables.

<a id="kubectl-check-connection"></a>
### Check Connection { #kubectl-check-connection }
Use the `kubectl version` command to verify that the configuration is correct. If there are no issues, `Server Version` is displayed.

```
$ kubectl version
Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.7", GitCommit:"6c143d35bb11d74970e7bc0b6c45b6bfdffc0bd4", GitTreeState:"clean", BuildDate:"2019-12-11T12:42:56Z", GoVersion:"go1.12.12", Compiler:"gc", Platform:"darwin/amd64"}
Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.7", GitCommit:"6c143d35bb11d74970e7bc0b6c45b6bfdffc0bd4", GitTreeState:"clean", BuildDate:"2019-12-11T12:34:17Z", GoVersion:"go1.12.12", Compiler:"gc", Platform:"linux/amd64"}
```

* Client Version: Version information of the kubectl file that was run
* Server Version: Version information of Kubernetes that makes up the cluster

<a id="certificatesigningrequest"></a>
### CSR(CertificateSigningRequest) { #certificatesigningrequest }
Using the Certificate API of Kubernetes, you can request and issue the X.509 certificate for a Kubernetes API client. CSR resource lets you request a certificate and decide to accept/reject the request. For more information, see the [Certificate Signing Requests](https://kubernetes.io/docs/reference/access-authn-authz/certificate-signing-requests/) document.

<a id="certificatesigningrequest-csr-request-and-issue-approval-example"></a>
#### CSR Request and Issuance Approval Example
First of all, create a private key. For more information on certificate creation, see the [Certificates](https://kubernetes.io/docs/tasks/administer-cluster/certificates/) document.

```
$ openssl genrsa -out dev-user1.key 2048
Generating RSA private key, 2048 bit long modulus
...........................................................................+++++
..................+++++
e is 65537 (0x010001)

$ openssl req -new -key dev-user1.key -subj "/CN=dev-user1" -out dev-user1.csr
```

Create a CSR resource that includes created private key information and request certificate issuance.

```
$ BASE64_CSR=$(cat dev-user1.csr | base64 | tr -d '\n')
$ cat <<EOF > csr.yaml -
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: dev-user1
spec:
  groups:
  - system:authenticated
  request: ${BASE64_CSR}
  signerName: kubernetes.io/kube-apiserver-client
  expirationSeconds: 86400  # one day
  usages:
  - client auth
EOF

$ kubectl apply -f csr.yaml
certificatesigningrequest.certificates.k8s.io/dev-user1 created
```

The registered CSR is in `Pending` state. This state indicates waiting for issuance approval or rejection.

```
$ kubectl get csr
NAME        AGE   SIGNERNAME                            REQUESTOR   REQUESTEDDURATION   CONDITION
dev-user1   3s    kubernetes.io/kube-apiserver-client   admin       24h                 Pending
```

Approve this certificate issuance request.

```
$ kubectl certificate approve dev-user1
certificatesigningrequest.certificates.k8s.io/dev-user1 approved
```

If you check the CSR again, you can see that it has been changed to the `Approved,Issued` state.

```
$ kubectl get csr
NAME        AGE   SIGNERNAME                            REQUESTOR   REQUESTEDDURATION   CONDITION
dev-user1   28s   kubernetes.io/kube-apiserver-client   admin       24h                 Approved,Issued
```

You can retrieve the certificate as follows. The certificate is the value of the certificate field in status.

```
$ apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"certificates.k8s.io/v1","kind":"CertificateSigningRequest","metadata":{"annotations":{},"name":"dev-user1"},"spec":{"expirationSeconds":86400,"groups":["system:authenticated"],"request":"LS0t..(omitted)","signerName":"kubernetes.io/kube-apiserver-client","usages":["client auth"]}}
  creationTimestamp: "2023-09-15T05:53:12Z"
  name: dev-user1
  resourceVersion: "176619"
  uid: a5813153-40de-4725-9237-3bf684fd1db9
spec:
  expirationSeconds: 86400
  groups:
  - system:masters
  - system:authenticated
  request: LS0t..(omitted)
  signerName: kubernetes.io/kube-apiserver-client
  usages:
  - client auth
  username: admin
status:
  certificate: LS0t..(omitted)
  conditions:
  - lastTransitionTime: "2023-09-15T05:53:26Z"
    lastUpdateTime: "2023-09-15T05:53:26Z"
    message: This CSR was approved by kubectl certificate approve.
    reason: KubectlApprove
    status: "True"
    type: Approved
```

> [Caution]
> This feature is provided only when the time of cluster creation falls within the following period:
> 
> * Pangyo region: Clusters created on December 29, 2020, or later
> * Pyeongchon region: Clusters created on December 24, 2020, or later

<a id="admission-controller"></a>
### Admission Controller Plugin { #admission-controller }
The admission controller can intercept a Kubernetes API server request and change objects or deny the request. See [Admission Controller](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/) for more information about the admission controller. For usage examples of the admission controller, see [Admission Controller Guide](https://kubernetes.io/blog/2019/03/21/a-guide-to-kubernetes-admission-controllers/).

The type of plugin applied to the admission controller varies depending on the cluster version and the time of cluster creation. For more information, see the list of plugins available depending on the time of cluster creation by region.

<a id="admission-controller-v11913-or-earlier"></a>
#### v1.19.13 or Earlier
The following applies to clusters created on February 22, 2021, or earlier for the Pangyo region and clusters created on February 17, 2021, or earlier for the Pyeongchon region.

* DefaultStorageClass
* DefaultTolerationSeconds
* LimitRanger
* MutatingAdmissionWebhook
* NamespaceLifecycle
* NodeRestriction
* ResourceQuota
* ServiceAccount
* ValidatingAdmissionWebhook

The following applies to clusters created on February 23, 2021, or later for the Pangyo region and clusters created on February 18, 2021, or later for the Pyeongchon region.

* DefaultStorageClass
* DefaultTolerationSeconds
* LimitRanger
* MutatingAdmissionWebhook
* NamespaceLifecycle
* NodeRestriction
* PodSecurityPolicy (newly added)
* ResourceQuota
* ServiceAccount
* ValidatingAdmissionWebhook

<a id="admission-controller-v12012-or-later"></a>
#### v1.20.12 or Later
All default active admission controllers per Kubernetes version are enabled. The following controllers are activated in addition to the default active admission controllers.

* NodeRestriction
* PodSecurityPolicy

<a id="cluster-upgrade"></a>
### Cluster Upgrade { #cluster-upgrade }
NHN Kubernetes Service (NKS) supports upgrading Kubernetes components in operating Kubernetes clusters.

<a id="cluster-upgrade-policy-of-supporting-different-kubernetes-versions"></a>
#### Kubernetes Version Skew Support Policy
Kubernetes version is represented as `x.y.z`. `x` is the major, `y` is the minor, and `z` is the patch version. If features are added, it is a major or minor version upgrade. If it provides features compatible with previous versions, such as bug fixes, it is a patch version upgrade. For more information about this, see [Semantic Versioning 2.0.0](https://semver.org/).

Kubernetes clusters can upgrade the Kubernetes components while in operation. To this end, each Kubernetes component determines whether to support features based on the version difference. In minor versions, for example, the difference of one version supports the Kubernetes component upgrade for the operating clusters by supporting mutual feature compatibility. It also defines the upgrade sequence for each component type. For more information, see [Version Skew Policy](https://kubernetes.io/releases/version-skew-policy/).

<br>

<a id="cluster-upgrade-manage-nks-cluster-version"></a>
#### Manage NKS Cluster Version
NKS clusters manage the Kubernetes version and platform version for each cluster control plane and worker node group. The Kubernetes version and platform version differ as follows.

##### Kubernetes Version
* This is the version defined by upstream Kubernetes.
* It determines the version of the major Kubernetes components that make up an NKS cluster.
* The major components affected by the Kubernetes version are as follows:
    * kube-apiserver
    * kube-controller-manager
    * kube-scheduler
    * kubelet
    * kube-proxy

##### Platform Version
* This is the version defined at the NKS service level.
* It defines and manages the various components that make up an NKS cluster as a single version.
* The major components affected by the platform version are as follows:
    * Major control plane and worker node components such as containerd and etcd
    * Various system components and system management tools

<br>

The upgrade targets based on the status of the cluster's Kubernetes version and platform version are as follows:

| Kubernetes Version Status | Platform Version Status | Upgrade Target |
| --- | --- | --- |
| Not latest | Not latest | Kubernetes version and platform version |
| Not latest | Latest | Kubernetes version |
| Latest | Not latest | Platform version |
| Latest | Latest | None |

The Kubernetes version and platform version of the control plane can be checked on the cluster query screen, and the Kubernetes version and platform version of each worker node group can be checked on the respective worker node group query screen.

<br>

<a id="cluster-upgrade-upgrade-rules"></a>
#### Upgrade Rules
Based on the NKS cluster version management method and the Kubernetes version skew support policy, components must be upgraded in the correct order. The rules that apply to the NKS cluster upgrade feature are as follows:

* You must run the upgrade command for the control plane and each worker node group separately.
* The Kubernetes versions of the control plane and all worker node groups must match for an upgrade to be possible.
* The control plane must be upgraded before worker node groups can be upgraded.
* The Kubernetes version can be upgraded to the next version of the current Kubernetes version (minor version +1).
* The platform version can be upgraded to the latest version provided by the NKS service.
* Downgrading is not supported for either the Kubernetes version or the platform version.
* Upgrading is not possible when the cluster is being updated due to another operation.
* When upgrading the Kubernetes version from v1.25.4 to v1.26.3, if the CNI is Flannel, it must be changed to Calico-VXLAN.
* Clusters without NKS Registry enabled cannot be upgraded.

The following example shows a table of whether upgrades are possible during the Kubernetes version upgrade process. The conditions used in the example are as follows:

* List of Kubernetes versions supported by NHN Cloud: v1.31.4, v1.32.3, v1.33.4
* Cluster was created with v1.31.4

| Status | Control Plane Version | Control Plane Upgradeable | Worker Node Group Version | Worker Node Group Upgradeable |
| --- | :-: | :-: | :-: | :-: |
| Initial state | v1.31.4 | Yes <sup>[1](#footnote_cluster_upgrade_rule_1)</sup> | v1.31.4 | No <sup>[2](#footnote_cluster_upgrade_rule_2)</sup> |
| After control plane upgrade | v1.32.3 | No <sup>[3](#footnote_cluster_upgrade_rule_3)</sup> | v1.31.4 | Yes <sup>[4](#footnote_cluster_upgrade_rule_4)</sup> |
| After worker node group upgrade | v1.32.3 | Yes <sup>[1](#footnote_cluster_upgrade_rule_1)</sup> | v1.32.3 | No <sup>[2](#footnote_cluster_upgrade_rule_2)</sup> |
| After control plane upgrade | v1.33.4 | No <sup>[3](#footnote_cluster_upgrade_rule_3)</sup> | v1.32.3 | Yes <sup>[4](#footnote_cluster_upgrade_rule_4)</sup> |
| After worker node group upgrade | v1.33.4 | No <sup>[5](#footnote_cluster_upgrade_rule_5)</sup> | v1.33.4 | No <sup>[2](#footnote_cluster_upgrade_rule_2)</sup> |

Notes

* <a name="footnote_cluster_upgrade_rule_1">1</a>: Upgrade is possible because the versions of the control plane and all worker node groups match.
* <a name="footnote_cluster_upgrade_rule_2">2</a>: Worker node groups can be upgraded only after the control plane has been upgraded.
* <a name="footnote_cluster_upgrade_rule_3">3</a>: Upgrade requires the versions of the control plane and all worker node groups to match.
* <a name="footnote_cluster_upgrade_rule_4">4</a>: Upgrade is possible because the control plane has been upgraded.
* <a name="footnote_cluster_upgrade_rule_5">5</a>: Upgrade is not possible because the latest version supported by NHN Cloud is already in use.

<br>

<a id="cluster-upgrade-considerations-for-etcd-version-changes"></a>
#### Considerations for etcd Version Changes
When performing a cluster upgrade, an etcd upgrade is carried out together only when the [etcd version](/Container/NKS/en/user-guide/#platform-version-etcd-version) defined in the target platform version differs from the current cluster's etcd version. Before starting the upgrade, make sure you are aware of the following considerations, and we recommend that you take measures such as notifying users in advance and securing a maintenance window.

##### Avoid Frequent Resource Changes During the Upgrade to Ensure Data Consistency
If resource deployment or deletion operations occur frequently during an etcd upgrade, data consistency verification may fail and the upgrade may fail. For a safe upgrade, we recommend that you perform the upgrade under the following conditions:
* Perform the upgrade during a time period with low cluster resource change activity.
* Perform the upgrade during a time period with minimal operational impact (such as a maintenance window).
* Avoid large-scale deployments, deletions, or batch job executions immediately before the upgrade, and proceed only after traffic has stabilized.

##### Temporary Cluster Outage During Automatic Recovery from etcd Upgrade Failure
If the etcd upgrade fails, an automatic recovery procedure is triggered to restore the cluster to its previous state. During this procedure, cluster operations (Kubernetes API responses) may be temporarily suspended. Workloads (pods) that are already running are not affected, but kubectl calls may be temporarily delayed, and new resource creation or modification operations may be briefly suspended.

<br>

<a id="cluster-upgrade-upgrade-strategy"></a>
#### Upgrade Strategy
NKS clusters provide two upgrade strategies: Rolling Upgrade and Blue/Green Upgrade. You can select the appropriate strategy according to your operational policy to upgrade the cluster.

<br>

**Rolling Upgrade**

![Rolling_Upgrade.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/Rolling_Upgrade.png)

Rolling Upgrade is an upgrade strategy that upgrades the control plane and worker node groups sequentially to transition the entire cluster to a new version. The following describes the cluster upgrade process and each step using the Rolling Upgrade strategy.

<br>

##### 1. Upgrade the control plane components using the upgrade button on the cluster query screen.

The NKS cluster control plane guarantees high availability. Because the control plane is upgraded using a rolling update method, cluster availability is maintained. During this process, Kubernetes API calls may temporarily fail.

##### 2. Upgrade the worker components of all worker node groups using the upgrade button on the node group query screen.

Worker components can be upgraded for each worker node group. The worker component upgrade proceeds in the following order:

1. Disable the cluster autoscaler feature.<sup>[1](#footnote_worker_component_upgrade_1)</sup>
2. Add a buffer node<sup>[2](#footnote_worker_component_upgrade_2)</sup> to the worker node group.<sup>[3](#footnote_worker_component_upgrade_3)</sup>
3. Perform the following tasks sequentially for all worker nodes in the worker node group.<sup>[4](#footnote_worker_component_upgrade_4)</sup>
    1. Evict the pods running on the worker node and transition the node to an unschedulable state.
    2. Upgrade the worker components.
    3. Transition the node to a schedulable state.
4. Evict the pods running on the buffer node and delete the buffer node.
5. Re-enable the cluster autoscaler feature.<sup>[1](#footnote_worker_component_upgrade_1)</sup>

Notes

* <a name="footnote_worker_component_upgrade_1">1</a>: This step is only applicable if the cluster autoscaler feature is enabled before the upgrade starts.
* <a name="footnote_worker_component_upgrade_2">2</a>: A buffer node is an extra node that is created so that the pods evicted from existing worker nodes can be rescheduled during the upgrade process. It is created with the same scale as the worker node defined in that worker node group, and is automatically deleted when the upgrade process is over. This node is charged based on the instance fee policy.
* <a name="footnote_worker_component_upgrade_3">3</a>: You can define the number of buffer nodes during upgrade. The default value is 1, and buffer nodes are not added when set to 0. Minimum value of 0, maximum value of (maximum number of nodes per node group - the current number of nodes for the worker node group).
* <a name="footnote_worker_component_upgrade_4">4</a>: Tasks are performed in batches equal to the maximum number of unavailable nodes set at upgrade time. The default value is 1. The minimum value is 1 and the maximum value is the current number of nodes in the worker node group.

The following may occur during this process:

* Pods in service are evicted and rescheduled to other nodes (for more details on pod eviction, refer to the pod eviction considerations below).
* The autoscaler feature does not operate.


> [Pod Eviction Considerations]
> 1. Pods managed by a daemonset controller are not evicted.
> Because a daemonset controller runs a pod on each worker node, pods run by a daemonset controller cannot run on another node even if evicted. Pods run by a daemonset controller are not evicted during the worker node group upgrade process.
> 2. Pods that use local storage lose their data when evicted.
> Pods that use the node's local storage via `emptyDir` lose the data they were using when evicted, because locally stored data on a node cannot be moved to another node.
> 3. Pods that cannot be copied to another node will not be relocated to another node.
> If the pods run by controllers such as (ReplicationController), (ReplicaSet), (Job), (Daemonset), and (StatefulSet) are evicted, they will be rescheduled to another node by the controller. However, the pods not run by these controllers will not be scheduled to another node after being evicted.
> 4. Eviction can fail or slow down due to the PodDisruptionBudgets (PDB) setting.
> You can define the number of pods to maintain using the PodDisruptionBudgets (PDB) setting. Depending on how this setting is configured, it may not be possible to evict pods, or evicting pods may take longer than normal during the upgrade. If pod eviction fails, upgrade fails as well. If PDB is enabled, the appropriate PDB setting ensures proper pod eviction. To find out more about the PDB setting, see [here](https://kubernetes.io/docs/tasks/run-application/configure-pdb/).


For a more detailed explanation of safe pod eviction, see [Safely Drain a Node](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/).


<br>

**Blue/Green Upgrade**

![Blue_Green.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/Blue_Green.png)

Blue/Green Upgrade is an upgrade strategy that configures two different version environments within the cluster during the upgrade process, increasing application availability and simplifying the rollback process in the event of an upgrade failure to reduce upgrade risk. One environment (Blue) consists of the node group running the pre-upgrade version, and the other environment (Green) consists of the node group running the version to be upgraded to. When testing in the Green environment is complete, application traffic is moved to the Green environment and the Blue environment is decommissioned. Through this process, the entire cluster can be upgraded to the next version. The following describes the cluster upgrade process and each step using the Blue/Green Upgrade strategy.

<br>

##### 1. Upgrade the control plane components using the upgrade button on the cluster query screen.
The NKS cluster control plane guarantees high availability. Because the control plane is upgraded using a rolling update method, cluster availability is maintained. During this process, Kubernetes API calls may temporarily fail.

##### 2. Create a node group.
Create a new node group to set up the Green environment for testing. New node groups created after the control plane component upgrade are created with the same Kubernetes version as the control plane. You can deploy the same resources as the Blue environment (existing node group) to the Green environment to validate the post-upgrade environment. At this time, you must separate application traffic so that the Blue environment does not affect the operation of the existing cluster.

##### 3. Validate the Green environment (new node group) and switch application traffic to the Green environment.
Verify that the resources that existing users have been operating are compatible with the next version of Kubernetes in the newly built Green environment. Once validation is complete, switch application traffic from the existing Blue environment to the newly built Green environment. If a problem occurs during the validation phase in the Green environment, you can easily roll back by deleting the Green environment without switching traffic.

##### 4. Decommission the Blue environment (all previous-version worker node groups).
When all resources in the Blue environment are decommissioned, the versions of the control plane and all worker node groups will all match.

<a id="api-endpoint-ipacl"></a>
### Apply IP Access Control to Cluster API Endpoints { #api-endpoint-ipacl }
You can enforce or disable IP access control to cluster API endpoints.
For more information about the IP access control feature, see [IP Access Control](/Network/Load%20Balancer/en/overview/#ip).

<a id="api-endpoint-ipacl-ip-access-control-rules"></a>
#### IP Access Control Rules
The following rules apply when adding IP access control targets for a cluster API endpoint.

* When the IP access control type is set to **Allow**, the cluster default subnet CIDR is automatically added to the access control targets.
* When the IP access control type is set to **Allow**, the Dashboard, Namespace, Workload, Service & Network, Storage, Configuration, and Events tabs in the NKS console are disabled.
* When the IP access control type is set to **Block**, requests are denied if an IP band that overlaps the cluster default subnet CIDR band is in the access control target list.
* The maximum number of IP access control targets that can be configured is 100.
* At least one IP access control target must exist.

<a id="rotate-certificate"></a>
### Renew Cluster Certificates { #rotate-certificate }
Kubernetes requires PKI certificates for TLS authentication between components. For more information about PKI certificates, see [PKI Certificates and Requirements](https://kubernetes.io/docs/setup/best-practices/certificates/). When you create an NKS cluster, the required certificates are generated automatically, and the default validity period for these certificates is set to 5 years.

If the certificate expires, key components of the cluster, such as the API server, Controller Manager, etcd, will stop working and the cluster will be unavailable.
Before a certificate expires, you can use the Renew Certificate feature to renew its validity. You can find the certificate validity for your cluster and the Renew Certificate button on the Cluster View screen > **Basic Information** > **Kubernetes Certificates**.

To use the certificate renewal feature, follow the steps below:
1. Click **Renew Certificate**.
2. Select the renewal period.
    * You can set the certificate validity for up to 5 years.
    * Renewal is only available in 1-year increments.
3. Click **OK** to proceed with the certificate renewal.
4. Check the status of the target cluster.
    * The status of a cluster undergoing certificate renewal is `UPDATE_IN_PROGRESS`. When the task completes successfully, the status changes to `UPDATE_COMPLETE`.
    * When there is a problem with the change, the status is converted to `UPDATE_FAILED`, and it is not allowed to change cluster configuration until normalization completes.
        * To normalize the cluster status, perform the certificate renewal again.
5. On the Cluster View screen, verify that the certificate expiration date has been successfully renewed.
6. Download a new kubeconfig file.
    * The kubeconfig file used to access the cluster contains the certificate.
    * Once the certificate is renewed, you can no longer access the cluster using the previously used kubeconfig.
7. Restart any pods that use the CA certificate.
    * The certificate renewal process does not include restarting user-created pods.
    * If pods with certificate configurations exist, they must be restarted to apply the renewed CA certificate.

> [Note]
> The certificate renewal feature is available for clusters using 1.24 and later versions of Calico-VXLAN CNI or Cilium CNI.


> [Caution]
> The certificate renewal feature involves a restart of the system components and any kube-system namespace pods initially deployed at cluster creation to reflect the new certificate generation and settings.
> As a result, the state of nodes in your cluster may temporarily change to Not Ready, or some components of your cluster may not function normally while certificate renewal is in progress.
> To minimize the impact of these operations, you should avoid performing tasks such as creating new Pods while certificate renewal is in progress.

<a id="k8s-component"></a>
### Kubernetes Component Configuration { #k8s-component }

You can configure various options for Kubernetes components. These options can be set when creating a cluster and can also be changed after the cluster is created.

The components and options supported for configuration by operation area are as follows. For a detailed description of each item, see the [Kubernetes official documentation](https://kubernetes.io/docs/).

> [Caution]
> * If you change the configuration of a component running on the control plane, the control plane components are restarted.
> * If you change the configuration of a component running on a worker node, the components on that worker node are restarted.
> * The configuration of components running on worker nodes can be set per worker node group. (Only available for platform version 1.202602.0 and later)

<a id="control-plain-options"></a>
### Control Plane Options { #control-plain-options }

| Component | Option | Description |
| --- | --- | --- |
| kube-apiserver | default-not-ready-toleration-seconds | Defines how long pods running on a node are tolerated when the node is in a NotReady state.<br>(Unit: seconds, Default: 300, Min: 0, Max: 86400) |
| kube-apiserver | default-unreachable-toleration-seconds | Defines how long pods running on a node are tolerated when the node is not connected to the network.<br>(Unit: seconds, Default: 300, Min: 0, Max: 86400) |
| kube-controller-manager | node-monitor-grace-period | Defines the amount of time to wait before marking a node as unhealthy when it is in an abnormal state.<br>(Unit: seconds, Default: 40, Min: 0, Max: 86400) |
| kube-controller-manager | unhealthy-zone-threshold | Defines the threshold for the ratio of NotReady nodes at which a zone is considered unhealthy.<br>(Unit: percentage, Default: 55, Min: 0, Max: 100) |

<a id="worker-node-options"></a>
### Worker Node Options { #worker-node-options }

| Component | Option | Description |
| --- | --- | --- |
| kubelet | node-status-update-frequency | Defines how often kubelet reports the node status.<br>(Unit: seconds, Default: 10, Min: 0, Max: 86400) |
| kubelet | max-pods | Defines the maximum number of pods that can run on a node.<br>(Default: 110, Min: 1, Max: the maximum number of pod IPs that can be created, calculated based on the pod network and subnet size settings)<br>Supported in platform version 1.202602.0 and later. |

<a id="k8s-label"></a>
### Kubernetes Label Configuration { #k8s-label }
You can use the Kubernetes label setting for each node group. When this feature is enabled, the user-defined labels are automatically applied to the nodes when they are created. Labels are key-value pairs attached to Kubernetes objects such as pods and nodes, and are used to identify characteristics of those objects. For a detailed description of labels, see [Labels and Selectors](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/).

Kubernetes labels consist of key-value pairs, and valid label keys and values must each conform to the following rules.

<a id="k8s-label-label-key"></a>
#### Label Keys
A label key can have a prefix and a name separated by a slash (/), and the prefix is optional.

* Prefix
    * Must be 253 characters or fewer.
    * Must be in DNS subdomain format.
    * Predefined prefixes cannot be used.
        * ["kubernetes.io", "k8s.io", "magnum.openstack.org"]
* Name
    * Must be 63 characters or fewer.
    * Only alphanumeric characters (both uppercase and lowercase), hyphens (-), underscores (_), and dots (.) are allowed. The name must begin and end with an alphanumeric character.


<a id="k8s-label-label-value"></a>
#### Label Values
* Must be empty or 63 characters or fewer.
* Only alphanumeric characters (both uppercase and lowercase), hyphens (-), underscores (_), and dots (.) are allowed. The value must begin and end with an alphanumeric character.

> [Note]
> * You can specify up to 20 Kubernetes labels.
> * When you change the Kubernetes label configuration, the updated settings are applied starting from newly created nodes.

<a id="oidc-auth"></a>
### OIDC Authentication Configuration { #oidc-auth }

OIDC (OpenID Connect) is an interoperable authentication protocol based on the OAuth 2.0 framework. With OIDC, you can authenticate users through an external authentication service. For detailed information on how OIDC works, see [What is OpenID Connect](https://openid.net/developers/how-connect-works/).

NKS clusters can be configured to handle authentication using OIDC. The configuration items related to OIDC authentication are as follows.

| Item | Required | Description |
| --- | --- | --- |
| Issuer URL | O | OIDC provider URL starting with 'https://' |
| Client ID | O | Client ID of the OIDC provider |
| Username claim | X | The claim to use as the username. Default: 'sub'<br>For claims other than email, the provider URL is prepended as a prefix. |
| Groups claim | X | The claim to use as groups |
| Username prefix | X | A prefix to prepend to the username claim to prevent conflicts.<br>If not set, username claims other than email are prefixed with the provider URL.<br>Enter '-' to use no prefix. |
| Groups prefix | X | A prefix to prepend to the groups claim to prevent conflicts |
| Required claim | X | Key/value pairs that must be present in the ID token |
| CA File | X | Certificate file of the CA that signed the OIDC provider's web certificate |
| Signing Algs | X | List of allowed JOSE asymmetric signing algorithms. Default: 'RS256' |

<a id="control-plane-k8s-log"></a>
### Store Control Plane Kubernetes Component Logs { #control-plane-k8s-log }
NHN Kubernetes Service (NKS) provides logs for key Kubernetes components running on the control plane. These logs help you better understand various events and operations occurring within the cluster, and can be useful for diagnosing service status and troubleshooting issues.

The control plane Kubernetes component log storage feature has the following characteristics:

* You can deliver logs to one of two services: Log & Crash Search or Object Storage.
* The log level for delivery is fixed at `INFO`.
* The Kubernetes components that provide logs are as follows:
    * kube-apiserver
    * kube-scheduler
    * kube-controller-manager


> [Note]
> You can only set one log delivery destination. If you want to manage logs in both Log & Crash Search and Object Storage, you can first set the delivery destination to Log & Crash Search, and then use the "Store logs offsite" feature to store additional logs in Object Storage.
> You can also deliver to Log & Crash Search or Object Storage in other projects.

<a id="control-plane-k8s-log-lncs"></a>
#### Deliver to Log & Crash Search

<a id="control-plane-k8s-log-lncs-forward"></a>
##### Log Delivery Interval
Logs are delivered after the user-specified delivery interval has elapsed from the time the log is generated. The delivery interval can be set between 1 and 60 minutes.

> [Note]
> If the log size exceeds 300 KB before the delivery interval has elapsed, the logs are immediately delivered to Log & Crash Search.

<a id="control-plane-k8s-log-lncs-labels"></a>
##### Log & Crash Search Label Information
The label information set when delivering logs to Log & Crash Search is as follows:

| Label | Description |
| --- | --- |
| logType | Fixed value: "log" |
| logSource | Fixed value: "NKS" |
| logLevel | Fixed value: "INFO" |
| logVersion | Fixed value: "v2" |
| projectVersion | Fixed value: "1.0.0" |
| host | Master node name |
| cluster_uuid | Cluster UUID |
| cluster_name | Cluster name |
| nks_version | Cluster version |
| component | Component name |

> [Note]
> The four labels cluster_uuid, cluster_name, nks_version, and component are not included in the default fields when viewing logs in the Log & Crash Search console.
> You can add them manually by adding labels in the Selected fields section.

<a id="control-plane-k8s-log-obs"></a>
#### Deliver to Object Storage

<a id="control-plane-k8s-log-obs-forward"></a>
##### Log Delivery Interval
Logs are collected and delivered at each user-specified delivery interval. The delivery interval can be set between 1 and 60 minutes.

> [Note]
> If the file size stored in Object Storage exceeds 300 KB, it is partitioned.
> Log files are delivered as soon as they exceed 300 KB.
> 400 KB or less: Stored as a single file with the _index0 suffix
> Over 400 KB: Split into multiple files suffixed with _index1, _index2, etc.

<a id="control-plane-k8s-log-obs-compression"></a>
##### File Compression
You can choose whether to compress and store files in gzip format when saving them to the storage.

<a id="control-plane-k8s-log-obs-authorization"></a>
##### Grant Storage Access Permissions
On the NKS page of the console, click **NKS System Account Information** to display the tenant ID and user ID used by NKS. If you set the control plane log store type to Object Storage (OBS), this NKS system account must be granted write permissions to that container. Otherwise, the NKS system account cannot write data to your OBS.

Configuration steps:

* Go to the NHN Cloud > Object Storage console.
* Select the container where you want to store the control plane logs.
* At the bottom, click **Basic Information** > **Change Access Policy Settings**.
* Under the role-based access policy, click **Use**.
* Enter the tenant ID and user ID from the NKS system account information you retrieved earlier, and grant Write permission.

> [Caution]
> If the Object Storage container is removed or Write permission is revoked from the container while control plane logs are being delivered, log delivery will fail.

<a id="control-plane-k8s-log-path"></a>
##### Control Plane Log Storage Path
The control plane log storage path is constructed based on the OBS endpoint, AUTH tenant, Container, and Path information, in the following format:

* {OBS_https_endpoint}/{AUTH_OBS_TENANT}/{Container}/{Path}

For example, if the configuration values are as follows:

* OBS https endpoint: https://kr1-api-object-storage.nhncloudservice.com/v1
* AUTH_OBS_TENANT: AUTH_e670167936434f85a03694184000ffe6
* Container: nks_log_container
* Desired storage path: example/my/folder

The actual control plane log storage path is as follows:

* https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_e670167936434f85a03694184000ffe6/nks_log_container/example/my/folder

> [Note]
> If the OBS endpoint, AUTH_tenant, or Container information set in obs_api_url does not exist, the configuration request will fail.

The actual logs are stored under the URL above in the following structure:

* ${User-configured OBS container name}/NKS/${Cluster UUID}/${Master node name}/${K8S component name}/${Year}/${Month}/${YearMonthDay-HourMinuteSecond}-index${index_count}.gz

For example, if the configuration values are as follows:

* Container: nks_log_container
* Cluster UUID: f31dd18f-4dab-49fa-97bb-8feba31cb30b
* Cluster name: nks-test
* Component: kube-apiserver
* Storage time: 2025-04-28 10:15:00

The path where logs are created in the OBS container is as follows:

* nks_log_container/NKS/f31dd18f-4dab-49fa-97bb-8feba31cb30b/
  nks-test-master-0/kube-apiserver/2025/04/20250428-101500-index0.gz

<a id="k8s-taint"></a>
### Kubernetes Taint Configuration { #k8s-taint }
You can use the Kubernetes taint configuration feature for each node group. Node groups created with this feature are initialized with the taints that you configured. For more information about taints, see [Taints and Tolerations](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/).

A Kubernetes taint consists of a key, a value, and an effect. Each element must follow the rules below.

<a id="k8s-taint-taint-key"></a>
#### Taint Key

A taint key can have a structure consisting of a prefix and a name separated by a slash (/). The prefix is optional.

* Prefix
    * Must be 253 characters or fewer.
    * Must be in DNS subdomain format.
    * Predefined prefixes cannot be used.
        * ["kubernetes.io", "k8s.io", "magnum.openstack.org"]
* Name
    * Must be 63 characters or fewer.
    * Only uppercase and lowercase letters, numbers, hyphens (-), underscores (_), and periods (.) are allowed. The name must begin and end with an alphanumeric character.

<a id="k8s-taint-taint-value"></a>
#### Taint Value

* Must be empty or 63 characters or fewer.
* Only uppercase and lowercase letters, numbers, hyphens (-), underscores (_), and periods (.) are allowed. The value must begin and end with an alphanumeric character.

<a id="k8s-taint-taint-effect"></a>
#### Taint Effect

You must specify one of the following three values:

* NoSchedule
    * Pods that do not tolerate the taint are not scheduled on the node.
    * Pods that are already running on the node are not affected.
* PreferNoSchedule
    * The scheduler avoids placing pods on the node if possible, but may schedule them there if no other option is available.
* NoExecute
    * Existing pods that do not tolerate the taint are immediately evicted from the node, and new pods are also not scheduled on it.

[Note]
* You can specify up to 30 Kubernetes taints per node group.
* If you change the Kubernetes taint configuration, the updated configuration is applied to newly created nodes going forward.

<a id="konnectivity-description"></a>
### konnectivity { #konnectivity-description }

Konnectivity is a component in Kubernetes that securely proxies network communication between the control plane (API server) and worker nodes. Previously, the API server had to access the kubelet or pods on a node directly, which made network configuration complex.

Konnectivity resolves this by consisting of two parts:
* Konnectivity Server: Exists on the control plane and forwards requests received from the API server to the Konnectivity Agent.
* Konnectivity Agent: Exists on worker nodes, forwards requests received from the Konnectivity Server to the target pod, and relays the response back to the Konnectivity Server.

The Konnectivity Server and Konnectivity Agent first establish a connection to create a tunnel, and the API server communicates with pods through this tunnel.

> [Caution]
> The resources listed below are related to the Konnectivity Agent. Modifying configurations or deleting these resources can have a critical impact on cluster operations.
> 
> | Kind | Namespace | Name |
> | --- | --- | --- |
> | ServiceAccount | kube-system | konnectivity-agent |
> | ClusterRoleBinding | kube-system | konnectivity-server-auth-delegator |
> | Deployment | kube-system | konnectivity-agent |

> [Note]
> Konnectivity is available on platform version 1.202605.0 or later.

<a id="worker-node-management"></a>
## Manage Worker Nodes { #worker-node-management }

<a id="container-management"></a>
### Manage Containers { #container-management }

<a id="container-management-clusters-of-kubernetes-v1243-or-older"></a>
#### Clusters of Kubernetes v1.24.3 or Older
Clusters of Kubernetes v1.24.3 or older use Docker to comprise the container runtime. In the worker node, you can use the docker CLI to view the container status and the container image. For more details and usage on the docker CLI, see [Use the Docker command line](https://docs.docker.com/engine/reference/commandline/cli/).

<a id="container-management-clusters-of-kubernetes-v1243-and-later"></a>
#### Clusters of Kubernetes v1.24.3 or Later

Clusters of Kubernetes v1.24.3 or later use containerd to comprise the container runtime. In the worker node, you can use nerdctl instead of the docker CLI to view the container status and the container image. For more details and usage on nerdctl, see [nerdctl: Docker-compatible CLI for containerd](https://github.com/containerd/nerdctl#nerdctl-docker-compatible-cli-for-containerd).

<a id="network-management"></a>
### Network Management { #network-management }

<a id="network-management-default-network-interface"></a>
#### Default Network Interface
Every worker node has a network interface that connects to the VPC/subnet entered when creating the cluster. This default network interface is named "eth0", and worker nodes connect to the control plane through this network interface.

<a id="network-management-additional-network-interface"></a>
#### Additional Network Interface
If you set additional networks when creating a cluster or a worker node group, additional network interfaces are created on the worker nodes in that group. Additional network interfaces are named eth1, eth2, etc., in the order they appear in the additional network settings.

<a id="network-management-default-route-settings"></a>
#### Default Route Settings
If multiple network interfaces exist on a worker node, a default route is set for each network interface. If multiple default routes are set on a system, the default route with the lowest metric value acts as the system default route. Default routes per network interface have lower metric values for smaller interface numbers. This causes the smallest numbered network interface among active network interfaces to act as the system default route.

To set the system default route to an additional network interface, the following tasks are required.

##### 1. Change the Metric Settings for Each Network Interface
All network interfaces of a worker node are assigned an IP address through a DHCP server. Set the default route for each network interface when an IP address is assigned from a DHCP server. Currently, the metric value for each default route is preset for each interface. The storage location and settings for each Linux distribution are as follows.

* CentOS
    * Configuration file location: /etc/sysconfig/network-scripts/ifcfg-{network interface name}
    * Metric value setting: METRIC
* Ubuntu
    * Configuration file location: /etc/systemd/network/toastcloud-{network interface name}.network
    * Metric value setting: RouteMetric in the DHCP section

> [Note]
> The metric value for each default route is determined when the default route is set.
> Therefore, the changed settings will take effect at the time of setting the next default route.
> To change the metric value for each route currently applied to the system, refer to `Change Metric Value for the Current Route` below.

##### 2. Change Metric Value for the Current Route

To change the system default route, you can adjust the metric value of the default route for each network interface. The following is an example of adjusting the metric value of each default route using the route command.

Below is the state before running the task. You can find that the smaller interface number has the lower metric value.
```
# route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.0.0.1        0.0.0.0         UG    0      0        0 eth0
0.0.0.0         192.168.0.1     0.0.0.0         UG    100    0        0 eth1
0.0.0.0         172.16.0.1      0.0.0.0         UG    200    0        0 eth2
...
```

To set eth1 as the system default route, change the metric value for eth1 to 0 and the metric value for eth0 to 100. Since you can't just change the metric value, you must delete the route and add it again. First, delete the route of eth0 and set the metric value for eth0 to 100.

```
# route del -net 0.0.0.0/0 dev eth0
# route add -net 0.0.0.0/0 gw 10.0.0.1 dev eth0 metric 100
```

For eth1, also delete the existing route first, then set the metric value for eth1 to 0.
```
# route del -net 0.0.0.0/0 dev eth1
# route add -net 0.0.0.0/0 gw 192.168.0.1 dev eth1 metric 0
```

If you query the routes again, you can confirm that the metric values have been changed.
```
# route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.0.1     0.0.0.0         UG    0      0        0 eth1
0.0.0.0         10.0.0.1        0.0.0.0         UG    100    0        0 eth0
0.0.0.0         172.16.0.1      0.0.0.0         UG    200    0        0 eth2
...
```

<a id="network-management-change-default-route-settings-using-user-script"></a>
#### Change Default Route Settings Using the User Script Feature
If you use the User Script feature, the above settings can be maintained even when a node is newly initialized due to node scale-out. The following user script is an example of setting the metric value of eth0 to 100 and the metric value of eth1 to 0 on a worker node using CentOS. This also changes the metric value for default route currently applied to the system, which persist after the worker node restarts.
```
#!/bin/bash
sed -i -e 's|^METRIC=.*$|METRIC=100|g' /etc/sysconfig/network-scripts/ifcfg-eth0
sed -i -e 's|^METRIC=.*$|METRIC=0|g' /etc/sysconfig/network-scripts/ifcfg-eth1
route del -net 0.0.0.0/0 dev eth0
route add -net 0.0.0.0/0 gw 10.0.0.1 dev eth0 metric 100
route del -net 0.0.0.0/0 dev eth1
route add -net 0.0.0.0/0 gw 192.168.0.1 dev eth1 metric 0
```

<a id="kubelet-argument"></a>
### Kubelet Custom Arguments Setting Feature { #kubelet-argument }
A kubelet is a node agent that runs on all worker nodes. The kubelet receives input for many settings using command-line arguments. The kubelet custom arguments setting feature provided by NKS allows you to add arguments that are input at kubelet startup. You can set up kubelet custom arguments and apply them to your system as follows.

* Enter the custom arguments in the `/etc/kubernetes/kubelet-user-args` file on the worker node in the format `KUBELET_USER_ARGS="custom arguments"`.
* Run the `systemctl daemon-reload` command.
* Run the `systemctl restart kubelet` command.
* Run the `systemctl status kubelet` command to verify that kubelet is working properly.

> [Caution]
> * This feature only works on clusters created after November 28, 2023.
> * Perform this task for each worker node on which you want to set custom arguments.
> * If you enter custom arguments in an incorrect format, kubelet will not work properly.
> * The configured custom arguments are applied even after a system restart.

<a id="containerd-registry-config"></a>
### Custom containerd Registry Settings Feature (deprecated) { #containerd-registry-config }

> [Caution]
> This feature does not work with Kubernetes v1.34 or later.
> For Kubernetes v1.34 or later, which uses containerd 2.2, you can apply per-registry settings by using the hosts.toml file.
> For more information, see [Registry Configuration](https://github.com/containerd/containerd/blob/main/docs/hosts.md).

NKS clusters with version 1.24.3 or later use containerd v1.6 as the container runtime. NKS provides a feature that allows you to configure registry-related settings among the various containerd settings to suit your environment. For registry settings for containerd v1.6, see [Configure Image Registry](https://github.com/containerd/containerd/blob/release/1.6/docs/cri/registry.md).

During the worker node initialization process, if the custom containerd registry settings file (`/etc/containerd/registry-config.json`) exists, its contents are applied to the containerd configuration file (`/etc/containerd/config.toml`). If the custom containerd registry settings file does not exist, the default registry settings are applied to the containerd configuration file. The default registry settings are as follows:

```json
[
   {
      "registry": "docker.io",
      "endpoint_list": [
         "https://registry-1.docker.io"
      ]
   }
]
```

The key/value format that can be configured for a single registry is as follows:

```json
{
  "registry": "REGISTRY_NAME",
  "endpoint_list": [
     "ENDPOINT1",
     "ENDPOINT2"
  ],
  "tls": {
     "ca_file": "CA_FILEPATH",
     "cert_file": "CERT_FILEPATH",
     "key_file": "KEY_FILEPATH",
     "insecure_skip_verify": true_or_false
  },
  "auth": {
     "username": "USERNAME",
     "password": "PASSWORD",
     "auth": "AUTH",
     "identitytoken": "IDENTITYTOKEN"
  }
}
```

<a id="containerd-registry-config-example-1"></a>
#### Example 1

To register additional registries other than `docker.io`, you can configure the settings as follows:

```json
[
   {
      "registry": "docker.io",
      "endpoint_list": [
         "https://registry-1.docker.io"
      ]
   },
   {
      "registry": "additional.registry.io",
      "endpoint_list": [
         "https://additional.registry.io"
      ]
   }
]
```

<a id="containerd-registry-config-example-2"></a>
#### Example 2

To remove the `docker.io` registry and register only registries that support HTTP, you can configure the settings as follows:
```json
[
   {
      "registry": "user-defined.registry.io",
      "endpoint_list": [
         "http://user-defined.registry.io"
      ],
      "tls": {
         "insecure_skip_verify": true
      }
   }
]
```

<a id="containerd-registry-config-example-3"></a>
#### Example 3

To generate a custom containerd registry configuration file with the contents of Example 2 upon node creation, you can set up a user script as follows:

```bash
mkdir -p /etc/containerd
echo '[ { "registry": "user-defined.registry.io", "endpoint_list": [ "http://user-defined.registry.io" ], "tls": { "insecure_skip_verify": true } } ]' > /etc/containerd/registry-config.json
```

> [Caution]
> * The containerd configuration file (`/etc/containerd/config.toml`) is a file that is managed by NKS. Arbitrary modifications to this file can cause errors in the behavior of NKS features or remove the modifications.
> * If an invalid registry is configured using the custom containerd registry settings feature, worker nodes may behave abnormally.
> * The point at which the custom containerd registry settings feature is applied to the containerd configuration file is during worker node initialization. The worker node initialization process is part of the worker node creation process and the worker node group upgrade process.
>     * To apply the custom container registry settings feature when creating a worker node, the user script must be configured to create this settings file.
>     * To apply the custom container registry settings feature when upgrading a group of worker nodes, you must manually set this file on all worker nodes before proceeding with the upgrade.
> * If the custom containerd registry settings file exists, its contents are applied to containerd as-is.
>     * To use the `docker.io` registry, you must also include settings for the `docker.io` registry. For settings for the `docker.io` registry, see Default registry settings.
>     * If you do not want to use the `docker.io` registry, you can simply not include any settings for the `docker.io` registry. However, at least one registry setting must exist.

<a id="constraints-on-cgroup"></a>
### Constraints Based on Kubernetes Version and CGroup Version { #constraints-on-cgroup }
CGroup (Control Group) is a Linux kernel feature that allows you to limit, isolate, and monitor the usage of system resources such as CPU, memory, disk I/O, and network for groups of processes. It is one of the core foundations of container technologies, including Kubernetes. CGroup started with version 1 (v1) and evolved to version 2 (v2) with enhanced memory and I/O control capabilities. Because it is a Linux kernel feature, CGroup v2 has a dependency on the Linux kernel. Therefore, CGroup v2 is only supported on relatively recent distributions and versions.

Starting with NKS cluster v1.34, worker nodes must operate with CGroup v2. This is a constraint introduced by the Kubernetes community, indicating that containerd 2.x will be used instead of containerd 1.x going forward, and that the system will operate based on CGroup v2 instead of v1.

Worker nodes in NKS operate with CGroup v2 in the following cases:
* Creating a node group using an OS image configured with CGroup v2
* Creating a node group using an OS image configured with CGroup v1, then performing a rolling upgrade to v1.34

You can check the default CGroup version based on the release date of the OS image:
* Images released before March 10, 2026: CGroup v1
* Images released after March 10, 2026: CGroup v2

Even if the default CGroup version of an OS image is v1, you can change the setting to v2. For node groups created using an OS image whose default CGroup version is v1, the CGroup version of the worker nodes is changed from v1 to v2 in the following cases:
* When performing a rolling upgrade to Kubernetes v1.34
* When scaling out worker nodes after performing a rolling upgrade to Kubernetes v1.34

The OS image distributions and versions that support changing the CGroup version from v1 to v2 are as follows:
* Ubuntu 22.04 or later
* Rocky 9.0 or later

> [Caution]
> * The process of changing the CGroup setting of a worker node from v1 to v2 includes a **reboot of the worker node**.
> * If node rebooting is not possible due to unauthorized changes to grub.conf or similar issues, not only will the CGroup version change fail, but the instance may also fail to boot.
> * You must proceed with the Kubernetes version upgrade of the node group only when there are no issues with rebooting the instance.

Node groups created using an OS image whose default CGroup version is v1 and that cannot be changed to v2 cannot be upgraded to Kubernetes v1.34 using the rolling upgrade method. In this case, you can upgrade the node group using the Blue-Green method.

<a id="worker-management-caution"></a>
### Worker Node Management Precautions { #worker-management-caution }
* You must not arbitrarily delete container images that have been pulled to worker nodes. Pods required by the NKS cluster may fail to run.
* If you forcibly stop the system using commands such as `shutdown`, `halt`, or `poweroff`, you will not be able to restart it from the console. Use the worker node start/stop feature instead.
* You must not modify various configuration files within the worker node or manipulate system services. Critical issues may occur in the NKS cluster.

<a id="cni"></a>
## CNI (Container Network Interface) { #cni }
NHN Kubernetes Service (NKS) provides different types of Container Network Interface (CNI) through the Addon feature. When creating a cluster, you can select one of the following CNIs: Calico-VXLAN, Calico-eBPF, or Cilium. The default setting is Calico-VXLAN. Calico-eBPF utilizes the BGP routing protocol for container workloads and leverages eBPF technology for direct communication, while certain segments—such as NodePort—rely on VXLAN. For more information about Calico's eBPF, see [about eBPF](https://docs.tigera.io/calico/latest/about/kubernetes-training/about-ebpf). Cilium is based on a VXLAN overlay network and uses eBPF technology to provide high network performance. For more information about Cilium's eBPF, see [eBPF Datapath](https://docs.cilium.io/ko/stable/network/ebpf/).

The OS constraints that can be selected per CNI are as follows:

| CNI | Available OS |
| :-: | :-: |
| Flannel | CentOS, Rocky, Red Hat, Ubuntu |
| Calico-VXLAN | CentOS, Rocky, Red Hat, Ubuntu |
| Calico-eBPF | Rocky, Ubuntu |
| Cilium | Rocky, Ubuntu |

<a id="calico-cni-types"></a>
### Calico CNI Types { #calico-cni-types }
The differences between Calico-VXLAN and Calico-eBPF provided by NHN Kubernetes Service (NKS) are as follows:

|  | Calico-VXLAN | Calico-eBPF |
| :-: | :-: | :-: |
| Container network processing module | Linux kernel network stack | eBPF + Linux kernel network stack |
| kube-proxy | Enabled | Disabled (eBPF replaces kube-proxy) |
| Network method | VXLAN | Direct communication |
| Pod-to-pod communication | Communicates via VXLAN encapsulation | Direct communication<sup>[1](#footnote_calico_1)</sup> |
| Service ClusterIP to pod communication | Communicates via VXLAN encapsulation | Direct communication |
| Service NodePort to pod communication | Communicates via VXLAN encapsulation | Communicates via VXLAN encapsulation |
| Network policy enforcement | iptables-based | eBPF-based (kernel level) |
| Network performance | Performance degradation due to VXLAN encapsulation | High performance due to direct communication (low latency) |

Notes

* <a name="footnote_calico_1">1</a>: The source IP and destination IP of packets are set to pod IPs. When using enhanced security rules, you must configure separate security rules for this traffic.

> [Caution]
> Clusters using Calico v3.24.1 in eBPF mode cannot create node groups that use Rocky 9.5 or later or Ubuntu 24.04 or later images.
> To use those images, you must update Calico to v3.28.2 or later through the addon management feature.

<a id="security-group"></a>
## Security Group { #security-group }
When you set Enhanced Security Rules to True during cluster creation, only required security rules are created when creating a worker node security group.

<a id="mandatory-sg-rules"></a>
### Cluster Worker Node Required Security Rules { #mandatory-sg-rules }

| Direction | IP Protocol | Port Range | Ether | Remote | Description | Notes |
| :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| ingress | TCP | 10250 | IPv4 | Worker node | kubelet port, direction: metrics-server(worker node) → kubelet(worker node) | |
| ingress | TCP | 10250 | IPv4 | NKS Control Plane | kubelet port, direction: kube-apiserver(NKS Control plane) → kubelet(worker node) | |
| ingress | TCP | 5473 | IPv4 | Worker node | calico-typha port, direction: calico-node(worker node) → calico-typha(worker node) | Created when CNI is Calico-VXLAN or Calico-eBPF |
| ingress | TCP | 179 | IPv4 | Worker node | calico-node BGP port, direction: pod(worker node) → pod(worker node) | Created when CNI is Calico-eBPF |
| ingress | TCP | 179 | IPv4 | NKS Control Plane | calico-node BGP port, direction: pod(NKS Control plane) → pod(worker node) | Created when CNI is Calico-eBPF |
| ingress | UDP | 8472 | IPv4 | Worker node | flannel vxlan overlay network port, direction: pod(worker node) → pod(worker node) | Created when CNI is flannel |
| ingress | UDP | 8472 | IPv4 | Worker node | flannel vxlan overlay network port, direction: pod(NKS Control plane) → pod(worker node) | Created when CNI is flannel |
| ingress | UDP | 4789 | IPv4 | Worker node | calico-node vxlan overlay network port, direction: pod(worker node) → pod(worker node) | Created when CNI is Calico-VXLAN or Calico-eBPF |
| ingress | UDP | 4789 | IPv4 | NKS Control Plane | calico-node vxlan overlay network port, direction: pod(NKS Control plane) → pod(worker node) | Created when CNI is Calico-VXLAN or Calico-eBPF |
| ingress | TCP | 4240 | IPv4 | Worker node | cilium-agent health check port, direction: cilium-agent(worker node) → cilium-agent(worker node) | Created when CNI is Cilium |
| ingress | ICMP | - | IPv4 | Worker node | cilium ping health monitoring, direction: cilium-agent(worker node) → worker node | Created when CNI is Cilium |
| ingress | UDP | 8472 | IPv4 | Worker node | cilium vxlan overlay network port, direction: pod(worker node) → pod(worker node) | Created when CNI is Cilium |
| ingress | UDP | 8472 | IPv4 | NKS Control Plane | cilium vxlan overlay network port, direction: pod(NKS Control plane) → pod(worker node) | Created when CNI is Cilium |
| egress | TCP | 2379 | IPv4 | NKS Control Plane | etcd port, direction: calico-kube-controller(worker node) → etcd(NKS Control plane) | |
| egress | TCP | 6443 | IPv4 | Kubernetes API endpoint | kube-apiserver port, direction: kubelet, kube-proxy(worker node) → kube-apiserver(NKS Control plane) | |
| egress | TCP | 6443 | IPv4 | NKS Control Plane | kube-apiserver port, direction: default kubernetes service(worker node) → kube-apiserver(NKS Control plane) | |
| egress | TCP | 5473 | IPv4 | Worker node | calico-typha port, direction: calico-node(worker node) → calico-typha(worker node) | Created when CNI is Calico-VXLAN or Calico-eBPF |
| egress | TCP | 53 | IPv4 | Worker node | DNS port, direction: worker node → external | |
| egress | TCP | 443 | IPv4 | Allow all | HTTPS port, direction: worker node → external | |
| egress | TCP | 80 | IPv4 | Allow all | HTTP port, direction: worker node → external | |
| egress | TCP | 179 | IPv4 | Worker node | calico-node BGP port, direction: pod(worker node) → pod(worker node) | Created when CNI is Calico-eBPF |
| egress | TCP | 179 | IPv4 | NKS Control Plane | calico-node BGP port, direction: pod(NKS Control plane) → pod(worker node) | Created when CNI is Calico-eBPF |
| egress | UDP | 8472 | IPv4 | Worker node | flannel vxlan overlay network port, direction: pod(worker node) → pod(worker node) | Created when CNI is flannel |
| egress | UDP | 8472 | IPv4 | NKS Control Plane | flannel vxlan overlay network port, direction: pod(worker node) → pod(NKS Control plane) | Created when CNI is flannel |
| egress | UDP | 4789 | IPv4 | Worker node | calico-node vxlan overlay network port, direction: pod(worker node) → pod(worker node) | Created when CNI is Calico-VXLAN or Calico-eBPF |
| egress | UDP | 4789 | IPv4 | NKS Control Plane | calico-node vxlan overlay network port, direction: pod(worker node) → pod(NKS Control plane) | Created when CNI is Calico-VXLAN or Calico-eBPF |
| egress | TCP | 4240 | IPv4 | Worker node | cilium-agent health check port, direction: cilium-agent(worker node) → cilium-agent(worker node) | Created when CNI is Cilium |
| egress | ICMP | - | IPv4 | Worker node | cilium ping health monitoring, direction: worker node → cilium-agent(worker node) | Created when CNI is Cilium |
| egress | UDP | 8472 | IPv4 | Worker node | cilium vxlan overlay network port, direction: pod(worker node) → pod(worker node) | Created when CNI is Cilium |
| egress | UDP | 8472 | IPv4 | NKS Control Plane | cilium vxlan overlay network port, direction: pod(worker node) → pod(NKS Control plane) | Created when CNI is Cilium |
| egress | UDP | 53 | IPv4 | Allow all | DNS port, direction: worker node → external | |

When using enhanced security rules, the NodePort type of service and the ports used by the NHN Cloud NAS service are not added to the security rules. You need to set the following security rules as needed.

| Direction | IP Protocol | Port Range | Ether | Remote | Description |
| :-: | :-: | :-: | :-: | :-: | :-: |
| ingress, egress | TCP | 30000 - 32767 | IPv4 | Allow all | NKS service object NodePort, direction: external → worker node |
| egress | TCP | 2049 | IPv4 | NHN Cloud NAS service IP address | RPC NFS port of csi-nfs-node, direction: csi-nfs-node(worker node) -> NHN Cloud NAS service |
| egress | TCP | 111 | IPv4 | NHN Cloud NAS service IP address | rpc portmapper port of csi-nfs-node, direction: csi-nfs-node(worker node) -> NHN Cloud NAS service |
| egress | TCP | 635 | IPv4 | NHN Cloud NAS service IP address | rpc mountd port of csi-nfs-node, direction: csi-nfs-node(worker node) -> NHN Cloud NAS service |

> [Caution — When Using Calico-eBPF CNI]
> When using Calico-eBPF CNI, communication between pods and from nodes to pods is handled through the ports configured on the pods.
> If you use enhanced security rules, you must manually add ingress and egress security rules for the relevant pod ports.

<a id="cilium-optional-security-group-rules"></a>
### Additional Security Group Rules for Cilium CNI Optional Features { #cilium-optional-security-group-rules }

To enable optional features such as Hubble, Envoy, and Prometheus in a cluster that uses Cilium CNI, you must configure the additional security group rules required for those features.

##### Required Ports by Optional Feature

| Feature | Direction | IP Protocol | Port Range | Remote | Description |
| :-: | :-: | :-: | :-: | :-: | :-: |
| Hubble Observability | ingress, egress | TCP | 4244 | Worker node | hubble server port, direction: hubble-relay(worker node) → hubble-server(worker node) |
| Hubble UI | ingress, egress | TCP | 4245 | Worker node | hubble relay port, direction: hubble-ui(worker node) → hubble-relay(worker node) |
| Cilium Agent Metrics | ingress, egress | TCP | 9962 | Worker node | cilium-agent prometheus metrics port |
| Cilium Operator Metrics | ingress, egress | TCP | 9963 | Worker node | cilium-operator prometheus metrics port |
| Cilium Envoy Metrics | ingress, egress | TCP | 9964 | Worker node | cilium-envoy prometheus metrics port |
| WireGuard Encryption | ingress, egress | UDP | 51871 | Worker node | WireGuard transparent encryption port |
| IPsec Encryption | ingress, egress | UDP | 500 | Worker node | IPsec IKE port |
| IPsec Encryption | ingress, egress | UDP | 4500 | Worker node | IPsec NAT-T port |
| IPsec Encryption | ingress, egress | ESP (50) | - | Worker node | IPsec ESP protocol |

> [Note]
> The optional features listed above are not included in the default Cilium installation.
> To use optional features, you must change the Cilium configuration and manually add the security group rules required for those features.

<a id="relaxd-sg-rules"></a>
### Rules Created When Not Using Enhanced Security Rules { #relaxd-sg-rules }

If you don't use enhanced security rules, additional security rules are created for services of type NodePort and for external network communication.

| Direction | IP Protocol | Port Range | Ether | Remote | Description |
| :-: | :-: | :-: | :-: | :-: | :-: |
| ingress | TCP | 1 - 65535 | IPv4 | Worker node | All ports, direction: worker node → worker node |
| ingress | TCP | 1 - 65535 | IPv4 | NKS Control Plane | All ports, direction: NKS Control plane → worker node |
| ingress | TCP | 30000 - 32767 | IPv4 | Allow all | NKS service object NodePort, direction: external → worker node |
| ingress | UDP | 1 - 65535 | IPv4 | Worker node | All ports, direction: worker node → worker node |
| ingress | UDP | 1 - 65535 | IPv4 | NKS Control Plane | All ports, direction: NKS Control plane → worker node |
| egress | Any | 1 - 65535 | IPv4 | Allow all | All ports, direction: worker node → external |
| egress | Any | 1 - 65535 | IPv6 | Allow all | All ports, direction: worker node → external |


<a id="addon-mgmt"></a>
## Add-on Management Feature { #addon-mgmt }
An add-on is a component that is not a required component of a Kubernetes cluster, but is provided to extend the functionality of an NKS cluster or to provide specialized functionality. Add-ons can include components that perform functions such as networking, service discovery, monitoring, storage provisioning, and more. Users can install/update/remove add-ons provided by NHN Cloud to the cluster through the add-on management feature.

> [Caution]
> Clusters without the NKS registry enabled cannot use the add-on management feature.

<a id="addon-mgmt-operation"></a>
### How It Works { #addon-mgmt-operation }
This section describes how the add-on management feature works.

<a id="addon-mgmt-operation-server-side-apply"></a>
#### Server-side apply
When installing/updating add-ons to a cluster using the add-on management feature, Kubernetes' Server-side apply is used. Client-side apply is where the client computes the resource state locally and sends the entire resource to the API server. Server-side apply, on the other hand, allows the API server to perform resource merging and field ownership management, allowing the API server to perform resource merging and conflict detection. For more information about server-side apply, see [Server-Side Apply](https://kubernetes.io/docs/reference/using-api/server-side-apply/).

<a id="addon-mgmt-operation-conflict-resolution-options"></a>
#### Conflict resolution options
Conflicts may occur during add-on installation or updates if users have modified fields that are managed by the add-on. Users can resolve these conflicts by selecting the appropriate conflict resolution option (resolve-conflicts) during installation or updates. The add-on management feature provides the following conflict resolution options.

* None: If a conflict occurs, the installation/update is not applied and the installation/update request is treated as failed.
* Overwrite: If a conflict occurs, the conflicting field is overwritten with the default value defined by the add-on.
* Preserve: If a conflict occurs, the conflicting field is preserved with its existing value.

> [Caution about version updates]
> When updating an add-on version, the default settings of required components may change. Conflicts may occur even if you have not directly modified the relevant fields, and selecting the 'None' or 'Preserve' conflict resolution option may cause the add-on installation/update to fail. You can prevent conflicts by selecting the 'Overwrite' conflict resolution option.

> [Caution about the Preserve option]
> You can't preserve all changes to the resources that make up an add-on.
> If a conflict occurs in a non-preservable field, the install/update operation will fail.

<a id="addon-mgmt-operation-main-features"></a>
#### Features
You can install, update, and remove add-ons from a cluster using the add-on management feature.

* Install
    * Installs an add-on to the cluster.
    * Specify the add-on version and per-add-on options during installation.
    * Specify a conflict resolution option during installation.
* Update
    * Updates an add-on that is installed in the cluster.
    * You can update the add-on version, per-add-on options, and more.
        * Depending on the add-on, changing options may not be possible.
    * Specify a conflict resolution option during the update.
* Remove
    * Removes all resources that make up the add-on from the cluster.
    * However, add-ons of the required type cannot be removed.

> [Note]
> Upgrades of components such as CNI and CoreDNS are no longer provided through the Kubernetes version upgrade feature.
> Instead, you can update the version of each add-on using the add-on update feature.

<a id="addon-mgmt-operation-enable-add-on-management"></a>
#### Enable the add-on management feature
Existing clusters without the add-on management feature enabled can still use it. In clusters where add-ons have not been configured, components such as Calico and CoreDNS may still be running, but the system will show that the add-ons are not installed. In this case, you can install each add-on manually, after which you can manage them through the add-on management feature. If you have modified the configuration of resources that make up an add-on, selecting the 'Preserve' option during installation allows you to retain the existing resource settings.

<a id="addon-mgmt-types"></a>
### Add-on Types { #addon-mgmt-types }
Add-on types categorize the add-ons installed in a cluster according to their characteristics.

| Type | Required | Description |
|---|---|---|
| CNI | O | The type corresponding to the CNI to be installed in the cluster. |
| kube-dns | O | The default DNS server running within an NKS cluster. |
| cinder-csi-plugin | X | A CSI driver that can provision and manage block storage in NHN Cloud. |
| metrics-server | X | A Kubernetes component that collects resource usage metrics from nodes and pods for autoscaling and monitoring. |
| snapshot-controller | X | A Kubernetes component that manages the lifecycle of volume snapshots, including creation, deletion, and PVC integration. |
| nfs-csi-plugin | X | A CSI driver that can provision and manage NFS in NHN Cloud. |

<a id="addon-mgmt-addon-list"></a>
### Add-on List { #addon-mgmt-addon-list }

<a id="addon-mgmt-addon-calico"></a>
#### Calico
Calico is a CNI plugin that provides networking and network security for Kubernetes. For more information about Calico provided by NHN Cloud, see [Calico CNI types](#calico-cni-types).

* Type: CNI
* Options
    * mode
        * Determines the operating mode of Calico.
        * Supported operating modes: vxlan, ebpf
* Non-user-modifiable resources and fields
    * Deployment/calico-kube-controllers, namespace kube-system
        * .spec.template.spec.containers[name="calico-kube-controllers"].image 
    * Deployment/calico-typha, namespace kube-system
        * .spec.template.spec.containers[name="calico-typha"].image
    * DaemonSet/calico-node, namespace kube-system
        * .spec.template.spec.initContainers[name="install-cni"].image
        * .spec.template.spec.initContainers[name="mount-bpffs"].image
        * .spec.template.spec.containers[name="calico-node"].image
* Supported version list
    * v3.28.2-nks1
    * v3.28.2-nks2: Improved the stability of the add-on management feature.
    * v3.28.2-nks3: Added support for konnectivity environments.
    * v3.28.2-nks4: Improved the stability of the add-on management feature.
    * v3.30.2-nks1
    * v3.30.2-nks2: Improved the stability of the add-on management feature.
    * v3.30.2-nks3: Added support for konnectivity environments.
    * v3.30.2-nks4: Improved the stability of the add-on management feature.
    * v3.31.4-nks1: The datastore is KDD (Kubernetes Datastore Driver) and supports konnectivity environments.
    * v3.31.4-nks2: Improved the stability of the add-on management feature.

> [Note]
> * The Calico versions that can be installed/updated on platform versions that support konnectivity (1.202605.0 or later) are as follows:
>     * v3.28.2-nks3 or later
>     * v3.30.2-nks3 or later
>     * v3.31.4 or later

<a id="addon-mgmt-addon-calico-datastore"></a>
##### Calico datastore
Calico stores various information such as pod IPs and per-node IP ranges in a datastore. Previously provided versions used etcd as the datastore, whereas newly provided versions use KDD (Kubernetes Datastore Driver) as the datastore. KDD uses Kubernetes CRDs to store various information in Kubernetes-level resources and objects. Using KDD simplifies the network topology and exposes all related information as CRs, which provides management advantages.

The following versions use etcd as the datastore:
* v3.28.2
* v3.30.2

The following versions use KDD as the datastore:
* v3.31.4 or later

> [Caution]
> * When updating the add-on to change the datastore from etcd to KDD, only the 'Overwrite' conflict resolution option is supported.
> * Updating the add-on to change the datastore from KDD to etcd is not supported.

<a id="addon-mgmt-addon-cilium"></a>
#### Cilium
Cilium is a CNI plugin that provides networking and network security for Kubernetes.

* Type: CNI
* Options: None
* Non-user-modifiable resources and fields
    * DaemonSet/cilium, namespace kube-system
        * .spec.template.spec.containers[name="cilium-agent"].image
        * .spec.template.spec.containers[name="cilium-envoy"].image
    * Deployment/cilium-operator, namespace kube-system
        * .spec.template.spec.containers[name="cilium-operator"].image
* Supported version list
    * v1.18.0-nks1
    * v1.18.0-nks2: Improved the stability of the add-on management feature.

#### CoreDNS
CoreDNS is the default DNS server for a Kubernetes cluster.

* Type: kube-dns
* Options: None
* Immutable resources and fields
    * Deployment/coredns, namespace kube-system
        * .spec.template.spec.containers[name="coredns"].image'
* Supported version list
    * 1.8.4-nks1
    * 1.8.4-nks2
        * Improved stability for add-on management.
        * Adjusted immutable resources and fields.
            * Deployment/coredns, namespace kube-system
                * Removed .metadata.labels.k8s-app
                * Removed .metadata.labels.kubernetes.io/name
                * Removed .spec.template.spec.nodeSelector
                * Removed .spec.template.spec.serviceAccountName
    * 1.8.4-nks3: Improved stability for add-on management.


<a id="addon-mgmt-addon-list-cinder-csi-plugin"></a>
#### Cinder CSI Plugin
The Cinder CSI Plugin is a CSI driver that allows you to provision and manage block storage in NHN Cloud.

* Type: cinder-csi-plugin
* Options: None
* Immutable resources and fields
    * StatefulSet/csi-cinder-controllerplugin, namespace kube-system
        * .spec.template.spec.containers[name="csi-attacher"].image
        * .spec.template.spec.containers[name="csi-provisioner"].image
        * .spec.template.spec.containers[name="csi-snapshotter"].image
        * .spec.template.spec.containers[name="csi-resizer"].image
        * .spec.template.spec.containers[name="cinder-csi-plugin"].image

* Supported version list
    * v1.27.101-nks1
    * v1.27.101-nks2: Updated the internal container versions.
        * csi-attacher: v3.0.2 → v3.3.0
        * csi-provisioner: v2.0.4 → v2.2.2
        * csi-snapshotter: v3.0.2 → v3.0.3
        * csi-resizer: v1.0.1 → v1.3.0
        * csi-node-driver-registrar: v2.0.1 → v2.3.0
    * v1.27.101-nks3: Improved stability for add-on management.
    * v1.27.102-nks1
    * v1.27.102-nks2: Updated the internal container versions.
        * csi-attacher: v3.0.2 → v3.3.0
        * csi-provisioner: v2.0.4 → v2.2.2
        * csi-snapshotter: v3.0.2 → v3.0.3
        * csi-resizer: v1.0.1 → v1.3.0
        * csi-node-driver-registrar: v2.0.1 → v2.3.0
    * v1.27.102-nks3: Improved stability for add-on management.
    * v1.27.102-nks4
        * Improved stability for add-on management.
        * Removed `effect: NoExecute` from the cinder-csi-nodeplugin DaemonSet toleration.

<a id="addon-mgmt-addon-list-metrics-server"></a>
#### Metrics Server
Metrics Server is a Kubernetes component that collects resource usage metrics from nodes and pods for autoscaling and monitoring.

* Type: metrics-server
* Options: None
* Immutable resources and fields
    * Deployment/metrics-server, namespace kube-system
        * .spec.template.spec.containers[name="metrics-server"].image
* Supported version list
    * v0.4.4-nks1
    * v0.4.4-nks2: Improved stability for add-on management.
    * v0.4.4-nks3: Improved stability for add-on management.

<a id="addon-mgmt-addon-list-snapshot-controller"></a>
#### Snapshot Controller
Snapshot Controller is a Kubernetes component that manages the lifecycle of volume snapshots, including creation, deletion, and PVC integration.

* Type: snapshot-controller
* Options: None
* Immutable resources and fields
    * Deployment/snapshot-controller, namespace kube-system
        * .spec.template.spec.containers[name="snapshot-controller"].image
* Supported version list
    * v4.1.1-nks1
    * v4.1.1-nks2: Improved stability for add-on management.
    * v4.1.1-nks3: Improved stability for add-on management.

<a id="addon-mgmt-addon-list-nfs-csi-plugin"></a>
#### NFS CSI Plugin
NFS CSI Plugin is a CSI driver that can provision and manage NFS in NHN Cloud.

* Type: nfs-csi-plugin
* Options: None
* Non-user-modifiable resources and fields
    * Deployment/csi-nfs-controller, namespace kube-system
        * .spec.template.spec.containers[name="csi-provisioner"].image
        * .spec.template.spec.containers[name="csi-snapshotter"].image
        * .spec.template.spec.containers[name="liveness-probe"].image
        * .spec.template.spec.containers[name="nfs"].image
    * DaemonSet/csi-nfs-node, namespace kube-system
        * .spec.template.spec.containers[name="liveness-probe"].image
        * .spec.template.spec.containers[name="node-driver-registrar"].image
        * .spec.template.spec.containers[name="nfs"].image
* Supported version list
    * v1.0.1-nks1
    * v1.0.1-nks2
        * Improved the stability of the add-on management feature.
        * Fixed an issue where non-user-modifiable resources/fields were not being checked.
    * v1.0.1-nks3: Improved the stability of the add-on management feature.
    * v1.0.2-nks1
        * Fixed an issue where the optional snapshot configuration was being required as mandatory.
    * v1.0.2-nks2: Improved the stability of the add-on management feature.
    * v1.0.3-nks1: Fixed an issue where PVs were not deleted when deleting PVCs based on a storageclass with a reclaimPolicy of Delete.

<a id="loadbalancer-service"></a>
## LoadBalancer Service { #loadbalancer-service }
A pod is the basic execution unit of a Kubernetes application and is connected to the cluster network via a CNI (container network interface). By default, pods are not accessible from outside the cluster. To expose a pod's services to the outside of the cluster, you need to create a path to expose to the outside using the Kubernetes `LoadBalancer` Service object. Creating a LoadBalancer service object creates an NHN Cloud Load Balancer outside the cluster and associates it with the service object.

<a id="create-webserver-pod"></a>
### Create a web server pod { #create-webserver-pod }
Write a manifest file for a Deployment object that runs two nginx pods as shown below, and create the object.

```yaml
# nginx.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

When you create the Deployment object, the pods defined in the manifest are created automatically.

```
$ kubectl apply -f nginx.yaml
deployment.apps/nginx-deployment created

$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE  
nginx-deployment-7fd6966748-pvrzs   1/1     Running   0          4m13s
nginx-deployment-7fd6966748-wv7rd   1/1     Running   0          4m13s
```

<a id="create-lb-service"></a>
### Create a LoadBalancer service { #create-lb-service }
To define a service object in Kubernetes, you need a manifest composed of the following items.

| Item | Description |
| --- | --- |
| metadata.name | Name of the service object |
| spec.selector | Name of the pod to associate with the service object |
| spec.ports | Interface settings for forwarding traffic from the external load balancer to the pod |
| spec.ports.name | Interface name |
| spec.ports.protocol | Protocol to use on the interface (e.g., TCP) |
| spec.ports.port | Port number to expose outside the service object |
| spec.ports.targetPort | Port number of the pod to associate with the service object |
| spec.type | Service object type |

Service manifest is written like below. The LoadBalancer service object is associated with a pod labelled as `app: nginx`, following the defined name at **spec.selector**. And as **spec.ports** defines, traffic inflow via TCP/8080 is delivered to the TCP/80 port of the pod.

```yaml
# service.yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-svc
  labels:
    app: nginx
spec:
  ports:
  - port: 8080
    targetPort: 80
    protocol: TCP
  selector:
    app: nginx
  type: LoadBalancer
```

When the LoadBalancer service object is created, it takes some time to create and associate a load balancer externally. Before associated with external load balancer, **EXTERNAL-IP** shows `<pending>`.

```
$ kubectl apply -f service.yaml
service/nginx-svc created

$ kubectl get service
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
nginx-svc    LoadBalancer   10.254.134.18   <pending>     8080:30013/TCP   11s
```

Once associated with the external load balancer, an IP address appears in the **EXTERNAL-IP** field. This IP is the floating IP of the external load balancer.

```
$ kubectl get service
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)          AGE
nginx-svc    LoadBalancer   10.254.134.18   123.123.123.30   8080:30013/TCP   3m13s
```

> [Note]
> You can view the created load balancer on the **Network > Load Balancer** page.
> Load balancer IP is a floating IP allowing external access. You can check it on the **Network > Floating IP** page.

<a id="internet-test-via-service"></a>
### Test the service via the internet { #internet-test-via-service }
Send an HTTP request to a floating IP associated with a load balancer to see whether the web server pod in a Kubernetes cluster responds. Since the TCP/8080 port of a service object is attached to the TCP/80 port of a pod, the request must be sent to the TCP/8080 port. If the external load balancer, service object, and pod are well associated, the web server shall respond to the nginx default page.

```
$ curl http://123.123.123.30:8080
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

<a id="advanced-lb-configuration"></a>
### Configure advanced load balancer options { #advanced-lb-configuration }
When defining service objects in Kubernetes, you can set several options for the load balancer. You can set the following.

* Set load balancer name
* Set keep-alive timeout
* Set load balancer type
* Configure static routes
* Configure session affinity
* Set whether to keep the floating IP address
* Set load balancer IP
* Set whether to use a floating IP
* Configure VPC
* Configure subnet
* Configure member subnet
* Set listener connection limit
* Set listener protocol
* Set listener Proxy Protocol
* Set load balancing method
* Set health check protocol
* Set health check interval
* Set health check maximum response time
* Set health check maximum retry count
* Set health check port
* Set health check host header
* L7 rules and conditions

<a id="advanced-lb-configuration-global-setting-and-per-listener-setting"></a>
#### Global settings and per-listener settings
Each setting item can be configured as either a global setting or a per-listener setting. If neither a global setting nor a per-listener setting is configured, the default value for that setting is used.

* Per-listener setting: A setting that applies only to the target listener.
* Global setting: Applied to the target listener when no per-listener setting exists for that listener.

<a id="advanced-lb-configuration-format-of-per-listener-setting"></a>
#### Per-listener setting format
Per-listener settings can be set by appending a prefix representing the listener to the global settings key. The prefix representing the listener is the service object's port protocol (`spec.ports[].protocol`) and port number (`spec.ports[].port`) concatenated with a dash (`-`). For example, if the protocol is TCP and the port number is 80, the prefix is `TCP-80`. If you want to set session affinity for the listener associated with this port, you can set it in TCP-80.loadbalancer.nhncloud/pool-session-persistence under .metadata.annotations.

The following manifest is an example that combines global settings and per-listener settings.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: echosvr-svc
  labels:
    app: echosvr
  annotations:
    # Global settings
    loadbalancer.nhncloud/pool-lb-method: SOURCE_IP
    
    # Per-listener settings
    TCP-80.loadbalancer.nhncloud/pool-session-persistence: "SOURCE_IP"
    TCP-80.loadbalancer.nhncloud/listener-protocol: "HTTP"
    TCP-443.loadbalancer.nhncloud/pool-lb-method: LEAST_CONNECTIONS
    TCP-443.loadbalancer.nhncloud/listener-protocol: "TCP"
spec:
  ports:
  - name: tcp-80
    port: 80
    targetPort: 8080
    protocol: TCP
  - name: tcp-443
    port: 443
    targetPort: 8443
    protocol: TCP
  selector:
    app: echosvr
  type: LoadBalancer
```

When this manifest is applied, the per-listener settings are configured as shown in the following table.

| Item | TCP-80 listener | TCP-433 listener | Description |
| --- | --- | --- | --- |
| Load balancing method | SOURCE_IP | LEAST_CONNECTIONS | The TCP-80 listener is set to SOURCE_IP according to the global setting<br>The TCP-443 listener is set to LEAST_CONNECTIONS according to the per-listener setting |
| Session affinity | SOURCE_IP | None | The TCP-80 listener is set to SOURCE_IP according to the per-listener setting<br>The TCP-443 listener is set to None according to the default value |
| Listener protocol | HTTP | TCP | Both the TCP-80 and TCP-443 listeners are configured according to their respective per-listener settings |

> [Note]
> The features without additional version information are applicable only to clusters running Kubernetes v1.19.13 or later.
> For clusters of Kubernetes v1.19.13 version, per-listener settings apply only to clusters created on January 25, 2022 or after.
>

> [Caution]
> All setting values for the features below must be entered in string format. In the YAML file input format, to enter in string format regardless of the input value, enclose the input value in double quotation marks ("). For more information about the YAML file format, see [Yaml Cookbook](https://yaml.org/YAML_for_ruby.html).
>

<a id="loadbalancer-update-without-modification"></a>
#### Update a load balancer without changing its settings

If you need to update a load balancer without changing its settings — for example, to update a certificate — you can use the following command.

```
# Set the annotation with the following command
kubectl annotate svc <name> loadbalancer.nhncloud/force-reconcile=true
```
Once the load balancer update starts, the annotation set by the above command is automatically removed.

> [Caution]
> This feature works on clusters with a platform version of 1.202605.0 or later.

<a id="advanced-lb-configuration-setting-load-balancer-name"></a>
#### Set load balancer name

You can set the name of the load balancer.

* The setting location is loadbalancer.nhncloud/loadbalancer-name under .metadata.annotations.
* Per-listener settings cannot be applied.
* Only alphanumeric characters, `-`, and `_` can be entered.
    * If the name contains invalid characters, the load balancer name is set according to the default load balancer name format.
    * Default load balancer name format: "kube_service_{CLUSTER_UUID}\_{SERVICE_NAMESPACE}_{SERVICE_NAME}"
* The maximum length is 255 characters. If the maximum length is exceeded, the load balancer name is truncated to 255 characters.

> [Caution]
> The following actions may cause serious malfunction of the load balancer.
> * Modifying the load balancer name after the service object has been created
> * Creating a load balancer with the same name within the same project

<a id="advanced-lb-configuration-set-load-balancer-type"></a>
#### Set load balancer type
You can set the type of the load balancer. For more information about load balancers, see the [Load Balancer Console User Guide](/Network/Load%20Balancer/en/console-guide/).

* The setting location is loadbalancer.nhncloud/loadbalancer-type under .metadata.annotations.
* Per-listener settings cannot be applied.
* You can set one of the following.
    * shared: Creates a 'general' type load balancer. This is the default value if not set.
    * dedicated: Creates a 'dedicated' type load balancer.

<a id="advanced-lb-configuration-set-static-routes"></a>
#### Configure static routes
You can configure whether to apply static routes to the load balancer.

* The setting location is loadbalancer.nhncloud/apply-subnet-host-routes under .metadata.annotations.
* Per-listener settings cannot be applied.
* You can set one of the following.
    * true: Applies static routes.
    * false: Does not apply static routes. This is the default value if not set.

> [Caution]
> The static route setting is available for clusters created after August 27, 2024 or clusters that have had their k8s version upgraded.

<a id="advanced-lb-configuration-set-the-session-affinity"></a>
#### Configure session affinity
You can configure session affinity for the load balancer.

* The setting location is loadbalancer.nhncloud/pool-session-persistence under .metadata.annotations.
* Per-listener settings can be applied.
* You can set one of the following.
    * Empty string (""): Sets session affinity to 'None'. This is the default value if not set.
    * SOURCE_IP: Sets session affinity to SOURCE_IP.
* If the load balancing method is SOURCE_IP, the session affinity setting is ignored and session affinity is set to 'None'.
* v1.17.6, v1.18.19 clusters
    * Cannot be changed after the load balancer is created.
* v1.19.13 and later clusters
    * Can be changed after the load balancer is created.

<a id="advanced-lb-configuration-set-whether-to-keep-a-floating-ip-address"></a>
#### Set whether to keep the floating IP address
A floating IP is associated with the load balancer. When deleting a load balancer or changing the floating IP, you can configure whether to delete or retain the floating IP associated with the load balancer.

* The setting location is loadbalancer.openstack.org/keep-floatingip under .metadata.annotations.
* Per-listener settings cannot be applied.
* You can set one of the following.
    * true: Retains the floating IP.
    * false: Deletes the floating IP. This is the default value if not set.

> [Note]
> If the floating IP address retention setting is not configured (default value: false), when the load balancer is deleted or the floating IP is changed, floating IPs that meet all of the following conditions are automatically deleted.
> 
> * The floating IP was automatically created when the service object was created.
> * The floating IP does not have deletion protection enabled.
> 
> Floating IPs that do not meet the above conditions are not subject to deletion, regardless of the floating IP address retention setting.

> [Caution]
> v1.18.19 clusters created before October 26, 2021 have an issue where floating IPs are not deleted when the load balancer is deleted. If you contact us through 1:1 inquiry of the Customer Center, we will provide detailed information on the procedure to solve this issue.


<a id="advanced-lb-configuration-set-the-load-balancer-ip"></a>
#### Set load balancer IP
You can set the IP of the load balancer when creating it.

* The setting location is .spec.loadBalancerIP.
* Per-listener settings cannot be applied.
* You can set one of the following.
  * Empty string (""): Associates the load balancer with an automatically created floating IP. This is the default value if not set.
  * \<Floating_IP\>: Associates the load balancer with an existing floating IP. This can be used when you have an allocated but unassociated floating IP.

The following is an example manifest that associates a user-defined floating IP with a load balancer.

```yaml
# service-fip.yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-svc-floatingIP
  labels:
    app: nginx
spec:
  loadBalancerIP: <Floating_IP>
  ports:
  - port: 8080
    targetPort: 80
    protocol: TCP
  selector:
    app: nginx
  type: LoadBalancer
```

<a id="advanced-lb-configuration-set-whether-to-use-the-floating-ip"></a>
#### Set Whether to Use Floating IPs
You can set whether to use floating IPs when creating the load balancer.

* The setting location is service.beta.kubernetes.io/openstack-internal-load-balancer under .metadata.annotations.
* Per-listener settings cannot be applied.
* You can set it to one of the following:
  * true: Does not use a floating IP, and uses a VIP (Virtual IP) instead.
  * false: Uses a floating IP. This is the default value if not set.
* If you are using a VIP, you can specify the VIP to connect to the load balancer instead of the automatically created VIP by setting the .spec.loadBalancerIP entry together.

The following is an example of manifest for associating a custom VIP with the load balancer.

```yaml
# service-vip.yaml
apiVersion: v1
kind: Service
metadata:
 name: nginx-svc-fixedIP
 labels:
   app: nginx
 annotations:
   service.beta.kubernetes.io/openstack-internal-load-balancer: "true"
spec:
 loadBalancerIP: <Virtual_IP>
 ports:
 - port: 8080
   targetPort: 80
   protocol: TCP
 selector:
   app: nginx
 type: LoadBalancer
```

Depending on the combination of floating IP usage and load balancer IP setting, it works as follows.

| Floating IP Usage Setting | Load Balancer IP Setting | Description |
| --- | --- | --- |
| false | Not set | Creates and connects a floating IP to the load balancer. |
| false | Set | Connects the specified floating IP to the load balancer. |
| true | Not set | Automatically sets the VIP connected to the load balancer. |
| true | Set | Connects the specified VIP to the load balancer. |


<a id="advanced-lb-configuration-set-vpc"></a>
#### Set VPC
You can set a VPC to which the load balancer is connected when creating a load balancer.

* The setting location is loadbalancer.openstack.org/network-id under .metadata.annotations.
* Per-listener settings cannot be applied.
* If not set, the VPC configured when the cluster was created is used.

<a id="advanced-lb-configuration-set-subnet"></a>
#### Set Subnet
You can set a subnet to which the load balancer is connected when creating a load balancer. The load balancer's private IP is connected to the set subnet. If no member subnet is set, worker nodes connected to this subnet are added as load balancer members.

* The setting location is loadbalancer.openstack.org/subnet-id under .metadata.annotations.
* Per-listener settings cannot be applied.
* If not set, the subnet configured when the cluster was created is used.

Below is an manifest example of setting a VPC, subnet, and member subnet for the load balancer.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-svc-vpc-subnet
  labels:
     app: nginx
  annotations:
    loadbalancer.openstack.org/network-id: "49a5820b-d941-41e5-bfc3-0fd31f2f6773"
    loadbalancer.openstack.org/subnet-id: "38794fd7-fd2e-4f34-9c89-6dd3fd12f548"
spec:
  ports:
  - port: 8080
    targetPort: 80
    protocol: TCP
  selector:
    app: nginx
  type: LoadBalancer
```

<a id="advanced-lb-configuration-set-member-subnet"></a>
#### Set Member Subnets
When creating a load balancer, you can set a subnet to which load balancer members will be connected. Worker nodes connected to this subnet are added as load balancer members.

* The setting location is `loadbalancer.nhncloud/member-subnet-id` under `.metadata.annotations`.
* Per-listener settings cannot be applied.
* If not set, the load balancer's subnet setting value is applied.
* The member subnet must be in the same VPC as the load balancer subnet.
* To set two or more member subnets, enter them as a comma-separated list.

The following is an example manifest for setting the VPC, subnet, and member subnet for a load balancer.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-svc-vpc-subnet
  labels:
     app: nginx
  annotations:
    loadbalancer.openstack.org/network-id: "49a5820b-d941-41e5-bfc3-0fd31f2f6773"
    loadbalancer.openstack.org/subnet-id: "38794fd7-fd2e-4f34-9c89-6dd3fd12f548"
    loadbalancer.nhncloud/member-subnet-id: "c3548a5e-b73c-48ce-9dc4-4d4c484108bf"
spec:
  ports:
  - port: 8080
    targetPort: 80
    protocol: TCP
  selector:
    app: nginx
  type: LoadBalancer
```

> [Caution]
> If you set the load balancer's subnet and member subnet differently, pay attention to the network settings. The following examples illustrate this.
>
> **Example 1.**
>
> * Load balancer subnet: subnet#1
> * Load balancer member subnet: subnet#2
> * Network interface subnet settings for the instance
>     * eth0: subnet#1
>     * eth1: subnet#2 (member)
>
> In this case, the IP address of instance eth1 is registered as a member. Healthcheck packets sent by the load balancer are received by instance eth1 and attempted to be sent through eth0. Please note that the source IP address of the packet to eth0 is different from the IP address of eth0. If source/destination verification is enabled on eth0's network interface, this packet will not be sent and discarded. In a configuration like this, you must disable source/destination verification on eth0's network interface for the member to function properly. For more information on the source/destination verification feature, see [Change source/destination verification](/Network/Network%20Interface/en/console-guide/#_4).
>
> **Example 2.**
>
> * Load balancer subnet: subnet#1
> * Load balancer member subnet: subnet#2
> * Network interface subnet settings for the instance
>     * eth0: subnet#3
>     * eth1: subnet#2 (member)
>
> In this case, the IP address of instance eth1 is registered as a member. Healthcheck packets sent by the load balancer are received by instance eth1. The response packets must be sent to the VIP of the load balancer, but since subnet#1 is not a directly connected network, the egress interface is determined by the routing table. To enable communication without setting up the source/destination verification feature on the network interface, you must set up routing to allow traffic destined for the load balancer's VIP to be sent through eth1.


> [Caution]
> Member subnets can be set up on clusters that have been upgraded to v1.24.3 or later after November 28, 2023, or are newly created.


<a id="advanced-lb-configuration-set-the-listener-connection-limit"></a>
#### Set Listener Connection Limit
You can set the connection limit for a listener.

* The setting location is `loadbalancer.nhncloud/connection-limit` under `.metadata.annotations`.
* Per-listener settings can be applied.
* v1.17.6, v1.18.19 clusters
    * The minimum value is 1 and the maximum value is 60,000.
    * It you do not set it, it is set to -1, and the value applied to the actual load balancer is 2000.
* Clusters from v1.19.13 onwards
    * The minimum value is 1 and the maximum value is 60,000.
    * If not set or a value outside the range is entered, it is set to the default value of 60,000.


<a id="advanced-lb-configuration-set-the-listener-protocol"></a>
#### Set the listener protocol
You can set the protocol for a listener.

* The setting is located at `loadbalancer.nhncloud/listener-protocol` under `.metadata.annotations`.
* Per-listener settings can be applied.
* You can set it to one of the following:
    * TCP: This is the default value if not set.
    * HTTP
    * HTTPS
    * TERMINATED_HTTPS: Sets the protocol to TERMINATED_HTTPS. You must additionally configure the SSL version, certificate, and private key information.

> [Caution]
> The listener protocol setting is not applied to the load balancer even if you change the service object.
> To change the listener protocol setting, you must delete the service object and then create it again.
> Note that the load balancer is deleted and then re-created in this case.


The SSL version can be configured as follows:

* The setting is located at `loadbalancer.nhncloud/listener-terminated-https-tls-version` under `.metadata.annotations`.
* Per-listener settings can be applied.
* You can set it to one of the following:
    * TLSv1.3: This is the default value if not set.
    * TLSv1.2
    * TLSv1.1
    * TLSv1.0_2016
    * TLSv1.0
    * SSLv3

> [Caution]
> TLSv1.3 can be set in clusters created after March 29, 2022.

Certificate information can be configured as follows:

* The setting is located at `loadbalancer.nhncloud/listener-terminated-https-cert` under `.metadata.annotations`.
* Per-listener settings can be applied.
* The header line and footer line must be included.

Private key information can be configured as follows:

* The setting is located at `loadbalancer.nhncloud/listener-terminated-https-key` under `.metadata.annotations`.
* Per-listener settings can be applied.
* The header line and footer line must be included.

The following is an example of manifest for setting the listener protocol to TERMINATED_HTTPS. Certificate information and private key information are partially omitted.
```yaml
metadata:
  name: echosvr-svc
  labels:
    app: echosvr
  annotations:
    loadbalancer.nhncloud/listener-protocol: TERMINATED_HTTPS
    loadbalancer.nhncloud/listener-terminated-https-tls-version: TLSv1.2
    loadbalancer.nhncloud/listener-terminated-https-cert: |
      -----BEGIN CERTIFICATE-----
      MIIDZTCCAk0CCQDVfXIZ2uxcCTANBgkqhkiG9w0BAQUFADBvMQswCQYDVQQGEwJL
      ...
      fnsAY7JvmAUg
      -----END CERTIFICATE-----
    loadbalancer.nhncloud/listener-terminated-https-key: |
      -----BEGIN RSA PRIVATE KEY-----
      MIIEowIBAAKCAQEAz+U5VNZ8jTPs2Y4NVAdUWLhsNaNjRWQm4tqVPTxIrnY0SF8U
      ...
      u6X+8zlOYDOoS2BuG8d2brfKBLu3As5VAcAPLcJhE//3IVaZHxod
      -----END RSA PRIVATE KEY-----
```

Instead of registering certificate information and private key information in the manifest, you can create a TERMINATED_HTTPS type listener by using a certificate registered in Certificate Manager.

* The setting is located at `loadbalancer.nhncloud/listener-terminated-https-cert-manager-name` under `.metadata.annotations`.
* The value is the name of the certificate registered in Certificate Manager.
* Per-listener settings can be applied.

The following is an example manifest that uses a certificate registered in Certificate Manager when setting the listener protocol to TERMINATED_HTTPS.

```yaml
metadata:
  name: echosvr-svc
  labels:
    app: echosvr
  annotations:
    loadbalancer.nhncloud/listener-protocol: TERMINATED_HTTPS
    loadbalancer.nhncloud/listener-terminated-https-tls-version: TLSv1.2
    loadbalancer.nhncloud/listener-terminated-https-cert-manager-name: test
```

> [Caution]
> Using certificates registered in Certificate Manager is available for clusters that were created on or after May 28, 2024, or that have upgraded to the k8s version.
> Deleting a certificate from Certificate Manager that is associated with a listener might affect the behavior of the load balancer.

<a id="advanced-lb-configuration-set-the-listener-proxy-protocol"></a>
#### Set the listener proxy protocol (Proxy Protocol)
When the listener protocol is TCP or HTTPS, you can set the proxy protocol to the listener. For more information on proxy protocol, see [Load Balancer Proxy Mode](/Network/Load%20Balancer/en/overview/#_4).

* The setting is located at `loadbalancer.nhncloud/proxy-protocol` under `.metadata.annotations`.
* Per-listener settings can be applied.
* You can set it to one of the following:
    * true: Enables the proxy protocol.
    * false: Disables the proxy protocol. This is the default value if not set.

<a id="advanced-lb-configuration-set-the-load-balancing-method"></a>
#### Set the load balancing method
You can set the load balancing method.

* The setting is located at `loadbalancer.nhncloud/pool-lb-method` under `.metadata.annotations`.
* Per-listener settings can be applied.
* You can set it to one of the following:
    * ROUND_ROBIN: This is the default value if not set.
    * LEAST_CONNECTIONS
    * SOURCE_IP


<a id="advanced-lb-configuration-set-the-health-check-protocol"></a>
#### Set the health check protocol
You can set the health check protocol.

* The setting is located at `loadbalancer.nhncloud/healthmonitor-type` under `.metadata.annotations`.
* Per-listener settings can be applied.
* You can set it to one of the following:
    * HTTP: You must additionally configure the HTTP URL, HTTP method, and HTTP status code.
    * HTTPS: You must additionally configure the HTTP URL, HTTP method, and HTTP status code.
    * TCP: This is the default value if not set.

The HTTP URL can be configured as follows:

* The setting is located at `loadbalancer.nhncloud/healthmonitor-http-url` under `.metadata.annotations`.
* Per-listener settings can be applied.
* The value must start with `/`.
* If you do not set a value or enter a value that does not conform to the rule, it is set to the default value of `/`.

The HTTP method can be configured as follows:

* The setting is located at `loadbalancer.nhncloud/healthmonitor-http-method` under `.metadata.annotations`.
* Per-listener settings can be applied.
* Currently, only GET is supported. If you do not set a value or enter a different value, it is set to the default value of GET.

The HTTP status code can be configured as follows:

* The setting is located at `loadbalancer.nhncloud/healthmonitor-http-expected-code` under `.metadata.annotations`.
* Per-listener settings can be applied.
* You can enter a single value (e.g., 200), a list (e.g., 200,202), or a range (e.g., 200-204).
* If you do not set a value or enter a value that does not conform to the rule, it is set to the default value of 200.

<a id="advanced-lb-configuration-set-the-health-check-interval"></a>
#### Set the health check interval
You can set the health check interval.

* The setting is located at `loadbalancer.nhncloud/healthmonitor-delay` under `.metadata.annotations`.
* Per-listener settings can be applied.
* The value is set in seconds.
* The minimum value is 1 and the maximum value is 5000.
* If you do not set a value or enter a value outside the range, it is set to the default value of 60.

<a id="advanced-lb-configuration-set-the-health-check-maximum-response-time"></a>
#### Set the health check maximum response time
You can set the health check maximum response time.

* The setting is located at `loadbalancer.nhncloud/healthmonitor-timeout` under `.metadata.annotations`.
* Per-listener settings can be applied.
* The value is set in seconds.
* The minimum value is 1 and the maximum value is 5000.
* This setting must be less than the health check interval setting value.
* If you do not set a value or enter a value outside the range, it is set to the default value of 30.
* However, if the entered value or configured value is greater than the health check interval, it is set to 1/2 of the health check interval setting.

<a id="advanced-lb-configuration-set-the-maximum-number-of-retries-for-a-health-check"></a>
#### Set the maximum number of retries for a health check
You can set the maximum number of retries for a health check.

* The setting is located at `loadbalancer.nhncloud/healthmonitor-max-retries` under `.metadata.annotations`.
* Per-listener settings can be applied.
* The minimum value is 1 and the maximum value is 10.
* If you do not set a value or enter a value outside the range, it is set to the default value of 3.

<a id="advanced-lb-configuration-health-check-port-settings"></a>
#### Set the health check port
You can set the member port to be targeted for health checks.

* The setting is located at `loadbalancer.nhncloud/healthmonitor-health-check-port` under `.metadata.annotations`.
* Per-listener settings can be applied.
* The minimum value is 0 and the maximum value is 65535.
* If you specify 0, health checks are performed against the port number assigned to each member.
* If you do not set a value or enter a value outside the range, it is set to the default value of 0.

<a id="advanced-lb-configuration-health-check-host-header-settings"></a>
#### Set the health check host header
You can set the field value of the host header to use for health checks.

* The setting is located at `loadbalancer.nhncloud/healthmonitor-http-host-header` under `.metadata.annotations`.
* Per-listener settings can be applied.
* If the health check protocol is set to TCP, the value configured in this field is ignored.

<a id="advanced-lb-configuration-setting-keep-alive-timeout"></a>
#### Set keep-alive timeout
You can set the keep-alive timeout value.

* The setting is located at `loadbalancer.nhncloud/keepalive-timeout` under `.metadata.annotations`.
* Per-listener settings can be applied.
* The value is set in seconds.
* The minimum value is 0 and the maximum value is 3600.
* If you do not set a value or enter a value outside the range, it is set to the default value of 300.

> [Caution]
> keep-alive timeout can be set up on clusters that have been upgraded to v1.24.3 or later after November 28, 2023, or are newly created.

<a id="advanced-lb-configuration-l7-rules"></a>
#### L7 rules
You can set L7 rules per listener. L7 rules work as follows:

* L7 rules can only be created when the listener protocol is HTTP or TERMINATED_HTTPS.
* L7 rules are applied in the following order based on action type: REJECT, REDIRECT_TO_URL, and REDIRECT_TO_POOL.
* Within the same action type, a lower index value indicates higher priority.
* A member group containing nodes connected to the member subnet is created, and this member group is set as the default member group for the listener.

L7 rules can be configured as follows:

* You can configure up to 10 L7 rules per listener.
* To identify each L7 rule, use the format `l7policy-%d` (where `%d` is an index starting from 0) in the setting location.

| Setting location | Description | Required | Value |
| --- | --- | :-: | --- |
| {LISTENER_SPEC}.{L7POLICY}.loadbalancer.nhncloud/name | Name | O | String of up to 255 characters |
| {LISTENER_SPEC}.{L7POLICY}.loadbalancer.nhncloud/description | Description | X | String of up to 255 characters |
| {LISTENER_SPEC}.{L7POLICY}.loadbalancer.nhncloud/action | Action type | O | One of REDIRECT_TO_POOL (forward to member group), REDIRECT_TO_URL (forward to URL), or REJECT (block) |
| {LISTENER_SPEC}.{L7POLICY}.loadbalancer.nhncloud/redirect-url | URL to redirect to | X (required if the action type is REDIRECT_TO_URL) | URL starting with `HTTP://` or `HTTPS://` |

> [Note]
> * {LISTENER_SPEC} uses the format `[TCP|UDP]-%d`, where `%d` is the port number. (e.g., TCP-80)
> * {L7POLICY} uses the format `l7policy-%d`, where `%d` is an index starting from 0. (e.g., l7policy-0)

The following constraints apply to L7 rule settings:

* The index used in L7 rule settings must be an integer value between 0 and 9.
* L7 rules configured for the same listener must use different index values.
* L7 rules configured for the same listener must use different names.

<a id="advanced-lb-configuration-l7-conditions"></a>
#### L7 Conditions
You can set L7 conditions for each L7 rule. L7 conditions work as follows:

* An L7 rule is applied only when all L7 conditions that belong to it are satisfied.
* There is no priority among L7 conditions.

L7 conditions can be set as follows:

* You can set up to 10 L7 conditions per L7 rule.
* To identify each L7 condition, use the format `rule-%d` (where `%d` is a zero-based index) as the setting location.

| Setting Location | Meaning | Required | Value |
| --- | --- | :-: | --- |
| {LISTENER_SPEC}.{L7POLICY}.{RULE}.loadbalancer.nhncloud/type | Type | O | One of HOST_NAME, PATH, FILE_TYPE, HEADER, or COOKIE |
| {LISTENER_SPEC}.{L7POLICY}.{RULE}.loadbalancer.nhncloud/compare-type | Comparison method | O | One of REGEX, STARTS_WITH, ENDS_WITH, CONTAINS, or EQUAL_TO <br>(However, if the type is FILE_TYPE, only EQUAL_TO and REGEX can be used) |
| {LISTENER_SPEC}.{L7POLICY}.{RULE}.loadbalancer.nhncloud/key | Key | X (Required if the type is HEADER or COOKIE) | String of up to 255 characters |
| {LISTENER_SPEC}.{L7POLICY}.{RULE}.loadbalancer.nhncloud/value | Value | O | String of up to 255 characters |

> [Note]
> * {RULE} follows the format `rule-%d`, where `%d` is a zero-based index. (Example: rule-0)

L7 conditions have the following constraints:

* The index used for L7 condition settings must be an integer between 0 and 9.
* L7 conditions set within the same L7 rule must use different index values.
* An L7 condition with the same specification (where the type, comparison method, key, and value are all identical) cannot be added to the same L7 rule.

> [Caution]
> L7 rules and L7 conditions can be set up on clusters that have been upgraded to v1.24.3 or later after July 23, 2024, or are newly created.

The following is an example of setting L7 rules and conditions.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: echosvr-svc
  labels:
    app: echosvr
  annotations:
    TCP-80.loadbalancer.nhncloud/listener-protocol: "HTTP"

    TCP-80.l7policy-0.loadbalancer.nhncloud/name: "reject-policy"
    TCP-80.l7policy-0.loadbalancer.nhncloud/description: "default reject policy"
    TCP-80.l7policy-0.loadbalancer.nhncloud/action: "REJECT"

    TCP-80.l7policy-0.rule-0.loadbalancer.nhncloud/type: "PATH"
    TCP-80.l7policy-0.rule-0.loadbalancer.nhncloud/compare-type: "CONTAINS"
    TCP-80.l7policy-0.rule-0.loadbalancer.nhncloud/value: "temp"

    TCP-80.l7policy-1.loadbalancer.nhncloud/name: "redirect-policy"
    TCP-80.l7policy-1.loadbalancer.nhncloud/description: "basic redirection policy"
    TCP-80.l7policy-1.loadbalancer.nhncloud/action: "REDIRECT_TO_POOL"

    TCP-80.l7policy-1.rule-0.loadbalancer.nhncloud/type: "PATH"
    TCP-80.l7policy-1.rule-0.loadbalancer.nhncloud/compare-type: "CONTAINS"
    TCP-80.l7policy-1.rule-0.loadbalancer.nhncloud/value: "incoming"

    TCP-80.l7policy-1.rule-1.loadbalancer.nhncloud/type: "HOST_NAME"
    TCP-80.l7policy-1.rule-1.loadbalancer.nhncloud/compare-type: "STARTS_WITH"
    TCP-80.l7policy-1.rule-1.loadbalancer.nhncloud/value: "Ubuntu"

    TCP-443.loadbalancer.nhncloud/listener-protocol: "TERMINATED_HTTPS"
    TCP-443.loadbalancer.nhncloud/listener-terminated-https-tls-version: TLSv1.2
    TCP-443.loadbalancer.nhncloud/listener-terminated-https-cert-manager-name: test

    TCP-443.l7policy-0.loadbalancer.nhncloud/name: "reject-policy"
    TCP-443.l7policy-0.loadbalancer.nhncloud/description: "default reject policy"
    TCP-443.l7policy-0.loadbalancer.nhncloud/action: "REJECT"

    TCP-443.l7policy-0.rule-0.loadbalancer.nhncloud/type: "PATH"
    TCP-443.l7policy-0.rule-0.loadbalancer.nhncloud/compare-type: "CONTAINS"
    TCP-443.l7policy-0.rule-0.loadbalancer.nhncloud/value: "temp"

    TCP-443.l7policy-1.loadbalancer.nhncloud/name: "redirect-policy"
    TCP-443.l7policy-1.loadbalancer.nhncloud/description: "basic redirection policy"
    TCP-443.l7policy-1.loadbalancer.nhncloud/action: "REDIRECT_TO_POOL"

    TCP-443.l7policy-1.rule-0.loadbalancer.nhncloud/type: "PATH"
    TCP-443.l7policy-1.rule-0.loadbalancer.nhncloud/compare-type: "CONTAINS"
    TCP-443.l7policy-1.rule-0.loadbalancer.nhncloud/value: "incoming"

    TCP-443.l7policy-1.rule-1.loadbalancer.nhncloud/type: "HOST_NAME"
    TCP-443.l7policy-1.rule-1.loadbalancer.nhncloud/compare-type: "STARTS_WITH"
    TCP-443.l7policy-1.rule-1.loadbalancer.nhncloud/value: "Ubuntu"

spec:
  ports:
  - name: tcp-80
    port: 80
    targetPort: 8080
    protocol: TCP
  - name: tcp-443
    port: 443
    targetPort: 8443
    protocol: TCP
  selector:
    app: echosvr
  type: LoadBalancer
```

<a id="ingress-controller"></a>
## Ingress Controller { #ingress-controller }
Ingress Controller routes HTTP and HTTPS requests from cluster externals to internal services, in reference of the rules that are defined at ingress object so as to provide SSL/TSL closure and virtual hosting. For more details on Ingress Controller and Ingress, see [Ingress Controller](https://kubernetes.io/ko/docs/concepts/services-networking/ingress-controllers/), [Ingress](https://kubernetes.io/ko/docs/concepts/services-networking/ingress/).

<a id="install-nginx-ingress-controller"></a>
### Install NGINX Ingress Controller { #install-nginx-ingress-controller }
NGINX Ingress Controller is one of the most frequently used ingress controllers. For more details, see [NGINX Ingress Controller](https://kubernetes.github.io/ingress-nginx/) and [NGINX Ingress Controller for Kubernetes](https://www.nginx.com/products/nginx-ingress-controller/). For installation of NGINX Ingress Controller, see [Installation Guide](https://kubernetes.github.io/ingress-nginx/deploy/).

<a id="uri-based-service-routing"></a>
### URI-Based Service Routing { #uri-based-service-routing }
Ingress controller can diverge services based on URI. The following figure shows the structure of a simple example of service divergence based on URI.

![ingress-01.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/ingress-01.png)

<a id="uri-based-service-routing-create-services-and-pods"></a>
#### Create Services and Pods
Manifest is created to create services and pods like below: Associate the `tea` pod to `tea-svc`, and the `coffee` pod to `coffee-svc`.

```yaml
# cafe.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: coffee
spec:
  replicas: 3
  selector:
    matchLabels:
      app: coffee
  template:
    metadata:
      labels:
        app: coffee
    spec:
      containers:
      - name: coffee
        image: nginxdemos/nginx-hello:plain-text
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: coffee-svc
spec:
  ports:
  - port: 80
    targetPort: 8080
    protocol: TCP
    name: http
  selector:
    app: coffee
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: tea
spec:
  replicas: 2
  selector:
    matchLabels:
      app: tea
  template:
    metadata:
      labels:
        app: tea
    spec:
      containers:
      - name: tea
        image: nginxdemos/nginx-hello:plain-text
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: tea-svc
  labels:
spec:
  ports:
  - port: 80
    targetPort: 8080
    protocol: TCP
    name: http
  selector:
    app: tea
```

Apply manifest, and see if deployment, service, and pod is created. The pod must be **Running**.

```
$ kubectl apply -f cafe.yaml
deployment.apps/coffee created
service/coffee-svc created
deployment.apps/tea created
service/tea-svc created

# kubectl get deploy,svc,pods
NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/coffee   3/3     3            3           27m
deployment.apps/tea      2/2     2            2           27m

NAME                 TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
service/coffee-svc   ClusterIP   10.254.171.198   <none>        80/TCP    27m
service/kubernetes   ClusterIP   10.254.0.1       <none>        443/TCP   5h51m
service/tea-svc      ClusterIP   10.254.184.190   <none>        80/TCP    27m

NAME                          READY   STATUS    RESTARTS   AGE
pod/coffee-7c86d7d67c-pr6kw   1/1     Running   0          27m
pod/coffee-7c86d7d67c-sgspn   1/1     Running   0          27m
pod/coffee-7c86d7d67c-tqtd6   1/1     Running   0          27m
pod/tea-5c457db9-fdkxl        1/1     Running   0          27m
pod/tea-5c457db9-z6hl5        1/1     Running   0          27m
```

<a id="uri-based-service-routing-create-ingress"></a>
#### Create Ingress
According to the request path, ingress manifest is created for service connection. A request with `/tea` as endpoint is connected to the `tea-svc` service, while `/coffee` is connected to the `coffee-svc` service.

```yaml
# cafe-ingress-uri.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: cafe-ingress-uri
spec:
  ingressClassName: nginx
  rules:
  - http:
      paths:
      - path: /tea
        pathType: Prefix
        backend:
          service:
            name: tea-svc
            port:
              number: 80
      - path: /coffee
        pathType: Prefix
        backend:
          service:
            name: coffee-svc
            port:
              number: 80
```

In a while after ingress is created, IP must be configured at the **ADDRESS** field.

```
$ kubectl apply -f cafe-ingress-uri.yaml
ingress.networking.k8s.io/cafe-ingress-uri created

$ kubectl get ingress cafe-ingress-uri
NAME               CLASS   HOSTS   ADDRESS          PORTS   AGE
cafe-ingress-uri   nginx   *       123.123.123.44   80      23s
```

<a id="uri-based-service-routing-send-http-requests"></a>
#### Send HTTP Requests
Send HTTP request to the IP address set for **ADDRESS** of ingress for an external host, to check if the ingress has been properly set.

Request for `/coffee` as endpoint is sent to the `coffee-svc` service so as the `coffee` pod can respond. From the **Server Name**, you can see that `coffee` pods take turns to respond in the round-robin technique.

```
$ curl 123.123.123.44/coffee
Server address: 10.100.24.21:8080
Server name: coffee-7c86d7d67c-sgspn
Date: 11/Mar/2022:06:28:18 +0000
URI: /coffee
Request ID: 3811d20501dbf948259f4b209c00f2f1

$ curl 123.123.123.44/coffee
Server address: 10.100.24.19:8080
Server name: coffee-7c86d7d67c-tqtd6
Date: 11/Mar/2022:06:28:27 +0000
URI: /coffee
Request ID: ec82f6ab31d622895374df972aed1acd

$ curl 123.123.123.44/coffee
Server address: 10.100.24.20:8080
Server name: coffee-7c86d7d67c-pr6kw
Date: 11/Mar/2022:06:28:31 +0000
URI: /coffee
Request ID: fec4a6111bcc27b9cba52629e9420076
```

Likewise, a request for `/tea` as endpoint is sent to the `tea-svc` service so as the `tea` pod can respond.

```
$ curl 123.123.123.44/tea
Server address: 10.100.24.23:8080
Server name: tea-5c457db9-fdkxl
Date: 11/Mar/2022:06:28:36 +0000
URI: /tea
Request ID: 11be1b7634a371a26e6bf2d3e72ab8aa
$ curl 123.123.123.44/tea
Server address: 10.100.24.22:8080
Server name: tea-5c457db9-z6hl5
Date: 11/Mar/2022:06:28:37 +0000
URI: /tea
Request ID: 21106246517263d726931e0f85ea2887
```

When a request is sent to undefined URI, the ingress controller sends `404 Not Found` as response.

```
$ curl 123.123.123.44/unknown
<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx</center>
</body>
</html>
```

<a id="uri-based-service-routing-delete-resources"></a>
#### Delete Resources
Resources for testing can be deleted with used manifest.

```
$ kubectl delete -f cafe-ingress-uri.yaml
ingress.networking.k8s.io "cafe-ingress-uri" deleted

$ kubectl delete -f cafe.yaml
deployment.apps "coffee" deleted
service "coffee-svc" deleted
deployment.apps "tea" deleted
service "tea-svc" deleted
```

<a id="host-based-service-routing"></a>
### Host-Based Service Routing { #host-based-service-routing }
Ingress controller can diverge services based on the host name. The following figure shows the structure of a simple example of service divergence based on the host name.

![ingress-02.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/ingress-02.png)

<a id="host-based-service-routing-create-services-and-pods"></a>
#### Create Services and Pods
Create services and pods by using the same manifest as in [URI-based service routing](/Container/NKS/en/user-guide/#uri).

<a id="host-based-service-routing-create-ingress"></a>
#### Create Ingress
Write the ingress manifest connecting services based on the hostname. Incoming request via the `tea.cafe.example.com` host is connected to the `tea-svc` service, while request via the `coffee.cafe.example.com` host is connected to the `coffee-svc` service.

```yaml
# cafe-ingress-host.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: cafe-ingress-host
spec:
  ingressClassName: nginx
  rules:
  - host: tea.cafe.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: tea-svc
            port:
              number: 80
  - host: coffee.cafe.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: coffee-svc
            port:
              number: 80
```

In a while after ingress is created, IP must be configured at the **ADDRESS** field.

```
$ kubectl apply -f cafe-ingress-host.yaml
ingress.networking.k8s.io/cafe-ingress-host created

$ kubectl get ingress
NAME                CLASS   HOSTS                                          ADDRESS          PORTS   AGE
cafe-ingress-host   nginx   tea.cafe.example.com,coffee.cafe.example.com   123.123.123.44   80      36s
```

<a id="host-based-service-routing-send-http-requests"></a>
#### Send HTTP Requests
HTTP request is sent from external host to IP configured at the ADDRESS of the ingress controller. Nevertheless, such request must be sent by using host name, since service divergence is based on the host name by configuration.

> [Note]
> To test with a random host name, use the --resolve option of curl: enter the --resolve option in the `{Host Name}:{Port Number}:{IP}` format. This means to resolve a request for {Port Number} to be sent to {Host Name} as {IP}.
> You may open up the `/etc/host` file and add `{IP} {Host Name}`.

When a request is sent to the `coffee.cafe.example.com` host, it is delivered to `coffee-svc` so that the `coffee` pod can respond.

```
$ curl --resolve coffee.cafe.example.com:80:123.123.123.44 http://coffee.cafe.example.com/
Server address: 10.100.24.27:8080
Server name: coffee-7c86d7d67c-fqn6n
Date: 11/Mar/2022:06:40:59 +0000
URI: /
Request ID: 1efb60d29891d6d48b5dcd9f5e1ba66d
```

When a request is sent to the `tea.cafe.example.com` host, it is delivered to `tea-svc` so that the `tea` pod can respond.

```
$ curl --resolve tea.cafe.example.com:80:123.123.123.44 http://tea.cafe.example.com/
Server address: 10.100.24.28:8080
Server name: tea-5c457db9-ngrxq
Date: 11/Mar/2022:06:41:39 +0000
URI: /
Request ID: 5a6cc490893636029766b02d2aab9e39
```

When it is requested to an unknown host, the ingress controller sends `404 Not Found` as response.

```
$ curl 123.123.123.44/unknown
<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx</center>
</body>
</html>
```

<a id="ingress-nginx-internal-communication"></a>
### ingress-nginx Controller Internal Communication Structure and Cautions { #ingress-nginx-internal-communication }
When exposing a service externally through the ingress-nginx controller, the path through which requests are forwarded to the workload differs depending on the location of the client sending the request (inside or outside the cluster).

<a id="ingress-nginx-internal-communication-cluster-external-client"></a>
#### External Cluster Client
Requests sent by clients outside the cluster are forwarded to the Ingress Controller through the load balancer. The load balancer acts as the external endpoint for the Ingress Controller Service, and the Ingress Controller routes requests to the destination backend Pod according to the Ingress rules.

```
Cluster external client → Load balancer → ingress-nginx Service → ingress-nginx Controller Pod → Backend Pod
```

<a id="ingress-nginx-internal-communication-cluster-internal-client"></a>
#### Internal Cluster Client
When a Pod inside the cluster sends a request to the Ingress address, the traffic does not go through the load balancer. The request is forwarded directly through an internal path via the ClusterIP of the Ingress Controller Service, and in this process, routing is performed in the following manner depending on the CNI:

- **Calico (VXLAN)**: Based on kube-proxy iptables rules
- **Calico (eBPF)**: Uses BPF MAP-based data path

In both cases, traffic is forwarded only within the internal network and does not go through the external load balancer.
```
Internal Pod → ingress-nginx Service (ClusterIP) → ingress-nginx Controller Pod → Backend Pod
```

<a id="ingress-nginx-internal-communication-cautions"></a>
#### Cautions

- Internal requests are not subject to load balancer policies. TLS settings, security policies, and firewall rules of the load balancer do not affect internal traffic.
- If the Ingress domain is called internally as-is, TLS or redirect behavior may differ from external behavior because the request does not go through the load balancer.
- We recommend that you use the Service DNS instead of the Ingress domain for internal communication. For communication between internal Pods, use the Service directly, and use Ingress only for externally exposed endpoints.

<a id="k8s-dashboard"></a>
## Kubernetes Dashboard { #k8s-dashboard }
NHN Kubernetes Service (NKS) provides a default web UI dashboard. For more information about the Kubernetes dashboard, see [Web UI (Dashboard)](https://kubernetes.io/ko/docs/tasks/access-application-cluster/web-ui-dashboard/).

> [Caution]
> * The Kubernetes dashboard is provided by default only up to NKS v1.25.4.
> * If you upgrade your NKS cluster version from v1.25.4 to v1.26.3, the Kubernetes Dashboard pods and related resources will remain in action.
> * You can view Kubernetes resources in the NHN Cloud console.

<a id="expose-dashboard"></a>
### Expose Dashboard { #expose-dashboard }
In your Kubernetes environment, the `kubernetes-dashboard` service object is pre-created to expose the dashboard.

```
$ kubectl get svc kubernetes-dashboard -n kube-system
NAME                   TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)   AGE
kubernetes-dashboard   ClusterIP   10.254.85.2   <none>        443/TCP   6h

$ kubectl describe svc kubernetes-dashboard -n kube-system
Name:              kubernetes-dashboard
Namespace:         kube-system
Labels:            k8s-app=kubernetes-dashboard
Annotations:       <none>
Selector:          k8s-app=kubernetes-dashboard
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                10.254.85.2
IPs:               10.254.85.2
Port:              <unset>  443/TCP
TargetPort:        8443/TCP
Endpoints:         10.100.24.7:8443
Session Affinity:  None
Events:            <none>
```

However, the `kubernetes-dashboard` object belongs to the ClusterIP type and is not open outside the cluster. To open the dashboard externally, the service object type must be changed to LoadBalancer, or an ingress controller and ingress object must be created.

<a id="expose-dashboard-change-into-loadbalancer"></a>
#### Change to LoadBalancer Service Object

Once the type of service object is changed to `LoadBalancer`, NHN Cloud Load Balancer is created from the cluster, which is associated with the load balancer and the service object. By querying the service object associated with the load balancer, the IP of the load balancer is displayed in the **EXTERNAL-IP** field. See [LoadBalancer Service](/Container/NKS/en/user-guide/#loadbalancer) for the description of service objects of the `LoadBalancer` type. The following figure shows the structure of making a dashboard public using the `LoadBalancer` type service.

![dashboard-01.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/dashboard-01.png)

Change the type of the `kubernetes-dashboard` service object to `LoadBalancer` as follows.

```
$ kubectl -n kube-system patch svc/kubernetes-dashboard -p '{"spec":{"type":"LoadBalancer"}}'
service/kubernetes-dashboard patched
```

Once the `kubernetes-dashboard` service object is changed to the `LoadBalancer` type, the load balancer IP will be available in the **EXTERNAL-IP** field after a moment.

```
$ kubectl get svc -n kube-system
NAME                   TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)                  AGE
...
kubernetes-dashboard   LoadBalancer   10.254.95.176   123.123.123.81   443:30963/TCP            2d23h
```

> [Note]
> You can view the created load balancer on the **Network > Load Balancer** page.
> Load balancer IP is a floating IP allowing external access. You can check it on the **Network > Floating IP** page.

When you access `https://{EXTERNAL-IP}` in a web browser, the Kubernetes dashboard page loads. For the token required to log in, see [Dashboard Access Token](/Container/NKS/en/user-guide/#dashboard-access-token).

> [Note]
> Since Kubernetes dashboard is based on a private certificate that is automatically created, the page may be displayed as unsafe, depending on the web browser or security setting.

<a id="expose-dashboard-open-services-with-ingress"></a>
#### Expose Services Using Ingress

Ingress refers to the network object providing routing to access many services within a cluster. The setting of an ingress object runs by ingress controller. The `kubernetes-dashboard` service object can go public through ingress. See [Ingress Controller](/Container/NKS/en/user-guide/#ingress-controller) regarding description on ingress and ingress controller. The following figure shows the structure of making dashboard public through ingress.

![dashboard-02.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/dashboard-02.png)

Refer to [Install NGINX Ingress Controller](/Container/NKS/en/user-guide/#nginx-ingress-controller) to install `NGINX Ingress Controller`, and write a manifest to create an ingress object as follows.

```yaml
# kubernetes-dashboard-ingress-tls-passthrough.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: k8s-dashboard-ingress
  namespace: kube-system
  annotations:
    ingress.kubernetes.io/ssl-passthrough: "true"
    kubernetes.io/ingress.allow-http: "false"
    nginx.ingress.kubernetes.io/backend-protocol: HTTPS
    nginx.ingress.kubernetes.io/proxy-body-size: 100M
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.org/ssl-backend: kubernetes-dashboard
spec:
  ingressClassName: nginx
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: kubernetes-dashboard
            port:
              number: 443
  tls:
  - secretName: kubernetes-dashboard-certs
```

Apply the manifest to create the ingress and check the **ADDRESS** field of the ingress object.

```
$ kubectl apply -f kubernetes-dashboard-ingress-tls-passthrough.yaml
ingress.networking.k8s.io/k8s-dashboard-ingress created

$ kubectl get ingress -n kube-system
NAME                    CLASS   HOSTS   ADDRESS          PORTS     AGE
k8s-dashboard-ingress   nginx   *       123.123.123.44   80, 443   34s
```

When you access `https://{ADDRESS}` in a web browser, the Kubernetes dashboard page loads. For the token required to log in, see [Dashboard Access Token](/Container/NKS/en/user-guide/#dashboard-access-token).

<a id="dashboard-access-token"></a>
### Dashboard Access Token { #dashboard-access-token }
A token is required to log in to the Kubernetes dashboard. You can obtain the token with the following command.

```
# SECRET_NAME=$(kubectl -n kube-system get secrets | grep "kubernetes-dashboard-token" | cut -f1 -d ' ')

$ kubectl describe secret $SECRET_NAME -n kube-system | grep -E '^token' | cut -f2 -d':' | tr -d " "
eyJhbGc...-QmXA
```

Enter the output token in the token input field of the browser to log in as a user with cluster administrator privileges.

<a id="persistent-volume"></a>
## Persistent Volume { #persistent-volume }
Persistent Volume or PV is a Kubernetes resource representing physical storage volume. One PV is attached to one NHN Cloud Block Storage. For more details, see [Persistent Volumes](https://kubernetes.io/ko/docs/concepts/storage/persistent-volumes/).

Persistent Volume Claims, or PVC is required to attach PV to pods. PVC defines necessary volume requirements, including volume and read/write modes.

With PV and PVC, users can define the attributes of a volume of choice, while the system separates the use of resources from management by assigning volume resources for each user requirement.

<a id="pv-lifecycle"></a>
### PV/PVC Lifecycle { #pv-lifecycle }
PV and PVC follow a four-stage lifecycle.

* Provisioning
You can use a [storage class](https://kubernetes.io/ko/docs/concepts/storage/storage-classes/) to directly secure a volume and create a PV (static provisioning) or create one dynamically (dynamic provisioning).

* Binding
PV and PVC are bound 1:1. If a PV is created by dynamic provisioning, binding is also performed automatically.

* Using
Mount PV to a pod and use it.

* Reclaiming
Reclaims the volume after use. Reclaim methods include Delete, Retain, and Recycle.

| Method | Description |
| --- | --- |
| Delete | Deletes the connected volume when the PV is deleted. |
| Retain | Does not delete the connected volume when the PV is deleted. The volume can be deleted or reused by the user directly. |
| Recycle | Does not delete the connected volume when the PV is deleted, but puts it in a state ready for reuse. This method is deprecated. |

<a id="storageclass"></a>
### Storage Class (StorageClass) { #storageclass }
A storage class must be defined before provisioning. Storage classes provide a way to classify storage by certain characteristics. You can set the information, such as media type and availability zone, by including information about the storage provider (provisioner).

<a id="storageclass-storage-provider-provisioner"></a>
#### Storage Provider (provisioner)
Set the provider information of storage. Depending on the Kubernetes version, the supported storage provider information is as follows:

* v1.19.13 or earlier: The provisioner field must be set to `kubernetes.io/cinder`.
* v1.20.12 or later: You can use the provisioner field by setting it to `cinder.csi.openstack.org`.

<a id="storageclass-parameters-parameter"></a>
#### Parameter
You can configure the following parameters through the storage class.

* Storage type (type): Enter the type of storage. (If not specified, General HDD is set.)
    * **General HDD**: The storage type is set to HDD.
    * **General SSD**: The storage type is set to SSD.
* Availability zone (availability): Set the availability zone. (If not specified, it is set randomly.)
    * Pangyo region: **kr-pub-a** or **kr-pub-b**
    * Pyeongchon region: **kr2-pub-a** or **kr2-pub-b**
    * Gwangju region: **kr3-pub-a** or **kr3-pub-b**

<a id="storageclass-volume-binding-mode-volumebindingmode"></a>
#### Volume Binding Mode (VolumeBindingMode)
A volume binding mode controls the time when volume binding and dynamic provisioning start. This setting is configurable only if the storage provider is cinder.csi.openstack.org.

* **Immediate**: Volume binding and dynamic provisioning start as soon as a persistent volume claim is created. When the persistent volume claim is created, there is no prior knowledge of the pods to which the volume will be attached. Therefore, if the availability zone of the volume and the availability zone of the node on which the pod will be scheduled are different, the pod will not work properly.
* **WaitForFirstConsumer**: When a persistent volume claim is created, volume binding and dynamic provisioning do not start. When the persistent volume claim is first attached to a pod, volume binding and dynamic provisioning are performed based on the availability zone information of the node on which the pod is scheduled. Therefore, unlike Immediate mode, the case where the pod does not work properly due to a mismatch between the availability zone of the volume and the availability zone of the instance does not occur.

<a id="storageclass-allow-volume-expansion-allowvolumeexpansion"></a>
#### Allow Volume Expansion (allowVolumeExpansion)
Set whether to allow expansion of the created volume. (If not specified, false is set.)

* **True**: Allows volume expansion.
* **False**: Does not allow volume expansion.

<a id="storageclass-example-1"></a>
#### Example 1
The storage class manifest below can be used for Kubernetes clusters using v1.19.13 or earlier. You can use parameters to specify the availability zone and volume type.

```yaml
# storage_class.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: sc-ssd
provisioner: kubernetes.io/cinder
parameters:
  type: General SSD
  availability: kr-pub-a
```

Create and verify the storage class.

```
$ kubectl apply -f storage_class.yaml
storageclass.storage.k8s.io/sc-ssd created

$ kubectl get sc
NAME     PROVISIONER            RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
sc-ssd   kubernetes.io/cinder   Delete          Immediate           false                  3s
```

<a id="storageclass-example-2"></a>
#### Example 2
The storage class manifest below can be used for Kubernetes clusters using v1.20.12 or later. Set the volume binding mode to WaitForFirstConsumer to initiate volume binding and dynamic provisioning when a persistent volume claim is attached to a pod.

```yaml
# storage_class_csi.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: csi-storageclass
provisioner: cinder.csi.openstack.org
volumeBindingMode: WaitForFirstConsumer
```

Create and verify the storage class.

```
$ kubectl apply -f storage_class_csi.yaml
storageclass.storage.k8s.io/csi-storageclass created

$ kubectl get sc
NAME               PROVISIONER                RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
csi-storageclass   cinder.csi.openstack.org   Delete          WaitForFirstConsumer   false                  7s
```

<a id="static-provisioning"></a>
### Static Provisioning { #static-provisioning }

Static provisioning requires the user to prepare a block storage manually. On the **Storage > Block Storage** service page of the NHN Cloud web console, click the **Create Block Storage** button to create a block storage to attach to the PV. See [Create Block Storage](/Storage/Block%20Storage/en/console-guide/#_1) in the Block Storage guide.

To create a PV, you need the ID of the block storage. On the **Storage > Block Storage** service page, select the block storage you want to use from the block storage list. You can find the ID under the block storage name section in the **Information** tab at the bottom.

Write a manifest for the PV to be attached to the block storage. Enter the storage class name in **spec.storageClassName**. To use NHN Cloud Block Storage, **spec.accessModes** must be set to `ReadWriteOnce`. **spec.presistentVolumeReclaimPolicy** can be set to `Delete` or `Retain`.

Clusters in v1.20.12 and later must use the **cinder.csi.openstack.org** storage provider. To define the storage provider, specify the value `pv.kubernetes.io/provisioned-by: cinder.csi.openstack.org` under **spec.annotations** and the value `driver: cinder.csi.openstack.org` under **csi** entries.

> [Caution]
> You must configure a storage class with a storage provider that matches the Kubernetes version.

```yaml
# pv-static.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  annotations: 
    pv.kubernetes.io/provisioned-by: cinder.csi.openstack.org
  name: pv-static-001
spec:
  capacity:
    storage: 10Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Delete
  storageClassName: sc-default
  csi:
    driver: cinder.csi.openstack.org
    fsType: "ext3"
    volumeHandle: "e6f95191-d58b-40c3-a191-9984ce7532e5" # UUID of Block Storage
```

Create and verify the PV.

```
$ kubectl apply -f pv-static.yaml
persistentvolume/pv-static-001 created

$ kubectl get pv -o wide
NAME            CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE   VOLUMEMODE
pv-static-001   10Gi       RWO            Delete           Available           sc-default              7s    Filesystem
```

Write a PVC manifest to use the created PV. The PV name must be specified in **spec.volumeName**. Set other items the same as the PV manifest.

```yaml
# pvc-static.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-static
  namespace: default
spec:
  volumeName: pv-static-001
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
  storageClassName: sc-default
```

Create and verify the PVC.

```
$ kubectl apply -f pvc-static.yaml
persistentvolumeclaim/pvc-static created

$ kubectl get pvc -o wide
NAME         STATUS   VOLUME          CAPACITY   ACCESS MODES   STORAGECLASS   AGE   VOLUMEMODE
pvc-static   Bound    pv-static-001   10Gi       RWO            sc-default     7s    Filesystem
```

After the PVC is created, query the PV status, and you can find the PVC name specified for **CLAIM** and **STATUS** changed to `Bound`.

```
$ kubectl get pv -o wide
NAME            CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                STORAGECLASS   REASON   AGE   VOLUMEMODE
pv-static-001   10Gi       RWO            Delete           Bound    default/pvc-static   sc-default              79s   Filesystem
```

<a id="dynamic-provisioning"></a>
### Dynamic Provisioning { #dynamic-provisioning }

With Dynamic Provisioning, block storage is automatically created in reference of attributes defined at storage class. To use Dynamic Provisioning, do not set Volume Binding Mode of storage class or set it to **Immediate**.

```yaml
# storage_class_csi_dynamic.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: csi-storageclass-dynamic
provisioner: cinder.csi.openstack.org
volumeBindingMode: Immediate
```

There is no need to create PV for dynamic provisioning; therefore, PVC manifest does not require the setting of **spec.volumeName**.

```yaml
# pvc-dynamic.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-dynamic
  namespace: default
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
  storageClassName: csi-storageclass-dynamic
```

If you do not set the volume binding mode or set it to **Immediate** and create a PVC, the PV will be created automatically. At the same time, block storage attached to the PV is also automatically created, and you can check it from the list of block storages on the **Storage > Block Storage** page of the NHN Cloud web console.

```
$ kubectl apply -f pvc-dynamic.yaml
persistentvolumeclaim/pvc-dynamic created

$ kubectl get sc,pv,pvc
NAME                                                   PROVISIONER                RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
storageclass.storage.k8s.io/csi-storageclass-dynamic   cinder.csi.openstack.org   Delete          Immediate           false                  50s

NAME                                                        CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                 STORAGECLASS               REASON   AGE
persistentvolume/pvc-1056949c-bc67-45cc-abaa-1d1bd9e51467   10Gi       RWO            Delete           Bound    default/pvc-dynamic   csi-storageclass-dynamic            5s

NAME                                STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS               AGE
persistentvolumeclaim/pvc-dynamic   Bound    pvc-1056949c-bc67-45cc-abaa-1d1bd9e51467   10Gi       RWO            csi-storageclass-dynamic   9s
```

> [Caution]
> A block storage created by dynamic provisioning cannot be deleted from the web console. It is not automatically deleted along with a cluster being deleted. Therefore, before a cluster is deleted, all PVCs must be deleted first; otherwise, you may be charged for PVC usage. The reclaimPolicy of PV created by dynamic provisioning is set to `Delete` by default, so deleting only the PVC will also delete the PV and block storage.

<a id="pod-pvc-mount"></a>
### Mount PVC to a Pod { #pod-pvc-mount }

To mount PVC to a pod, mount information must be defined at the pod manifest. Enter the PVC name to use in `spec.volumes.persistenVolumeClaim.claimName` and enter paths to mount in `spec.containers.volumeMounts.mountPath`.

The example below mounts a PVC created by static provisioning to `/usr/share/nginx/html` of the pod.

```yaml
# pod-pvc.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-with-static-pv
spec:
  containers:
    - name: web
      image: nginx
      ports:
        - name: web
          containerPort: 80
          hostPort: 8082
          protocol: TCP
      volumeMounts:
        - name: html-volume
          mountPath: "/usr/share/nginx/html"
  volumes:
    - name: html-volume
      persistentVolumeClaim:
        claimName: pvc-static
```

Create a pod and verify that the block storage is mounted.

```
$ kubectl apply -f pod-static-pvc.yaml
pod/nginx-with-static-pv created

$ kubectl get pods
NAME                   READY   STATUS    RESTARTS   AGE
nginx-with-static-pv   1/1     Running   0          50s

$ kubectl exec -ti nginx-with-static-pv -- df -h
Filesystem      Size  Used Avail Use% Mounted on
...
/dev/vdc        9.8G   23M  9.7G   1% /usr/share/nginx/html
...
```

You can also check block storage attachment information in the **Storage > Block Storage** service page of the NHN Cloud web console.

<a id="volume-expansion"></a>
### Volume Expansion { #volume-expansion }
You can adjust an existing volume by editing the PersistentVolumeClaim (PVC) object. You can change the volume size by editing the **spec.resources.requests.storage** item of the PVC object. Volume shrinking is not supported. To use the volume expansion feature, the **allowVolumeExpansion** property of StorageClass must be **True**.


<a id="volume-expansion-from-v11913-and-older"></a>
#### Volume Expansion from v1.19.13 and Older
**kubernetes.io/cinder**, the storage provider from v1.19.13 and older does not provide the volume expansion feature for the volume in use. To use the feature for the volume in use, you must use **cinder.csi.openstack.org**, the storage provider from v1.20.12 and later. The cluster upgrade feature allows you to upgrade the version to v1.20.12 or later in order to use the storage provider **cinder.csi.openstack.org**.

To use the storage provider **cinder.csi.openstack.org** from v1.20.12 and later instead of the storage provider **kubernetes.io/cinder** from v1.19.13 and older, you must modify the annotations of PVC as follows.

* pv.kubernetes.io/bind-completed: "yes" > Delete
* pv.kubernetes.io/bound-by-controller: "yes" > Delete
* volume.beta.kubernetes.io/storage-provisioner: kubernetes.io/cinder > volume.beta.kubernetes.io/storage-provisioner:cinder.csi.openstack.org
* volume.kubernetes.io/storage-resizer: kubernetes.io/cinder > volume.kubernetes.io/storage-resizer: cinder.csi.openstack.org
* pv.kubernetes.io/provisioned-by:cinder.csi.openstack.org > Add


The following is an example of the modified PVC.

``` yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  annotations:
    pv.kubernetes.io/provisioned-by: cinder.csi.openstack.org
    volume.beta.kubernetes.io/storage-provisioner: cinder.csi.openstack.org
    volume.kubernetes.io/storage-resizer: cinder.csi.openstack.org
  creationTimestamp: "2022-07-18T06:13:01Z"
  finalizers:
  - kubernetes.io/pvc-protection
  labels:
    app: nginx
  name: www-web-0
  namespace: default
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 310Gi
  storageClassName: sc-ssd
  volumeMode: Filesystem
  volumeName: pvc-0da7cd55-bf29-4597-ab84-2f3d46391e5b
status:
  accessModes:
  - ReadWriteOnce
  capacity:
    storage: 300Gi
  phase: Bound
```

<a id="volume-expansion-from-v12012-and-older"></a>
#### Volume Expansion from v1.20.12 and Later
The storage provider **cinder.csi.openstack.org** from v1.20.12 and later supports the expansion of the volume in use by default. You can change the volume size by modifying the **spec.resources.requests.storage** item of the PVC object to a desired value.

<a id="service-integration"></a>
## NHN Cloud Service Integration { #service-integration }

<a id="ncr-integration"></a>
### NHN Cloud Container Registry (NCR) Service Integration { #ncr-integration }
You can use images saved at NHN Cloud Container Registry. To use images registered in the registry, first create a secret to login to user registry.

To use NHN Cloud (Old) Container Registry, you need to create a secret as follows:

```
$ kubectl create secret docker-registry registry-credential --docker-server={user registry address} --docker-username={NHN Cloud account email address} --docker-password={service Appkey or integrated Appkey}
secret/registry-credential created

$ kubectl get secrets
NAME                  TYPE                             DATA   AGE
registry-credential   kubernetes.io/dockerconfigjson   1      30m
```


To use NHN Cloud Container Registry, create a secret as follows:

```
$ kubectl create secret docker-registry registry-credential --docker-server={user registry address} --docker-username={User Access Key ID} --docker-password={Secret Access Key}
secret/registry-credential created

$ kubectl get secrets
NAME                  TYPE                             DATA   AGE
registry-credential   kubernetes.io/dockerconfigjson   1      30m
```


By adding the secret information to the deployment manifest file and changing the image name, you can create a pod using the image stored in the user registry.

```yaml
# nginx.yaml
...
spec:
  ...
  template:
    ...
    spec:
      containers:
      - name: nginx
        image: {User Registry Address}/nginx:1.14.2
        ...
      imagePullSecrets:
      - name: registry-credential
```

> [Note]
> For information on how to use NHN Cloud Container Registry, see the [NHN Cloud Container Registry (NCR) User Guide](/Container/NCR/en/user-guide) document.

<a id="nas-integration"></a>
### NHN Cloud NAS Service Integration { #nas-integration }
You can utilize NAS volumes provided by NHN Cloud as PV. In order to use NAS services, you must use a cluster of version v1.20 or later. For more information on using NHN Cloud NAS, please refer to the [NAS Console User Guide](/Storage/NAS%20(online)/en/console-guide).

> [Note]
> As of the current date (August 2024), the NHN Cloud NAS service is only available in select regions. For more information on the supported regions for the NHN Cloud NAS service, see the [NAS Service Overview](/Storage/NAS%20(online)/en/overview).

<a id="nas-integration-run-the-rpcbind-service-on-all-worker-nodes"></a>
#### Run the rpcbind Service on All Worker Nodes
To use NAS volumes, you must run the rpcbind service on all worker nodes. Connect to all worker nodes and run the rpcbind service by using the following command.

The command to run the rpcbind service is the same regardless of the image type.

```
$ systemctl start rpcbind
```

For clusters using enhanced security rules, additional security rules must be added.

| Direction | IP Protocol | Port Range | Ether | Remote | Description |
| :-: | :-: | :-: | :-: | :-: | :-: |
| egress | TCP | 2049 | IPv4 | NAS IP address | NFS port for rpc, direction: csi-nfs-node (worker node) → NAS |
| egress | TCP | 111 | IPv4 | NAS IP address | portmapper port for rpc, direction: csi-nfs-node (worker node) → NAS |
| egress | TCP | 635 | IPv4 | NAS IP address | mountd port for rpc, direction: csi-nfs-node (worker node) → NAS |

<a id="nas-integration-install-csi-driver-nfs"></a>
#### Install csi-driver-nfs
To use the NHN Cloud NAS service, you must deploy [nfs-csi-plugin](/Container/NKS/en/user-guide/#addon-mgmt-addon-list-nfs-csi-plugin) to the cluster using the Addon feature of NHN Kubernetes Service (NKS).

csi-driver-nfs is a driver that supports NFS storage provisioning that works by creating new subdirectories on NFS storage.
csi-driver-nfs works by presenting NFS storage information to storage classes, reducing what you have to manage.

If you configure multiple PVs using the csi-driver-nfs, the csi-driver-nfs registers the NFS storage information in the StorageClass, removing the need to configure an NFS-Provisioner pod.
<br>
![nfs-csi-driver-02.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/nfs-csi-driver-02.png)

<a id="nas-integration-how-to-use-existing-nhn-cloud-nas-volume-when-provisioning"></a>
#### How to use an existing NHN Cloud NAS volume when provisioning
You can use existing NAS storage as a PV by entering the NAS information when creating the PV manifest or by entering the NAS information in the StorageClass manifest.

##### Method 1. Define NAS volume information when creating a PV manifest
When creating the PV manifest, define the NHN Cloud NAS volume information. The setting location is **csi** under .spec.

* driver: Enter **nfs.csi.k8s.io**.
* readOnly: Enter **false**.
* volumeHandle: Enter a unique ID that does not conflict with other IDs in the cluster.
* volumeAttributes: Enter the connection information for the NAS volume.
  * server: Enter the value of the **ip** part from the NAS volume connection information.
  * share: Enter the value of the **volume name** part from the NAS volume connection information.

The following is a manifest example.
``` yaml
# pv.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-onas
spec:
  capacity:
    storage: 300Gi
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  csi:
    driver: nfs.csi.k8s.io
    readOnly: false
    volumeHandle: unique-volumeid
    volumeAttributes:
      server: 192.168.0.98
      share: /onas_300gb
```

Create and verify the PV.
```
$ kubectl apply -f pv.yaml
persistentvolume/pv-onas created

$ kubectl get pv -o wide
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM                      STORAGECLASS   REASON   AGE    VOLUMEMODE
pv-onas                                    300Gi      RWX            Retain           Available                                                      101s   Filesystem
```

Write a PVC manifest to use the created PV. The PV name must be specified in **spec.volumeName**. Set other items the same as the PV manifest.
```yaml
# pvc.yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-onas
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 300Gi
  volumeName: pv-onas
```

Create and verify the PVC.
```
$ kubectl apply -f pvc.yaml
persistentvolumeclaim/pvc-onas created

$ kubectl get pvc -o wide
NAME              STATUS   VOLUME    CAPACITY   ACCESS MODES   STORAGECLASS   AGE    VOLUMEMODE
pvc-onas   Bound    pv-onas   300Gi      RWX                           2m8s   Filesystem
```

After the PVC is created, query the PV status, and you can find the PVC name specified for the **CLAIM** field and the STATUS field changed to `Bound`.
```
$ kubectl get pv -o wide
NAME      CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                     STORAGECLASS   REASON   AGE     VOLUMEMODE
pv-onas   300Gi      RWX            Retain           Bound    default/pvc-onas                           3m20s   Filesystem
```

##### Method 2. Define NAS information when creating a StorageClass manifest
When creating the StorageClass manifest, define the storage provider information and the NHN Cloud NAS volume information.

* provisioner: Enter **nfs.csi.k8s.io**.
* parameters: Refer to the table below for the input items.

| Item | Description | Example | Required | Default |
| ------- |------- | --------------------------- | ---------------------------- | ------------- |
| server | Specifies the **ip** part of the NAS volume connection information. | 192.168.0.81 | O |  |
| share | Specifies the **volume name** part of the NAS volume connection information. | /onas_300gb | O |  |
| mountPermissions | Specifies the permissions to set on the NAS volume mount point directory. | "0700" | X | 0741 |
| uid | Enter the UID to set on the NAS volume mount point directory. | 1000 | X | root(0) |
| gid | Enter the GID to set on the NAS volume mount point directory. | 1000 | X | root(0) |

The following is a manifest example.
``` yaml
# storageclass.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: onas-sc
provisioner: nfs.csi.k8s.io
parameters:
  server: 192.168.0.81
  share: /onas_300gb
  mountPermissions: "0700"
  uid: 1000
  gid: 1000
reclaimPolicy: Retain
volumeBindingMode: Immediate
```

Create and verify the StorageClass.
```
$ kubectl apply -f storageclass.yaml
storageclass.storage.k8s.io/onas-sc created

$ kubectl get sc
NAME      PROVISIONER      RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
onas-sc   nfs.csi.k8s.io   Retain          Immediate           false                  3s
```

You don't need to create a PV separately; just create a PVC manifest. Do not set **spec.volumeName** in the PVC manifest.
```yaml
# pvc.yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-onas-dynamic
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 300Gi
  storageClassName: onas-sc
```
If you do not set the volume binding mode or set it to Immediate and create a PVC, the PV will be created automatically.

```
$ kubectl apply -f pvc.yaml
persistentvolumeclaim/pvc-onas created

$ kubectl get sc,pv,pvc
NAME                                  PROVISIONER      RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
storageclass.storage.k8s.io/onas-sc   nfs.csi.k8s.io   Retain          Immediate           false                  25s

NAME                                                        CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                      STORAGECLASS   REASON   AGE
persistentvolume/pvc-71392e58-5d8e-43b2-9798-5b59de34b203   300Gi      RWX            Retain           Bound    default/pvc-onas   onas-sc                 3s

NAME                                     STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
persistentvolumeclaim/pvc-onas   Bound    pvc-71392e58-5d8e-43b2-9798-5b59de34b203   300Gi      RWX            onas-sc        4s
```

To mount PVC to a pod, mount information must be defined in the pod manifest. Enter the PVC name to use in **spec.volumes.persistenVolumeClaim.claimName** and enter the path to mount in **spec.containers.volumeMounts.mountPath**.

The following is a manifest example that mounts the created PVC to `/tmp/nfs` of the pod.
```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: nginx
  namespace: default
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
        volumeMounts:
          - name: onas-dynamic
            mountPath: "/tmp/nfs"
      volumes:
        - name: onas-dynamic
          persistentVolumeClaim:
            claimName: pvc-onas-dynamic
```

Create the pod and verify that the NAS volume is mounted.
```
$ kubectl apply -f deployment.yaml
deployment.apps/nginx created

$ kubectl get pods
NAME                             READY   STATUS    RESTARTS   AGE
nginx-5fbc846574-q28cf   1/1     Running   0          26s

$ kubectl exec -it nginx-5fbc846574-q28cf -- df -h
Filesystem                                                                 Size  Used Avail Use% Mounted on
...
192.168.0.45:/onas_300gb/pvc-71392e58-5d8e-43b2-9798-5b59de34b203  270G  256K  270G   1% /tmp/nfs
...
```

<a id="nas-integration-how-to-create-new-nhn-cloud-nas-volume-when-provisioning"></a>
#### How to create a new NHN Cloud NAS volume during provisioning
You can use an automatically created NAS volume as a PV by entering NAS information when creating a StorageClass and PVC manifest.

Define the storage provider information, snapshot policy, access control list (ACL), and subnet information for the NAS volume to be created in the StorageClass manifest.

* provisioner: Enter **nfs.csi.k8s.io**.
* parameters: Refer to the table below for input items. When defining multiple values for a parameter, separate the values with **,**.

| Item | Description | Example | Multiple values | Required | Default |
| ------- |------- | --------------------------- | ---------------------------- | --------- | ------------- |
| maxscheduledcount | The maximum number of snapshots that can be stored. When the maximum number of saves is reached, the first automatically created snapshot is deleted. Only numbers between 1 and 20 can be entered. | "7" | X | X |  |
| reservepercent | The maximum snapshot storage capacity. If the total snapshot capacity exceeds the set size, the first snapshot created is deleted. Only numbers between 0 and 80 can be entered. | "80" | X | X |  |
| scheduletime | The time at which snapshots are created. | "09:00" | X | X |  |
| scheduletimeoffset | The offset for the snapshot creation time. Based on UTC; specify +09:00 for KST. | "+09:00" | X | X |  |
| scheduleweekdays | The snapshot creation cycle. Each day from Sunday to Saturday is represented by numbers 0 through 6. | "6" | O | X |  |
| subnet | The subnet to access the storage. Only subnets in the selected VPC can be selected. | "59526f1c-c089-4517-86fd-2d3dac369210" | X | O |  |
| acl | A list of IP addresses or IP ranges that are allowed read and write access. | "0.0.0.0/0" | O | X | 0.0.0.0/0 |
| onDelete | Whether to delete the NAS volume when the PVC is deleted. | "delete" / "retain" | X | X | delete |
| mountPermissions | Specifies the permissions to set on the NAS volume mount point directory. | "0700"| X | X | 0741 |
| uid | Enter the UID to set on the NAS volume mount point directory. | 1000 | X | X | root(0) |
| gid | Enter the GID to set on the NAS volume mount point directory. | 1000 | X | X | root(0) |

> [Note]
> When using snapshot parameters, you must define all related parameter values. The snapshot-related parameters are as follows:
> + maxscheduledcount
> + reservepercent
> + scheduletime
> + scheduletimeoffset
> + scheduleweekdays

<br>

> [Caution] Constraints in multi-subnet environments
> 
> A NAS volume is connected to the subnet defined in the StorageClass.
> For a pod to work with the NAS volume, all worker node groups must be connected to this subnet.

The following is an example manifest.
```yaml
# storage_class.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: sc-nfs
provisioner: nfs.csi.k8s.io
reclaimPolicy: Delete
volumeBindingMode: Immediate
parameters:
  maxscheduledcount : "7"
  reservepercent : "80"
  scheduletime : "09:00"
  scheduletimeoffset : "+09:00"
  scheduleweekdays : "6"
  subnet : "59526f1c-c089-4517-86fd-2d3dac369210"
  acl : ""
  mountPermissions: "0700"
  uid: 1000
  gid: 1000
```

Define the name, description, and size of the NAS volume to be created in the **Annotation** of the PVC manifest. Refer to the table below for input items.

| Item | Description | Example | Required |
| ---- | ------- | --------------------------- | --------- |
| nfs-volume-name | Name of the storage to be created. The NFS access path can be created with the storage name. Storage name is limited to less than 100 alphabetic characters, numbers, and some symbols ('-', '_'). | "nas_sample_volume_300gb" | O |
| nfs-volume-description | Description of the NAS volume to be created. | "nas sample volume" | X |
| nfs-volume-sizegb | The size of the NAS volume to be created. Set in GB units. You can enter a value from a minimum of 300 to a maximum of 10,000. | "300" | O |

The following is an example manifest.
```yaml
# pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
  annotations:
    nfs-volume-name: "nas_sample_volume_300gb"
    nfs-volume-description: "nas sample volume"
    nfs-volume-sizegb: "300"
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 50Gi
  storageClassName: sc-nfs
```

Create and verify the StorageClass and PVC.
```
$ kubectl apply -f storage_class.yaml
storageclass.storage.k8s.io/sc-nfs created

$ kubectl get sc
NAME         PROVISIONER      RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
sc-nfs       nfs.csi.k8s.io   Delete          Immediate           false                  50s
```

You don't need to create a PV separately; just create a PVC manifest. Do not set **spec.volumeName** in the PVC manifest.
If you do not set the volume binding mode or set it to Immediate and create a PVC, the PV will be created automatically. After the NAS volume is created, it takes about 1 minute to be bound.
You can also view the NAS volume information you created on the **Storage > NAS** Service page in the NHN Cloud console.

```
$ kubectl apply -f pvc.yaml
persistentvolumeclaim/pvc-nfs created

$ kubectl get pv,pvc
NAME                                                        CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM             STORAGECLASS   REASON   AGE
persistentvolume/pvc-a8ea2054-0849-4fe8-8207-ee0e43b8a103   50Gi       RWX            Delete           Bound    default/pvc-nfs   sc-nfs                  2s

NAME                            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
persistentvolumeclaim/pvc-nfs   Bound    pvc-a8ea2054-0849-4fe8-8207-ee0e43b8a103   50Gi       RWX            sc-nfs         75s
```

To mount a PVC to a pod, you must define the mount information in the pod manifest. Enter the PVC name to use in **spec.volumes.persistenVolumeClaim.claimName**. Then enter the path to mount in **spec.containers.volumeMounts.mountPath**.

The following is an example manifest that mounts the created PVC to `/tmp/nfs` on the pod.
```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: nginx
  namespace: default
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
        volumeMounts:
          - name: nas
            mountPath: "/tmp/nfs"
      volumes:
        - name: nas
          persistentVolumeClaim:
            claimName: pvc-nfs
```

Create the pod and verify that the NAS volume is mounted.
```
$ kubectl apply -f deployment.yaml
deployment.apps/nginx created

$ kubectl get pods
NAME                             READY   STATUS    RESTARTS   AGE
nginx-9f448b9f7-xw92w   1/1     Running   0          12s

$ kubectl exec -it nginx-9f448b9f7-xw92w -- df -h
Filesystem                                                                     Size  Used Avail Use% Mounted on
overlay                                                                         20G   16G  4.2G  80% /
tmpfs                                                                           64M     0   64M   0% /dev
tmpfs                                                                          1.9G     0  1.9G   0% /sys/fs/cgroup
192.168.0.57:nas_sample_volume_100gb/pvc-a8ea2054-0849-4fe8-8207-ee0e43b8a103   20G  256K   20G   1% /tmp/nfs
...
```

> [Note]
> csi-driver-nfs works by creating a subdirectory inside the NFS storage when provisioning.
> In the process of mounting the PV to the pod, not only the subdirectory is mounted, but the entire NFS storage is mounted, so it is not possible to force the application to use the volume by the provisioned size.

<a id="encrypted-block-storage-integration"></a>
### NHN Cloud Encrypted Block Storage Integration { #encrypted-block-storage-integration }
You can utilize encrypted block storage provided by NHN Cloud as PV. For more information about NHN Cloud encrypted block storage, see [Encrypted Block Storage](/Storage/Block%20Storage/en/console-guide/#_2).

> [Note]
> The Encrypted Block Storage service integration is available for clusters running v1.24.3 or later.
> Newly created clusters on or after November 28, 2023, have the Encrypted Block Storage integration feature built in by default.
> Clusters created before November 28, 2023, can enable encrypted block storage integration by upgrading to v1.24.3 or later, or by replacing the cinder-csi-plugin images in the csi-cinder-controllerplugin statefulset and csi-cinder-nodeplugin daemonset with newer versions.

> [Caution]
> If you are using a cluster with a version prior to v1.24.3 without upgrading it and just replacing the cinder-csi-plugin container image, it can cause malfunctions.

<a id="encrypted-block-storage-integration-updating-cinder-csi-plugin-image-for-encrypted-block-storage-integration"></a>
#### Update the cinder-csi-plugin Image for Encrypted Block Storage Integration
Run the following command to check the tag of the cinder-csi-plugin image currently deployed in your cluster.

```
$ kubectl -n kube-system get statefulset csi-cinder-controllerplugin -o=jsonpath="{$.spec.template.spec.containers[?(@.name=='cinder-csi-plugin')].image}"

> registry.k8s.io/provider-os/cinder-csi-plugin:v1.27.101
```

If the tag in the cinder-csi-plugin image is v1.27.101 or later, you can integrate encrypted block storage without taking any action.
If the tag of the cinder-csi-plugin image is less than v1.27.101, you can update the image of the cinder-csi-plugin using the steps below and then integrate encrypted block storage.

| Region | Internet connection | cinder-csi-plugin image |
| --- | --- | --- |
| Korea (Pangyo) region | O | dfe965c3-kr1-registry.container.nhncloud.com/container_service/cinder-csi-plugin:v1.27.101 |
| | X | private-dfe965c3-kr1-registry.container.nhncloud.com/container_service/cinder-csi-plugin:v1.27.101 |
| Korea (Pyeongchon) region | O | 6e7f43c6-kr2-registry.container.cloud.toast.com/container_service/cinder-csi-plugin:v1.27.101 |
| | X | private-6e7f43c6-kr2-registry.container.cloud.toast.com/container_service/cinder-csi-plugin:v1.27.101 |
| Korea (Gwangju) region | O | d6628457-kr3-registry.container.nhncloud.com/container_service/cinder-csi-plugin:v1.27.101 |
| | X | private-d6628457-kr3-registry.container.nhncloud.com/container_service/cinder-csi-plugin:v1.27.101 |

##### 1. Enter the correct cinder-csi-plugin image value for container_image.
```
$ container_image={cinder-csi-plugin image}
```

##### 2. Replace the container image.
```
$ kubectl -n kube-system patch statefulset csi-cinder-controllerplugin -p "{\"spec\": {\"template\": {\"spec\": {\"containers\": [{\"name\": \"cinder-csi-plugin\", \"image\": \"${container_image}\"}]}}}}"

$ kubectl -n kube-system patch daemonset csi-cinder-nodeplugin -p "{\"spec\": {\"template\": {\"spec\": {\"containers\": [{\"name\": \"cinder-csi-plugin\", \"image\": \"${container_image}\"}]}}}}"
```

> [Note]
> The cinder-csi-plugin container image is maintained in NHN Cloud NCR. Since the cluster configured in a closed network environment is not connected to the Internet, it is necessary to configure the environment to use a private URI in order to receive images normally. For information on how to use Private URI, refer to the [NHN Cloud Container Registry (NCR) User Guide](/Container/NCR/en/user-guide/#private-uri).


<a id="encrypted-block-storage-integration-static-provisioning"></a>
#### Static Provisioning
To create a PV, you need the ID of the encrypted block storage. On the Storage > Block Storage service page, select the block storage you want to use from the block storage list. You can find the ID under the block storage name section in the **Information** tab at the bottom.

When creating a PV manifest, enter the encrypted block storage information. The settings are located under **.spec.csi**.

* driver: Enter `cinder.csi.openstack.org`.
* fsType: Enter `ext3`.
* volumeHandle: Enter the ID of the encrypted block storage that you created.

The following is an example manifest.
```yaml
# pv-static.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  annotations:
    pv.kubernetes.io/provisioned-by: cinder.csi.openstack.org
  name: pv-static-encrypted-hdd
spec:
  capacity:
    storage: 10Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Delete
  csi:
    driver: cinder.csi.openstack.org
    fsType: ext3
    volumeHandle: 9f606b78-256b-4f74-8988-1331cd6d398b
```

The process of creating a PVC manifest and mounting it to a Pod is the same as static provisioning for general block storage. For more information, see [Static Provisioning](/Container/NKS/en/user-guide/#static-provisioning).

<a id="encrypted-block-storage-integration-dynamic-provisioning"></a>
#### Dynamic Provisioning
When creating a storage class manifest, you can enter the information required to create encrypted block storage and use the automatically created encrypted block storage as a PV.

Enter the information required to create encrypted block storage in the storage class manifest. The settings are located under **.parameters**.

* Storage type (type): Enter the type of storage.
    * **Encrypted HDD**: Sets the storage type to encrypted HDD.
    * **Encrypted SSD**: Sets the storage type to encrypted SSD.
* Encryption key ID (volume_key_id): Enter the ID of the symmetric key created in the Secure Key Manager (SKM) service.
* Encryption app key (volume_appkey): Enter the Appkey confirmed in the Secure Key Manager (SKM) service.

The following is an example manifest.
```yaml
# storage_class.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: csi-storageclass-encrypted-hdd
provisioner: cinder.csi.openstack.org
volumeBindingMode: Immediate
allowVolumeExpansion: true
parameters:
  type: Encrypted HDD
  volume_key_id: "5530..."
  volume_appkey: "uaUW..."
```

The process of creating a PVC manifest and mounting it to a Pod is the same as dynamic provisioning for general block storage. For more information, see [Dynamic Provisioning](/Container/NKS/en/user-guide/#dynamic-provisioning).


<a id="etcd-encryption-with-skm"></a>
### Integrate Secure Key Manager for Encrypting and Decrypting Sensitive Data { #etcd-encryption-with-skm }

NKS clusters encrypt data when storing secret resources in the data store (etcd). NKS provides two methods for encrypting this data.

<a id="etcd-encryption-with-skm-standard"></a>
#### Standard Method

* A symmetric key is automatically generated and stored in the control plane when a cluster is created.
* etcd data is encrypted with that key.
* Key management is handled internally within the cluster.

<a id="etcd-encryption-with-skm-skm-integration"></a>
#### SKM Integration Method

* Sets the storage encryption provider to Secure Key Manager (SKM).
* Encryption and decryption of etcd data is performed through the SKM API.
* Enables centralized key management and audit logging.

> [Caution]
> Deleting the SKM symmetric key associated with the cluster or a rotated key version will cause the cluster to malfunction.
> * Cluster startup failure due to inability to decrypt etcd data
> * Inability to access encrypted resources
> * Possible unrecoverable data loss

> Note: How to safely delete a rotated key version
> Forcing all secret resource data to be read and rewritten with the new key re-encrypts the data with the latest key version.
> After re-encrypting all secret resource data with the following command, you can safely delete the rotated key version.
> `kubectl get secrets --all-namespaces -o json | kubectl replace -f -`
