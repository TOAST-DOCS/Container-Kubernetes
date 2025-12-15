## Container > NHN Kubernetes Service (NKS) > User Guide

<a id="cluster-headings"></a>
## Cluster
Cluster refers to a group of instances that comprise user's Kubernetes.

<a id="cluster-create"></a>
### Creating Clusters
To use NHN Kubernetes Service (NKS), you must create clusters first.

> [Caution] Setting up permissions to use clusters<br>
> To create a cluster, the user must have **Infrastructure ADMIN**, **Infrastructure LoadBalancer ADMIN**, or **Infrastructure NKS ADMIN** permissions of basic infrastructure services for the project.
Only with the permissions, the user can normally create and operate clusters running on basic infrastructure services. It is totally possible to add one of the two permissions when the other is already acquired.
To learn more about setting up permissions, see [Manage Project Members](/nhncloud/en/console-user-guide/#_3).

Go to **Container > NHN Kubernetes Service(NKS)** and click **Create Cluster** and a page for creating clusters shows up. The following items are required to create a cluster.

| Item | Description |
| --- | --- |
| Cluster Name | Name of a Kubernetes cluster. It is limited to 20 characters, and only lowercase English letters, numbers, and '-' can be entered. It must start with a lowercase letter and end with a lowercase letter or number. RFC 4122 compliant UUID formats cannot be used. |
| Kubernetes Version | Kubernetes version to use |
| VPC | VPC network to be attached to clusters |
| Subnet | Subnet to be associated with instances that comprise a cluster, among those defined in VPC |
| NCR Service Gateway | NCR Type Service Gateway<br>(but only if your subnet is not connected to the internet gateway |
| OBS Service Gateway | OBS Type Service Gateway<br>(but only if your subnet is not connected to the internet gateway |
| K8s Service Network | Service object CIDR for a cluster |
| Pod Network | Pod network for a cluster |
| Pod Subnet Size | Pod subnet size for a cluster |
| Kubernetes API endpoint | Public: Endpoint assigned with the domain address and associated with a floating IP <br>Private: Endpoint set to an internal network address |
| Enhanced Security Rule | Create only required security rules when creating a worker node security group. See C<br>True: Create only required security rules<br>False: Create required security rules and security rules that allow all ports|
| Image | Images for instances comprising a cluster |
| Availability Zone | Area to create a default node group instance |
| Flavor | Instance specifications for a default node group |
| Node Count | Number of instances for a default node group |
| Key Pair | Key pair to access default node group |
| Block Storage Type | Type of block storage for a default node group instance |
| Block Storage Size | Size of block storage for a default node group instance |
| Additional Network | Additional network and subnet to create in a default worker node group |

Enter information as required and click **Create Cluster**, and a cluster begins to be created. You can check the status from the list of clusters. It takes about 10 minutes to create; more time may be required depending on the cluster configuration.

> [Caution]
> For CIDRs of VPC network subnet, K8s service network and pod network, the following constraints must be avoided.
>  - CIDR cannot overlap with the link-local address band (169.254.0.0/16).
>  - CIDR cannot overlap with bands of the VPC network subnet or additional network subnets, pod network, and K8s service network.
>  - CIDR cannot overlap with the IP band (198.18.0.0/19) being used inside the NKS.
>  - You cannot enter a CIDR block greater than /24. (The following CIDR blocks are not available: /26, /30).
>  - For clusters of v1.23.3 or earlier, they cannot overlap with BIP (bridged IP range) (172.17.0.0/16).
>
> You should not delete the service gateway that you set up when you created the cluster.
>  - If the selected subnet is not connected to an internet gateway, you need to set up NCR service gateway and OBS service gateway.
>  - These two service gateways are used to get the images/binaries required for NKS cluster configuration and basic features.
>  - If you delete the service gateway that was set up when the cluster was created, the cluster will not work properly.
>
> You should not change the Internet gateway connectivity for the subnet you set when you created the cluster.
>  - The registry to receive the image/binary depends on whether the subnet you set up when creating the cluster is connected to the internet gateway or not.
>  - If the subnet's Internet gateway connectivity changes after the cluster is created, the cluster will not work properly because it cannot connect to the set registry.

> [Maximum number of nodes that can be created]
> The maximum number of nodes that can be created when creating a cluster is determined by the pod network, pod subnet size settings.
> Calculation: 2^(Pod Subnet Size - Host Bits in Pod Network) - 3
> Example:
>  - Pod subnet size = 24
>  - Pod network = 10.100.0.0/16
>  - Calculation: 2 ^ (24 - 16) - 3 = up to 253 nodes can be created

> [Maximum number of IPs that can be assigned to the Pod for each node]
The maximum number of IPs that can be used on a single node is determined by the Pods subnet size setting.
Calculation: 2^(32 - pods_network_subnet) - 2
Example:
>  - Pod subnet size = 24
>  - Calculation: 2 ^ (32 - 24) - 2 = up to 254 IPs available

> [Maximum number of IPs that can be assigned to Pods in the cluster]
> Calculation: Maximum number of IPs that can be assigned to a Pod for each node * Maximum number of nodes that can be created
> Example:
>  - Pod subnet size = 24
>  - Pod network = 10.100.0.0/16
>  - Calculate: 254 (maximum number of IPs that can be assigned to a Pod for each node) * 253 (maximum number of nodes that can be created) = 64,262 IPs available at most

<a id="cluster-show"></a>
### Querying Clusters
A newly created cluster can be found in the **Container > NHN Kubernetes Service (NKS)** page. Select a cluster and the information is displayed at the bottom.

| Item | Description |
| --- | --- |
| Cluster Name | Cluster name |
| Node Count | Total number of worker nodes in the cluster |
| Kubernetes Version | Kubernetes version information |
| kubeconfig file | Button to download the kubeconfig file to control the cluster |
| Task Status | Task status for the command to the cluster |
| k8s API status | Behavioral status of Kubernetes API endpoints |
| k8s Node Status | Status of Kubernetes Node resources |

The meaning of each icon of the task status is as follows.

| Icon | Meaning |
| --- | --- |
| Green solid icon | Normal end of task|
| Circular rotation icon | Task in progress |
| Red solid icon | Task failed |
| Gray solid icon | Cluster unavailable |

The meaning of each icon in the k8s API status is as follows:

| Icon | Meaning |
| --- | --- |
| Green solid icon | Working properly|
| Yellow solid icon | Information is not accurate because it nears expiration (5 minutes) |
| Red solid icon | Kubernetes API endpoint is not working properly or information expired |

The meaning of each icon in k8s Node status is as follows.

| Icon | Meaning |
| --- | --- |
| Green solid icon | All nodes in the cluster are in the Ready state |
| Yellow solid icon | Kubernetes API endpoints are not working properly or there are nodes in NotReady in the cluster |
| Red solid icon | All nodes in cluster are in NotReady |

When you select a cluster, cluster information appears at the bottom.

| Item | Description |
| --- | --- |
| Cluster Name | Name and ID of Kubernetes Cluster |
| Node Count | Number of instances of all nodes comprising a cluster |
| Kubernetes Version | Kubernetes version in service |
| Kubernetes Certificate | Expiration date of cluster certificate |
| CNI | Kubernetes CNI type in service |
| K8s Service Network | CIDR of the cluster'service object |
| Pod Network | Kubernetes pod network in service |
| Pod Subnet Size | Kubernetes pod subnet size in service |
| VPC | VPC network attached to cluster |
| Subnet | Subnet associated to a node instance comprising a cluster |
| API Endpoint | URI of API endpoint to access cluster for operation |
| Configuration File | Download button of configuration file required to access cluster for operation |

<a id="cluster-delete"></a>
### Deleting Clusters
Select a cluster to delete, and click **Delete Clusters** and it is deleted. It takes about 5 minutes to delete. More time may be required depending on the cluster status.

<a id="change-keypair"></a>
### Change Cluster Key Pair

Change the key pair of all worker nodes in the cluster. To set the key pair, select one of the key pairs of the logged-in user. When you change the key pair, the following applies

* The selected key pair is set on all worker node VMs.
* You can access all worker node VMs via SSH by using the set key pair.
* The key pair for each worker node instance appears as `managed-by-nks`.

A cluster with key pairing operates with the permissions of a service user. A service user is an internal user managed at the NKS service level, and the functional behavior and service integration of NKS operates with the service user's permissions. Clusters operating under the service user's permissions do not need to change/manage the owner.

> [Caution]
> * Clusters that are set up with a regular user as the owner can be changed to act as a service user through the Change key pair feature.
> * The Change cluster owner feature is no longer available. If you want the cluster to act as a service user, use the Change key pair feature.

<a id="nodegroup-headings"></a>
## Node Group
A node group is comprised of worker node instances that comprise a Kubernetes.

<a id="nodegroup-show"></a>
### Querying Node Groups
Click a cluster name on the list to find the list of node groups. Select a node group and the brief information shows at the bottom.

| Item | Description |
| --- | --- |
| Node Group Name | Name of the node group |
| Node Count | The number of nodes in a node group |
| Kubernetes Version | Kubernetes version information applied to the node group |
| Availability Zone | Availability zone information applied to node groups |
| Flavor | Node group instance type |
| Image Type | Node group image type |
| Task Status | Task status for command to the node group |
| k8s Node Status | Status of Kubernetes Node resources belonging to a node group |

The meaning of each icon of the task status is as follows.

| Icon | Meaning |
| --- | --- |
| Green solid icon | Normal end of task |
| Circular rotation icon | Task in progress |
| Red solid icon | Task failed |
| Orange solid icon | Some node operations succeeded |
| Gray solid icon | Clusters and node groups unavailable |

The meaning of each icon of k8s Node status is as follows:

| Icon | Meaning |
| --- | --- |
| Green solid icon | All nodes in the node group are in the Ready state |
| Yellow solid icon | The Kubernetes API endpoint is not working properly or there are nodes in the NotReady status in the node group |
| Red solid icon | All nodes in the node group are in the NotReady status |

When you select a node group, node group information appears at the bottom.

* Basic Information
On the **Basic Information** tab, you can check the following:

| Item | Description |
| --- | --- |
| Node Group Name | Name and ID of a node group |
| Cluster Name | Name and ID of a cluster to which a node group is included |
| Kubernetes Version | Kubernetes version in service |
| Availability Zone | Area in which a node group instance is created |
| Flavor | Specifications of a node group instance |
| Image Type | Type of image for a node group instance |
| Block Storage Size | Size of block storage for a node group instance |
| Created Date | Time when node group was created |
| Modified Date | Last time when node group was modified |

* List of Nodes
Find the list of instances comprising a node group from the **List of Nodes** tab.

<a id="nodegroup-create"></a>
### Creating Node Groups
Along with a new cluster, a default node group is created, but more node groups can be created depending on the needs. If higher specifications are required to run a container, or more worker node instances are required to scale out, node groups may be additionally created. Click **Create Node Groups** from the page of node group list, and the page of creating a node group shows up. The following items are required to create a node group:

| Item | Description |
| --- | --- |
| Availability Zone | Area to create instances comprising a cluster |
| Node Group Name | Name of an additional node group. It is limited to 20 characters, and only lowercase English letters, numbers, and '-' can be entered. It must start with a lowercase letter and end with a lowercase letter or number. RFC 4122 compliant UUID formats cannot be used. |
| Flavor | Specifications of an instance for additional node group |
| Node Count | Number of instances for additional node group |
| Key Pair | Key pair to access additional node group |
| Block Storage Type | Type of block storage of instance for additional node group |
| Block Storage Size | Size of block storage of instance for additional node group |
| Additional Network | Additional network and subnet to create in a default worker node group |

Enter information as required and click **Create Node Groups**, and a node group begins to be created. You may check status from the list of node groups. It takes about 5 minutes to create; more time may be required depending on the node group setting.

>[Caution]
Only the user who created the cluster can create node groups.

<a id="nodegroup-delete"></a>
### Deleting Node Groups
Select a node group to delete from the list of node groups, and click **Delete Node Groups** and it is deleted. It takes about 5 minutes to delete a node group; more time may be required depending on the node group status.

<a id="nodegroup-scale-out"></a>
### Adding node to node group
Nodes can be added to operating node groups. The current list of nodes will appear upon clicking the node list tab on the node group information query page. Nodes can be added by selecting the Add Node button and entering the number of nodes you want.

>[Caution]
Nodes cannot be manually added to node groups on which autoscaler is enabled.

<a id="nodegroup-scale-in"></a>
### Deleting node from node group
Nodes can be deleted from operating node groups. The current list of nodes will appear upon clicking the node list tab on the node group information query page. A confirmation dialog box will appear when a user selects nodes for deletion and clicks the Delete Node button. When the user confirms the node name and selects the OK button, the node will be deleted.

>[Caution]
Pods that were operating in the deleted node will go through force shutdown. To safely transfer pods from the currently operating nodes to be deleted to other nodes, you must run the "drain" command. New pods can be scheduled on nodes even after they are drained. To prevent new pods from being scheduled in nodes that are to be deleted, you must run the "cordon" command. For more information on safe node management, see the document below.

>[Caution]
Nodes cannot be manually deleted from node groups on which autoscaler is enabled.

* [Safe node drain](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/)
* [Manual node management](https://kubernetes.io/docs/concepts/architecture/nodes/#manual-node-administration)

<a id="node-start-stop"></a>
### Stop and start node
Nodes can be stopped from node groups and started again. The current list of nodes will appear upon clicking the node list tab on the node group information query page. Nodes can be stopped when a user selects nodes and click the stop button. The stopped nodes can be restarted when the user select them and click the start button.

#### Action process

When you stop a node that is started, the node operates in the following order.

* The node is drained
* The node is deleted from Kubernetes node resources.
* Turn the node into the SHUTDOWN status at the instance level.

If you start a stopped node, it operates in the following order.

* The node becomes the ACTIVE status at the instance level.
* The node is added to Kubernetes node resources again.


#### Constraints

Stop and start node feature has the following constraints.

* You can stop a node that is started, and can start a stopped node.
* You cannot stop all nodes from the worker node group.
* Nodes cannot be stopped from node groups on which autoscaler is enabled.
* Autoscaler cannot be enabled when the node group contains stopped nodes.
* You cannot upgrade node groups that contain stopped nodes.


#### Display status

The status icon is displayed according to the node status on the node list tab. The status colors are as follows.

* Green: A node in the start status
* Gray: A node in the stop status
* Red: A node in the abnormal status

<a id="use-gpu-nodegroup"></a>
### Using a GPU node group 
When you need to run GPU-based workloads through Kubernetes, you can create a node group composed of GPU instances.
Select the `g2` type when selecting a flavor while creating the clusters or node groups to create a GPU node group.

> [Note]
GPU provided by NHN Cloud GPU instance is affiliated with NVIDIA. ([Identify available GPU specifications that can be used](/Compute/GPU%20Instance/en/overview/#gpu-specifications))
nvidia-device-plugin required for Kubernetes to use an NVIDIA GPU will be installed automatically when creating a GPU node group.

To check the default setting and run a simple operation test for the created GPU node, use the following method:

#### Node level status check
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

#### Kubernetes level status check
Use the `kubectl` command to view information about available GPU resources at the cluster level.
Below are commands and execution results that displays the number of GPU cores available for each node.

```
$ kubectl get nodes -A -o custom-columns='NAME:.metadata.name,GPU Allocatable:.status.allocatable.nvidia\.com/gpu,GPU Capacity:.status.capacity.nvidia\.com/gpu'
NAME                                       GPU Allocatable   GPU Capacity
my-cluster-default-w-vdqxpwisjjsk-node-1   1                 1
```

#### Sample workload execution for GPU testing
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

You should see the following results if you run the above file.

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
To prevent workloads that do not require GPU from being allocated to GPU nodes, see [Taints and Tolerations](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/).

<a id="autoscaler"></a>
### Autoscaler
An autoscaler is a feature that automatically adjusts the number of nodes when a node group runs out of available resources or when the utilization of when node utilization stays below a certain threshold. It can be configured for each node group and operates independently. NKS supports two types of autoscalers.

* Metric-based autoscaler
* Cluster Autoscaler

The Autoscaler is set up and operates on a per-node group basis. The feature can be set up with the path below

* Set up on the default node groups when creating a cluster.
* Set up on additional node groups when adding the node groups.
* Set up on existing node groups.

> [Caution]
Nodes cannot be manually added to or removed from node groups on which autoscaler is enabled.
Autoscaler cannot be enabled multiple times. 

**Glossary**
Terms used in relation to the autoscaler and their meanings are as follows:

| Term | Meaning |
| --- | --- |
| Scale Up | Increase a number of nodes. |
| Scaling Down | Decrease a number of nodes. |

<a id="metric-base-autoscaler"></a>
#### Metric-based autoscaler
The metrics-based autoscaler operates based on NHN Cloud's [Cloud Monitoring](/Monitoring/Cloud%20Monitoring/ko/overview/) service. A metric collection agent installed on the worker nodes sends system metrics to Cloud Monitoring at one-minute intervals, and automatically adds or removes nodes when the collected metrics exceed or fall below the thresholds you set. The Scale Up and Scale Down features can be enabled independently of each other.

<a id="metric-base-autoscaler-set"></a>
##### Metric-based Autoscaler Settings
When enabling metric-based autoscaler, you can set the following items:

**Scale-Up Settings**

| Settings Item | Meaning | Valid Range | Default |
| --- | --- | --- | --- |
| Maximum Node Count | Maximum number of nodes that can be scaled up| 1-10 | 10 |
| Enabled | Enable or disable the node scale-up autoscaler | Enable/Disable | Disable |

**Scale-Down Settings**

| Settings Item | Meaning | Valid Range | Default |
| --- | --- | --- | --- |
| Minimum Node Count | Minimum number of nodes that can be scaled down| 1-10 | 10 |
| Enabled | Configure whether to enable or disable the node scale-down autoscaler | Enable/Disable | Disable |

**Common Settings**

| Settings Item | Meaning | Valid Range | Default | Unit |
| --- | --- | --- | --- | --- |
| Rule operators | Setting the operators applied between auto-scaling triggering conditions<br>AND: Triggered when all conditions are met<br>OR: Triggered if either is met | AND/OR | OR | - |
| Autoscaling latency | Minimum wait time before the next scaling operation after the previous one completes (can be configured separately for scale-up and scale-down)| 1 - 60 | 10 | minutes |
| Node performance metrics | Setting the metrics to monitor (see table below) | Metric types | Required settings | - |
| Number of node adjustments | Number of nodes to add/remove when autoscaling occurs | 1 - 10 | 1 | unit |
| Configure thresholds | Metric thresholds for triggering conditions | By metric | Required settings | - |
| Scale Down Unneeded Time| Run a scale action when a threshold condition lasts longer than a set time (2-60 minutes)| 2-60 | Required settings | minutes |

**Node performance metrics**

| System resources | Statistical data provided | Unit |
| --- | --- | --- |
| CPU usage | Average CPU usage of all nodes in the node group | % |
| Memory usage | Average memory usage of all nodes in the node group | % |
| Disk transfer rate (read) | The average amount of disk read data per second across all nodes in the node group. | Bytes/s |
| Disk transfer rate (write) | The average amount of data written to disk per second across all nodes in the node group. | Bytes/s |
| Network transfer rate (Send) | The average amount of data sent to the network per second across all instances in the scaling group. | Bytes/s |
| Network transmission rate (receive) | The average amount of data received from the network per second across all instances in the scaling group. | Bytes/s |

<a id="metric-base-autoscaler-resize"></a>
##### Scale-Up/Down Conditions
Scales up if all of the following conditions are met:

* Selected node performance metrics have exceeded the threshold for longer than the scale down unneeded time.
* The current number of nodes is below the maximum limit.
* Autoscaling wait time elapsed

Scales down if all of the following conditions are met:

* Node performance metrics have remained below the threshold for longer than the scale down unneeded time.
* The current number of nodes exceeds the minimum limit.
* Autoscaling wait time elapsed

> [Note]
> Autoscaling wait time can be specified for both scaling up and/down policies.
> In general, specifying a short scale-up wait time allows you to respond immediately to sudden load increases.
> Conversely, set a long scale-down wait time to ensure stability by slowly reducing instances.
> You must continuously monitor the service load and configure appropriate policies to prevent unnecessary instance usage.
If only one specific node meets the condition, the policy will not be triggered. The evaluation is based on the average of all nodes in the node group.
Whether a policy is triggered is determined by continuously checking if the specified performance metric exceeds the threshold for the duration of the scale down unneeded time.
For example, if the condition is CPU utilization ≥ 90% and the scale down unneeded time is 5 minutes, the policy will be triggered only if the CPU utilization stays at or above 90% for the full 5 minutes.

> [Note: Node Scale-down]
> When the metric-based autoscaler performs scale-down, it removes the most recently created nodes first.

<a id="metric-base-autoscaler-example"></a>
##### Example

**Scale-up policy**

| Settings Item | Value |
| --- | --- |
| Maximum number of nodes | 7 units |
| Adjusting scale-up nodes | 3 units |
| Wait time after scale-up | 5 minutes |
| Scale-up conditions: Metrics | 3.10 |
| Scale-up conditions: Scale down unneeded time  | 5 minutes |
| Scale-up conditions: Threshold | 70% or more |

**Scale-down policy**

| Settings Item | Value |
| --- | --- |
| Minimum Node Count | 3 units |
| Adjusting scale-down nodes | 1 unit |
| Wait time after scale-down | 10 minutes |
| Scale-down conditions: Metrics | 3.10 |
| Scale-down conditions: Scale down unneeded time  | 2 min |
| Scale-down conditions: Threshold | 30% or less |

**Behavior summary**

* Number of nodes in current node group: 5
* A scale-up is triggered when the average CPU usage of 5 nodes stays above 70% for 5 minutes
* Although the node adjustment setting in the scale-up policy is 3 nodes, only 2 nodes were actually added because the maximum node count is set to 7 (Node count: 5 → 7)
* 5 minutes after the node scale-up was completed, the average CPU usage of the 7 nodes stayed below 30% for 2 minutes, triggering a scale-down request
* Request rejected because the wait time after scale-down is 10 minutes
* A scale-down operation was performed 10 minutes later
* Since the scale-down policy is configured to remove 2 nodes, 1 node was removed (Node count: 7 → 6)
* No additional scale-down occurs during the 10-minute wait time after a scale-down operation.

**Behavioral details**

| Time (minutes) | CPU Average | Node Count | Scale state | Description |
| ------ | ------ | ---- | ------ | --- |
| 0 – 3 | 65% | 5 | – | Below threshold (70%) |
| 4 | 72% | 5 | – | Scale-up condition threshold exceeded → Start measuring 5 minutes of scale down unneeded time  |
| 4 – 8 | 73% | 5 | – | The scale-up condition was met as the threshold was exceeded for 5 continuous minutes. |
| 8 | 76% | 5 → 7 | Requesting a scale-up operation | The scale-up node adjustment is set to 3 nodes, but the maximum node limit is 7 → actual +2 nodes<br>Node addition operation started |
| 8 – 13 | 65% | 7 | – | Node addition operation completed<br>13 minutes after the end of the operation is set as the start time of the 'Wait after scale-down/up' condition |
| 13 | 28% | 7 | – | Below the scale-up condition threshold → Start measuring 2 minutes of scale down unneeded time |
| 15 | 27% | 7 | Requesting a scale-down operation (rejected) | The scale-down condition was met as the threshold was exceeded for 2 continuous minutes<br>But rejected because of 10 minutes of post-reduction waiting (13→23) |
| 15 – 23 | 27% | 7 | – | Wait time after scale-down continues |
| 23 | 27% | 7 → 6 | Scale-Down | The 10-minute wait time after scale-down has expired, and the scale-down conditions are still met<br>Since the scale-down node adjustment is set to 1, one node was removed |
| 24 | 28% | 6 |  | Node scale-down operation completed<br>The time 24 minutes after the operation ended is set as the start point of the 'Wait time after scale-up/scale-down' condition |
| 24 - | 28% | 6 | – | Below the scale-up condition threshold → Start measuring 2 minutes of scale down unneeded time<br>If the 10-minute wait time after scale-down (24 → 34) is satisfied thereafter, nodes will be removed one by one. |

<a id="cluster-autoscaler"></a>
#### Cluster Autoscaler
Cluster Autoscaler works based on the cluster-autoscaler feature, which is an officially supported feature of the Kubernetes project. For more information, see [Cluster Autoscaler](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler).

> [Note]
The version of the `cluster-autoscaler` applied to NHN Kubernetes Service (NKS) is `1.19.0.`

<a id="cluster-autoscaler-set"></a>
##### Cluster Autoscaler Settings
When enabling cluster autoscaler, you can set the following items

| Settings Item | Meaning | Valid Range | Default | Unit |
| --- | --- | --- | --- | --- |
| Minimum Node Count | Minimum number of nodes that can be scaled down| 1-10 | 1 | unit|
| Maximum Node Count | Maximum number of nodes that can be scaled up| 1-10 | 10 | unit|
| Scaling Down | Enable/Disable Node Scale-down | Enable/Disable | Enable | - |
| Scale Down Utilization Threshold | Reference value to determine resource usage threshold range for scale-down | 1-100 | 50 | % |
| Scale Down Unneeded Time| The duration for retaining resource usage of target nodes to scale down below the threshold| 1-1440 | 10 | minutes |
| Scale Down Delay After Add | Delay before starting to monitor for scale-down targets after scaling up| 10-1440 | 10 | minutes |


<a id="cluster-autoscaler-resize"></a>
#### Scale-up/down Conditions
Scales up if all of the following conditions are met:

* There are no more available nodes to schedule pods.
* The current number of nodes is below the maximum limit.

Scales down if all of the following conditions are met:

* Nodes use resources below the threshold for the set critical area duration.
* The current number of nodes exceeds the minimum limit.

If some nodes contain at least one pod that meets the following conditions, then they will be excluded from the list of nodes to be scaled down:

* Pods restricted by "PodDisruptionBudget"
* Pods in the "kube-system" namespace
* Pods not started by control objects such as "deployment" or "replicaset"
* Pods that use the local storage
* Pods that cannot be moved to other nodes because of the restrictions such as "node selector"

For more information on the conditions for scale-up/down, see [Cluster Autoscaler FAQ](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md).

<a id="cluster-autoscaler-example"></a>
#### Operation Example
Let's check how the autoscaler work through the following example:

**1. Enabling Autoscaler**

Enables autoscaling on the default node group of the cluster you want. For this example, the number of nodes for the default group has been set to 1 and autoscaler settings are configured as follows:

| Settings Item | Value |
| --- | --- |
| Minimum Node Count | 1 |
| Maximum Node Count | 5 |
| Scaling Down | Enable |
| Scale Down Utilization Threshold | 50 |
| Scale Down Unneeded Time| 3 |
| Scale Down Delay After Add | 10 |

**2. Deploying Pods**

Deploy pods using the following manifest:

> [Caution]
Just like this manifest, all manifests must have a container resource request defined on them.

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

**3. Checking Node Scale-up**

The following is the node list before scale-up:

```
# kubectl get nodes
NAME                                            STATUS   ROLES    AGE   VERSION
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready    <none>   45m   v1.28.3
```

After 5-10 minutes, the nodes should be scaled up as shown below:

```
# kubectl get nodes
NAME                                            STATUS   ROLES    AGE   VERSION
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready    <none>   48m   v1.28.3
autoscaler-test-default-w-ohw5ab5wpzug-node-1   Ready    <none>   77s   v1.28.3
autoscaler-test-default-w-ohw5ab5wpzug-node-2   Ready    <none>   78s   v1.28.3
```

The pods that were in `Pending` status are now normally scheduled after the scale-up.

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

You can check scale-up events with the following command:

```
# kubectl get events --field-selector reason="TriggeredScaleUp"
LAST SEEN   TYPE     REASON             OBJECT                                 MESSAGE
4m          Normal   TriggeredScaleUp   pod/nginx-deployment-756fd4cdf-64gtv   pod triggered scale-up: [{default-worker-bf5999ab 1->3 (max: 5)}]
4m          Normal   TriggeredScaleUp   pod/nginx-deployment-756fd4cdf-7bsst   pod triggered scale-up: [{default-worker-bf5999ab 1->3 (max: 5)}]
...
```


**4. Checking Node Scale-down after Deleting Pods**

Deleting the current deployments also deletes the deployed pods.

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

You can check scale-down events with the following command:

```
# kubectl get events --field-selector reason="ScaleDown"
LAST SEEN   TYPE     REASON      OBJECT                                               MESSAGE
13m         Normal   ScaleDown   node/autoscaler-test-default-w-ohw5ab5wpzug-node-1   node removed by cluster autoscaler
13m         Normal   ScaleDown   node/autoscaler-test-default-w-ohw5ab5wpzug-node-2   node removed by cluster autoscaler
```

You can check the status of each node group's autoscaler through `configmap/cluster-autoscaler-status`. Configmaps are created in different namespaces per node group. The following is the naming convention for namespace per node group created by the autoscaler:

* Format: nhn-ng-{node group name}
* Enter a node group name in {node group name}.
* The default node group name is "default-worker."

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
As for the status information, the `Cluster-wide` area has the same information as `NodeGroups.`

<a id="cluster-autoscaler-with-hpa"></a>
#### Example of an action working with HPA (HorizontalPodAutoscale)
Horizontal Pod Autoscaler (HPA) observes resource usage, such as CPU usage, to auto-scale the number of pods of ReplicationController, Deployment, ReplicaSet, and StatefulSet. As the number of pods is adjusted, there could be too many or too few resources available in the node. At this moment, utilize the Autoscaler feature to increase or decrease the number of nodes. In this example, we will see how HPA and Autoscaler can work together to deal with this issue. For more information on HPA, see [Horizontal Pod Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/). 

**1. Enabling Autoscaler**
Enable Autoscaler as shown in the above example.

**2. Configuring HPA**
Deploy a container that creates CPU load for a certain amount of time after receiving a web request. The, expose the service. The following is taken from the `php-apache.yaml` file.

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

(A moment later)

# kubectl top nodes
NAME                                            CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
autoscaler-test-default-w-ohw5ab5wpzug-node-0   574m         57%    1013Mi          58%
```

If you look up the status of HPA, you can see the increase in CPU load and the resultant increase in REPLICAS (number of pods).

```
# kubectl get hpa
NAME         REFERENCE               TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   250%/50%   1         30        5          2m44s
```

**4. Checking the operation of Autoscaler**
If you look up pods, due to the increase in the number of pods, you can see some pods are running as they are scheduled to `node-0`, but some are still pending

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

After a while, you can see another node (node-8) has been added.

```
# kubectl get nodes
NAME                                            STATUS     ROLES    AGE   VERSION
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready      <none>   22d   v1.28.3
autoscaler-test-default-w-ohw5ab5wpzug-node-8   Ready      <none>   90s   v1.28.3
```

You can see all pods that were in Pending state are properly scheduled and now in the Running state.

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

As the number of pods decreases, the resource usage of the node also decreases, which results in the reduction in nodes. You can see the newly added node-8 has been reduced.

```
# kubectl get nodes
NAME                                            STATUS   ROLES    AGE   VERSION
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready    <none>   22d   v1.28.3
```

<a id="user-script-old"></a>
### User Script (old)
You can register a user script when creating clusters and additional node groups. A user script has the following features.

* Feature setting
    * This feature can be set by worker node group.
    * A user script entered when creating clusters is applied to the default worker node group.
    * A user script entered when creating additional node groups is applied to the corresponding worker node group.
    * **The content of a user script cannot be changed after the worker node group is created.**
* Script execution time
    * A user script is executed during the instance initialization process while initializing the worker node.
    * After the user script has been executed, it sets and registers the instance as the worker node of the 'worker node group'.
* Script content
    * The first line of a user script must start with #!.
    * The maximum size of script is 64KB.
    * The script is run with the root permission.
    * The script execution records are stored in the following location.
        * Script exit code: `/var/log/userscript.exitcode`
        * Standard output and standard error streams of script: `/var/log/userscript.output`

<a id="user-script"></a>
### User Script
The features of a new version of user script are included in the node groups created after July 26, 2022. The following features are found in the new version.

* **You can change the user script content after the worker node group is created.**
    * However, the changes are applied only to nodes created after the user script change. 
* The script execution records are stored in the following location.
    * Script exit code: `/var/log/userscript_v2.exitcode`
    * Standard output and standard error streams of scrip: `/var/log/userscript_v2.output`

* Correlations with the old version
    * Features of the new version replace those of the old version.
        * The user script set when creating node groups through the console and API is configured for the new version.
    * For the worker node group that set the old version of a user script, the old version and new version features work separately.
        * You cannot change the user script content set in the old version.
        * You can change the user script content set in the new version.
    * If user scripts are set in the old version and the new version respectively, they are executed in the following order.
        1. The old version of a user script
        2. The new version of a user script

<a id="instance-flavor-update"></a>
### Change Instance Flavor
Change the instance flavor of a worker node group. The instance flavors of all worker nodes in a worker node group are changed.



#### Process

Changing the instance flavor proceeds in the following order.

1. Deactivate the cluster auto scaler feature.
2. Add a buffer node to the worker node group. 
3. Perform the following tasks for all worker nodes within the worker node group:
    1. Evict the working pods from the worker node, and make the nodes not schedulable.
    2. Change the instance flavor of a worker node.
    3. Make the nodes schedulable.
4. Evict working pods from the buffer node, and delete the buffer node.
5. Reactivate the cluster auto scaler feature.

Instance flavor changes work in a similar way to worker component upgrades. For more details on creating and deleting buffer nodes and evicting pods, see [Upgrade a Cluster](/Container/NKS/en/user-guide/#cluster-upgrade).


#### Constraints

You can only change an instance to another flavor that is compatible with its current flavor.

* m2, c2, r2, t2, x1 flavor instances can be changed to m2, c2, r2, t2, x1 flavors.
* m2, c2, r2, t2, x1 flavor instances cannot be changed to u2 flavors.
* u2 flavor instances cannot be changed to other flavors once they have been created, not even to those of the same u2 flavor.

<a id="custom-image"></a>
### Use Custom Image as Worker Image

You can create a worker node group using your custom images. This requires additional work (conversion to NKS worker node) in NHN Cloud Image Builder so that the custom image can be used as a worker node image. In Image Builder, you can create custom worker node images by creating image templates with the worker node application of NHN Kubernetes Service (NKS). For more information on Image Builder, see [](/Compute/Image%20Builder/ko/console-guide/#_1)Image Builder User Guide[](/Compute/Image%20Builder/ko/console-guide/#_1).

> [Caution]
Conversion to NKS worker node involves installing packages and changing settings, so if you work with images that don't work properly, it may fail.
You may be charged for using the Image Builder service.

#### Constraints
The supported OS images and the application versions you must select for each OS image are shown in the table below. You must select the correct version of application to match the image of the base instance from which your custom image is created.

| OS | Image | Application name |
| --- | --- | --- |
| Rocky | Rocky Linux 8.10 (2024.08.20)  | 1.6 |
|  | Rocky Linux 8.10 (2024.11.19)  | 1.7 |
|  | Rocky Linux 8.10 (2025.02.25)  | 1.8 |
|  | Rocky Linux 9.5 (2025.11.18)   | 1.9 |
| Ubuntu | Ubuntu Server 22.04.3 LTS (2023.11.21) | 1.3 |
|  | Ubuntu Server 22.04.3 LTS (2024.02.20)  | 1.4 |
|  | Ubuntu Server 22.04.5 LTS (2024.05.21)  | 1.5 |
|  | Ubuntu Server 22.04.5 LTS (2024.11.19)  | 1.7 |
|  | Ubuntu Server 22.04.5 LTS (2025.02.25)  | 1.8 |
|  | Ubuntu Server 22.04.5 LTS (2025.11.18)  | 1.9 |
|  | Ubuntu Server 24.04.3 LTS (2025.11.18)  | 1.9 |


> [Notes]
During the process of converting a custom image to a worker node image, GPU drivers are installed according to the options selected.
So even if you create a custom GPU worker node image, you don't need to create a custom image with a GPU instance.

#### Process

To use a custom image as a worker node image, perform the following process in the Image Builder service.

1. Click **Create Image Template**.
2. After selecting the application, write the **image template name** , **OS** , **minimum block storage (GB)**, **user script**, and **description**.
    * For a group of worker nodes that do not use GPU Flavor, choose the NHN Kubernetes Service (NKS) Worker Node application.
    * For a group of worker nodes using GPU Flavor, select NHN Kubernetes Service (NKS) Worker Node (GPU) Application.
3. Click **Confirm** to create a image template.
4. Select the created image template and choose **Build Image**.
5. On the **Build Image** screen, select the **Private Image** tab and select a custom image to convert to a worker node.
6. Click **Confirm** to create a new image after conversion to NKS worker node completes.
7. Select the created custom image on the **Create Cluster** or **Create Node Group**.

![nkscustom_image_1.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/nkscustom_image_1.png)

![nkscustom_image_2.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/nkscustom_image_2.png)

![nkscustom_image_3.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/nkscustom_image_3.png)

<a id="extra-volumes"></a>
### Additional Block Storage
You can use additional block storage for node groups. You can specify and create additional block storage when creating clusters and node groups, or you can create and use additional block storage in an existing node group. Additional block storage has the following characteristics

* You can set up to three additional block stores per node group, and the size of the block stores can range from 1 to 2048 GB.
* The additional block storage settings for a node group apply equally to all worker nodes in the node group.
    * Additional block storage changes will reflect the changes to all worker nodes in the node group.
* Additional block storage changes only support resizing and changing the mount path.
    * You cannot delete the additional block storage created.
    * It cannot be resized to a smaller size than the value already set.
* The additional block storage is named in the form `{cluster name}-{node group name}-{node name}-extra-volume-{index}`.
* If you enter a mount path, additional block storage is created and then attempts to mount to the specified path.
    * If not entered, the mount will not proceed.
    * If you enter an invalid mount path and the mount fails, the feature does not work.

[Caution]
> Changing the settings for additional block storage might affect the services you are using because it involves unmounting existing volumes.

<a id="extra-security-groups"></a>
### Additional Security Groups
You can set additional security groups on node groups. You can specify and create additional security groups when you create clusters and node groups, or you can set additional security groups on existing node groups. Additional security groups have the following characteristics

* You can set up up to eight additional security groups per subnet.
* Additional security group settings in a node group apply equally to all worker nodes in the node group.
* If no additional security groups are entered, only the cluster's default security group is applied.
* Security groups that users set directly on individual nodes are not viewed in the additional security groups in the node group.

[Note]
> The additional security groups specified when creating a node group in the console apply to the primary network and all additional networks. Changes to the additional security groups for individual networks can be made after the node group is created.

[Caution]
> When you set an additional security group on a node group, any security groups assigned to existing instances that are not defined in the additional security group are removed.
Changing additional security groups changes network settings, so communication might be temporarily affected while the settings take effect.

<a id="fip-auto-bind"></a>
### Floating IP Auto-assignment
You can use the floating IP auto-assignment feature for a node group. When this feature is enabled, a floating IP is automatically assigned to each node when it is created. You can choose whether to enable this feature when creating a cluster or an additional node group. Once set, the option cannot be changed later. The following items are required to enable the floating IP auto-assignment feature:

| Item | Description | 
| --- | --- | 
| Subnet to connect to | The subnet of the network interface to which the floating IP will be connected. The subnet must be the primary subnet of the cluster or included in the additional subnets of the node group. |
| Floating IP labels | Identifier for selecting which floating IPs to assign to the node. If not entered, the assignment will be to all floating IPs. |


The Floating IP auto-assignment has the following features

* Does not create a floating IP.
  * This feature works by assigning floating IPs that the user has created in advance.
If there are not enough available floating IPs, node scaling may fail.
* Enabling/disabling the Floating IP Auto-Assign and changing its settings does not affect existing nodes.
  * Even if you enable the feature for a node group where it was previously disabled, floating IPs will not be assigned to existing nodes.
  * Even if you disable the feature for a node group where it was previously enabled, floating IPs already assigned to existing nodes will not be disabled.

<a id="cluster-management"></a>
## Cluster Management
To run and manage clusters from a remote host, `kubectl` is required, which is the command-line tool (CLI) provided by Kubernetes.

<a id="kubectl-install"></a>
### Installing kubectl
You can use kubectl by downloading an executable file without any special installation process. The download path for each operating system is as follows.

> [Caution]
If you install Kubernetes-related components such as kubeadm, kubelet, and kubectl using the package manager on worker nodes, the cluster may malfunction. If you are installing kubectl on a worker node, please refer to the download command below to download the file.

| OS | Download Command |
| --- | --- |
| Linux | curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.15.7/bin/linux/amd64/kubectl |
| MacOS | curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.15.7/bin/darwin/amd64/kubectl |
| Windows | curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.15.7/bin/windows/amd64/kubectl.exe |

For more details on installation and options, see [Install and Set Up kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

#### Change Permission
The downloaded file does not have the execute permission by default. You need to add the execute permission.

```
$ chmod +x kubectl
```

#### Change the Location or Set the Path
Move the file to the path set in the environment variable so that kubectl can be executed on any path, or add the path including kubectl to the environment variable.

* Change the location to a path set in the environment variable
```
$ sudo mv kubectl /usr/local/bin/
```

* Add the path to the environment variable
```
// Executed on the path including kubectl
$ export PATH=$PATH:$(pwd)
```

<a id="kubectl-set-kubeconfig"></a>
### Configuration
To access Kubernetes cluster with kubectl, cluster configuration file (kubeconfig) is required. On the NHN Cloud web console, open the **Container > NHN Kubernetes Service (NKS)** page and select a cluster to access. From **Basic Information**, click **Download** of **Configuration Files** to download a configuration file. Move the downloaded configuration file to a location of your choice to serve it as a reference for kubectl execution.

> [Caution]
A configuration file downloaded from the NHN Cloud web console includes cluster information and token for authentication. With the file, you're authorized to access corresponding Kubernetes clusters. Take cautions for not losing configuration files.

kubectl requires a cluster configuration file every time it is executed, so a cluster configuration file must be specified by using the `--kubeconfig` option. However, if the environment variable includes specific path for a cluster configuration file, there is no need to specify each option.

```
$ export KUBECONFIG={Path of cluster configuration file}
```

You may copy cluster configuration file path to `$HOME/.kube/config`, which is the default configuration file of kubectl, if you don't want to save it to an environment variable. However, when there are many clusters, it is easier to change environment variables.

<a id="kubectl-check-connection"></a>
### Confirming Connection
See if it is well set by the `kubectl version` command. If there's no problem, `Server Version` is printed.

```
$ kubectl version
Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.7", GitCommit:"6c143d35bb11d74970e7bc0b6c45b6bfdffc0bd4", GitTreeState:"clean", BuildDate:"2019-12-11T12:42:56Z", GoVersion:"go1.12.12", Compiler:"gc", Platform:"darwin/amd64"}
Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.7", GitCommit:"6c143d35bb11d74970e7bc0b6c45b6bfdffc0bd4", GitTreeState:"clean", BuildDate:"2019-12-11T12:34:17Z", GoVersion:"go1.12.12", Compiler:"gc", Platform:"linux/amd64"}
```

* Client Version: Version information of executed kubectl file
* Server Version: Kubernetes version information comprising a cluster

<a id="certificatesigningrequest"></a>
### CSR (CertificateSigningRequest)
Using Certificate API of Kubernetes, you can request and issue the X.509 certificate for a Kubernetes API client . CSR resource lets you request certificate and decide to accept/reject the request. For more information, see the [Certificate Signing Requests](https://kubernetes.io/docs/reference/access-authn-authz/certificate-signing-requests/) document.

#### CSR Request and Issue Approval Example
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

You can look up the certificate as below. The certificate is a value for the Certificate field under Status.

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
> * Pangyo region: Cluster created on December 29, 2020 or later
> * Pyeongchon region: Cluster created on December 24, 2020 or later

<a id="admission-controller"></a>
### Admission Controller plugin
The admission controller can intercept a Kubernetes API server request and change objects or deny the request. See [Admission Controller]( https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/) for more information about the admission controller. For usage examples of the admission controller, see [Admission Controller Guide](https://kubernetes.io/blog/2019/03/21/a-guide-to-kubernetes-admission-controllers/).

The type of plugin applied to the admission controller varies depending on the cluster version and the time of cluster creation. For more information, see the list of plugins available depending on the time of cluster creation by region.

#### v1.19.13 or earlier
The following applies to clusters created on February 22, 2021 or earlier for the Pangyo region and clusters created on February 17, 2021 or earlier for the Pyeongchon region.

* DefaultStorageClass
* DefaultTolerationSeconds
* LimitRanger
* MutatingAdmissionWebhook
* NamespaceLifecycle
* NodeRestriction
* ResourceQuota
* ServiceAccount
* ValidatingAdmissionWebhook

The following applies to clusters created on February 23, 2021 or later for the Pangyo region and clusters created on February 18, 2021 or later for the Pyeongchon region.

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

#### v1.20.12 or later
All default active admission controllers per Kubernetes version are enabled. The following controllers are activated in addition to the default active admission controllers.

* NodeRestriction
* PodSecurityPolicy

<a id="cluster-upgrade"></a>
### Cluster upgrade
NHN Kubernetes Service (NKS) supports the Kubernetes component upgrade for the currently operating Kubernetes clusters. 

#### Policy of supporting different Kubernetes versions
Kubernetes version is represented as `x.y.z`. `x` is the major, `y` is the minor, and `z` is the patch version. If features are added, it is a major or minor version upgrade. If it provides features compatible with previous versions such as bug fixes, it is a patch version. For more information about this, see [Semantic Versioning 2.0.0](https://semver.org/).

Kubernetes clusters can upgrade the Kubernetes components while in operation. To this end, each Kubernetes component defines whether to support the features based on the Kubernetes version difference. In minor version, for example, the difference of one version supports the Kubernetes component upgrade for the operating clusters by supporting the mutual feature compatibility. It also defines the upgrade sequence for each type of the components. For more information, see [Version Skew Policy](https://kubernetes.io/releases/version-skew-policy/).

<br>

#### Manage NKS Cluster Version
NKS cluster manages Kubernetes and platform versions for each cluster control plane and worker node group. The differences between Kubernetes and platform versions are as follows:

##### Kubernetes Version
* A version defined by upstream Kubernetes.
* It determines the versions of the major Kubernetes components that make up the NKS cluster.
* The major components affected by the Kubernetes version are as follows:
    * kube-apiserver
    * kube-controller-manager
    * kube-scheduler
    * kubelet
    * kube-proxy

##### Platform Version
* A version defined at the NKS service level.
* The various components that make up an NKS cluster are defined and managed as a single version.
* The major components affected by the platform version are as follows:
    * Control plane and worker node components, such as containers and etc
    * Various system components and system management tools, etc.

<br>

Upgrade targets based on the status of the Kubernetes version and platform version of the cluster are as follows:

| Kubernetes version status | Platform version status | Upgrade target |
| --- | --- | --- |
| Not the latest | Not the latest | Kubernetes version and platform version |
| Not the latest | Latest | Kubernetes version |
| Latest | Not the latest | Platform version |
| Latest | Latest | None |

The Kubernetes version and platform version of the control plane can be checked on the cluster view screen, and the Kubernetes version and platform version of the worker node group can be checked on the view screen of each worker node group. 

<br>

##### Upgrade Rules
When upgrading, NKS cluster version control and Kubernetes versioning support policy must be followed to keep the proper sequence. The following rules are applied to NKS's cluster upgrade features.

* Upgrade commands must be given to each control plane and worker node group. 
* In order to upgrade, the Kubernetes version of the control plane  and all worker node groups must match.
* Control plane must be upgraded first in order to upgrade the worker node group. 
* The Kubernetes version can be upgraded to the next version (minor version +1) of the current Kubernetes version.
* The platform version can be upgraded to the latest version provided by the NKS service.
* Neither the Kubernetes version nor the platform version supports downgrades.
* If the cluster is being updated due to the operation of other features, upgrade cannot be proceeded. 
* When upgrading the Kubernetes version from v1.25.4 to v1.26.3, if the CNI is Flannel, it must be changed to Calico-VXLAN.
* Cannot upgrate clusters where NKS registry is not enabled.

The following table shows whether upgrade is possible while upgrading the Kubernetes version. The following conditions are used for the example: 

* List of Kubernetes versions supported by NHN Cloud: v1.31.4, v1.32.3, v1.33.4.3
* Clusters are created as v1.31.4

| Status | Control plane version | Whether control plane can be upgraded | Worker node group version | Whether worker node group can be upgraded
| --- | :-: | :-: | :-: | :-: |
| Initial state| v1.31.4  | Available <sup>[1](#footnote_cluster_upgrade_rule_1)</sup> | v1.31.4  | Unavailable  <sup>[2](#footnote_cluster_upgrade_rule_2)</sup> | 
| State after control plane upgrade | v1.32.3 | Unavailable <sup>[3](#footnote_cluster_upgrade_rule_3)</sup> | v1.31.4 | Available <sup>[4](#footnote_cluster_upgrade_rule_4)</sup> | 
| State after worker node group upgrade | v1.32.3 | Available <sup>[1](#footnote_cluster_upgrade_rule_1)</sup> |v1.32.3 | Unavailable  <sup>[2](#footnote_cluster_upgrade_rule_2)</sup> |
| State after control plane upgrade | v1.33.4 | Unavailable <sup>[3](#footnote_cluster_upgrade_rule_3)</sup> | v1.32.3 | Available <sup>[4](#footnote_cluster_upgrade_rule_4)</sup> | 
| State after worker node group upgrade | v1.33.4  | Unavailable <sup>[5](#footnote_cluster_upgrade_rule_5)</sup> | v1.33.4  | Unavailable  <sup>[2](#footnote_cluster_upgrade_rule_2)</sup> |

Notes

* <a name="footnote_cluster_upgrade_rule_1">1</a>: Upgrade is possible because the versions of the control plane and all worker node groups are matching
* <a name="footnote_cluster_upgrade_rule_1">2</a>: Worker node groups can be upgraded once the control plane is upgraded
* <a name="footnote_cluster_upgrade_rule_1">3</a>: The versions of the control plane and all worker node groups must match in order to upgrade
* <a name="footnote_cluster_upgrade_rule_1">4</a>: Upgrade is possible because the control plane is upgraded
* <a name="footnote_cluster_upgrade_rule_1">5</a>: Upgrade is not possible because the latest version supported by NHN Cloud is being used

<br>

#### Upgrade Strategy
NKS clusters provide two upgrade strategies: Rolling Upgrade and Blue/Green Upgrade. Users can choose the appropriate strategy to upgrade the cluster based on their operational policies.

<br>

**Rolling Upgrade**

![Rolling_Upgrade.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/Rolling_Upgrade.png)

Rolling Upgrade is an upgrade strategy that moves the entire cluster to a new version by sequentially upgrading groups of control plane and worker nodes. Below are the steps involved in performing a cluster upgrade with the Rolling Upgrade strategy and a description of each step.

<br>

##### 1. Upgrade the control plane components via the Upgrade button on the Cluster Query screen.

The NKS cluster control plane ensures high availability. Upgrades are made to the control plane on a rolling update basis to ensure the availability of the cluster. During this process, the Kubernetes APIs may temporarily fail. 

##### 2. Upgrade the worker components of all worker node groups via the Upgrade button on the Node Group Query screen.
Worker components can be upgraded for each worker node group. Worker components are upgraded in the following steps:

1. Deactivate the cluster auto scaler feature.<sup>[1](#footnote_worker_component_upgrade_1)</sup> 
2. Add a buffer node.<sup>[2](#footnote_worker_component_upgrade_2)</sup> to the worker node group.<sup>[3](#footnote_worker_component_upgrade_3)</sup> 
3. Perform the following tasks for all worker nodes within the worker node group.<sup>[4](#footnote_worker_component_upgrade_4)</sup> 
    1. Evict working pods from the worker node, and make the node not schedulable.
    2. Upgrade worker components.
    3. Make the node schedulable.
4. Evict working pods from the buffer node, and delete the buffer node.
5. Reactivate the cluster auto scaler feature.<sup>[1](#footnote_worker_component_upgrade_1)</sup> 

Notes

* <a name="footnote_worker_component_upgrade_1">1</a>: This step is valid only if the cluster autoscaler feature is enabled before starting the upgrade feature.
* <a name="footnote_worker_component_upgrade_2">3</a>: Buffer node is an extra node which is created so that the pods evicted from existing worker nodes can be rescheduled during the upgrade process. It is created having the same scale as the worker node defined in that worker node group, and is automatically deleted when the upgrade process is over. This node is charged based on the instance fee policy. 
* <a name="footnote_worker_component_upgrade_3">3</a>: You can define the number of buffer nodes during upgrade. The default value is 1, and buffer nodes are not added when set to 0. Minimum value of 0, maximum value of (maximum number of nodes per node group - the current number of nodes for the worker node group).
* <a name="footnote_worker_component_upgrade_4">4</a>: Tasks are executed by the maximum number of unavailable nodes set during upgrade. The default value of 1, minimum value of 1, and maximum value of the current number of nodes for the worker node group.

The following can happen in this process.

* Pods in service will be evicted and scheduled to another node. (To find out more about pod eviction, refer to the notes below.)
* Auto scale feature does not operate. 


> [Pod eviction cautions]
> 1. Pods operated by daemonset controller are not evicted.
Daemonset controller runs the pod for each worker node, so the pods run by Daemonset controller cannot run in other nodes. While upgrading the worker node group, the pods run by the Daemonset controller will not be evicted. 
> 2. Pods that use the local storage will lose the previous data as they are evicted.
Pods that use the local storage of the node by using `emptyDir` will lose the previous data when being evicted. This is because the storage space in the local node cannot be relocated to another node. 
> 3. Pods that cannot be copied to another node will not be relocated to another node.
If the pods run by controllers such as (ReplicationController), (ReplicaSet), (Job), (Daemonset), and (StatefulSet) are evicted, they will be rescheduled to another node by the controller. However, the pods not run by these controllers will not be scheduled to another node after being evicted. 
> 4. Eviction can fail or slow down due to the PodDisruptionBudgets (PDB) setting.
You can define the number of pods to maintain with the PodDisruptionBudgets(PDB) setting. Depending on how this setting is set, it may not be possible to evict pods or evicting pods can take longer than normal during upgrade. If pod eviction fails, upgrade fails as well. So if the PDB is enabled, appropriate PDB setting will ensure proper pod eviction. To find out more about PDB setting, see [here](https://kubernetes.io/docs/tasks/run-application/configure-pdb/).


To find out more about safely evicting pods, see [Safely Drain a Node](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/).


<br>

**Blue/Green Upgrade**

![Blue_Green.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/Blue_Green.png)

Blue/Green Upgrade is an upgrade strategy that allows you to configure two different versions of an environment within a cluster during the upgrade process to increase application availability and reduce upgrade risk by simplifying the rollback process in the event of a deployment failure. One environment (Blue) consists of a group of nodes in the version prior to the upgrade, and the other environment (Green) consists of a group of nodes in the version to be upgraded. Once testing is complete on the Green environment, you move application traffic to the Green environment and decommission the Blue environment. This process allows you to upgrade the entire cluster to the next version. Below is the process of performing a cluster upgrade with the Blue/Green Upgrade strategy and a description of each step.

<br>

##### 1. Upgrade the control plane components via the Upgrade button on the Cluster Query screen.
The NKS cluster control plane ensures high availability. Upgrades are made to the control plane on a rolling update basis to ensure the availability of the cluster. During this process, the Kubernetes APIs may temporarily fail. 

##### 2. Create a node group.
Create a new node group to create a green environment for testing. New node groups created after a control plane component upgrade are created with the same version of Kubernetes as the control plane. You can deploy the same resources in the Green environment as in the Blue environment (the existing node group) to validate the post-upgrade environment. In doing so, you must isolate application traffic to ensure that the Blue environment does not impact the operation of the existing cluster.

##### 3. After validating the Green environment (new node group), switch application traffic to the Green environment.
In the newly built Green environment, existing users validate that the resources they are running on are fully compatible with the next version of Kubernetes, and once validation is complete, they switch their application traffic from the existing Blue environment to the newly built Green environment. If something goes wrong during the validation phase in the Green environment, you can simply roll back by deleting the Blue environment without switching traffic.

##### 4. Discard the Blue environment (all worker node groups from previous versions).
Discarding all resources in the Blue environment will cause the control plane and all worker node groups to have consistent versions.

<a id="api-endpoint-ipacl"></a>
### Enforce IP Access Control to Cluster API Endpoints
You can enforce or disable IP access control to cluster API endpoints.
For more information about the IP access control feature, see [IP Access Control](/Network/Load%20Balancer/ko/overview/#ip).

#### IP Access Control Rules
When you add a Cluster API endpoint to IP access control targets, the rules below apply.

* If the IP access control type is set to **Allow**, the cluster default subnet CIDR is automatically added to the access control target.
* If the IP access control type is set to **Allow**, the Dashboard, Namespaces, Workloads, Services & Network, Storage, Settings, and Events tabs in the NKS console are disabled.
* If the IP access control type is set to **Block**, requests are denied if an IP band that overlaps the cluster default subnet CIDR band is in the access control target list.
* The maximum number of IP access control targets you can set is 100.
* At least one IP access control target must exist.

<a id="rotate-certificate"></a>
### Renew Cluster Certificate
Kubernetes requires a PKI certificate for TLS authentication between components. For more information about PKI certificates, see [PKI Certificates and Requirements](https://kubernetes.io/docs/setup/best-practices/certificates/). When you create an NKS cluster, Kubernetes automatically generates the required certificate for the cluster, which has a default validity of 5 years.

If the certificate expires, key components of the cluster, such as the API server, Controller Manager, etcd, will stop working and the cluster will be unavailable.
Before a certificate expires, you can use the Renew Certificate feature to renew its validity. You can find the certificate validity for your cluster and the Renew Certificate button on the Cluster View screen > **Basic Information** > **Kubernetes Certificates**.

Here's how to use the certificate renewal feature
1. Click **Renew Certificate**.
2. Select a renewal period. 
    * You can set a certificate validity period of up to 5 years.
    * Renewal is possible only on a one-year basis.
3. To proceed with the certificate renewal, click **Confirm**.
4. Check the status of the target cluster.
    * A cluster with a certificate renewal in progress has a status of `UPDATE_IN_PROGRESS`, which transitions to `UPDATE_COMPLETE` when the operation completes successfully.
    * When there is a problem with the change, the status is converted to `UPDATE_FAILED`, and it is not allowed to change cluster configuration until normalization completes.
        * Perform certificate renewal once again to normalize the cluster health.
5. On the Cluster Lookup screen, verify that the certificate expiration period has been renewed successfully.
6. Download the kubeconfig file.
    * The kubeconfig file for accessing the cluster contains the certificate.
    * Once the certificate is renewed, you will no longer be able to access the cluster with the kubeconfig you were using.
7. Restart the Pod that uses the CA certificate.
    * The certificate renewal process does not include the feature to restart user-created pods.
    * If a pod with certificate settings exists, a restart is required to apply the updated CA certificate.

> [Note]
> The certificate renewal feature is available for clusters using 1.24 or later versions of Calico-VXLAN CNI.

> [Caution]
> The certificate renewal feature involves a restart of the system components and any kube-system namespace pods initially deployed at cluster creation to reflect the new certificate generation and settings.
> As a result, the state of nodes in your cluster may temporarily change to Not Ready, or some components of your cluster may not function normally while certificate renewal is in progress.
> To minimize the impact of these operations, you should avoid performing tasks such as creating new Pods while certificate renewal is in progress.

<a id="k8s-component"></a>
### Set Kubernetes Component

You can set a number of options for Kubernetes components. You can set these options at cluster creation time, and you can also change them after cluster creation is complete. This applies to the following components and options.

| Component | Behavior Arear | Option | Description |
| --- | --- | --- | --- |
| kube-apiserver | Control plane | default-not-ready-toleration-seconds | Defines how long pods running on a node will be allowed when the node is in the NotReady state:<br>(unit: seconds, default: 300, minimum: 0, maximum: 86400) |
| kube-apiserver | Control plane | default-unreachable-toleration-seconds | Defines how long pods running on a node will be allowed when the node is not connected to the network:<br>(unit: seconds, default: 300, minimum: 0, maximum: 86400) |
| kube-controller-manager | Control plane | node-monitor-grace-period | Defines how long to wait when a node is in an unhealthy state before considering it unhealthy.<br>(unit: seconds, default: 40, minimum: 0, maximum: 86400) |
| kube-controller-manager | Control plane | unhealthy-zone-threshold | Defines the threshold for the percentage of NotReady nodes to consider a zone unhealthy:<br>(Unit: percentage, Default: 55, Minimum: 0, Maximum: 100) |
| kubelet | Worker node | node-status-update-frequency | Defines how often the kubelet should report the node state of the kubelet:<br>(unit: seconds, default: 10, minimum: 0, maximum: 86400) |

For more information on each item, see the [Kubernetes official documents](https://kubernetes.io/docs/).

> [Caution]
> * If you change the settings of a component running in the control plane, the component in the control plane will restart.
> * If you change the settings of a component running on a worker node, the component on the worker node will restart.

<a id="k8s-label"></a>
### Kubernetes Label Settings
You can use the Kubernetes label setting for each node group. When this feature is enabled, the user-defined labels are automatically applied to the nodes when they are created. Labels are key-value pairs attached to Kubernetes objects such as pods and nodes, and are used to identify characteristics of those objects. For a detailed description of labels, see [Labels and Selectors](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/).


A Kubernetes label consists of a key and value pair, and each valid label key and value must adhere to the following rules.

#### Label Key
Label keys can have a structure of prefix and name separated by a slash (/), and the prefix can be omitted.
+ Prefixes
  + It must be 253 characters or less.
  + It must be in the subdomain format of DNS.
  + Predefined prefixes cannot be used.
    + ["kubernetes.io", "k8s.io", "magnum.openstack.org"]
+ Name
  + It must be 63 characters or less.
  + Alphabetical case, numbers, dashes (-), underscores (_), and dots (.) and must start and end with an alphanumeric character.


#### Label Value
+ It must be blank or 63 characters or less.
+ Alphabetical case, numbers, dashes (-), underscores (_), and dots (.) and must start and end with an alphanumeric character.

> [Note]
> * You can specify up to 20 Kubernetes labels.
> * When you change the Kubernetes label settings, new nodes created afterward will have the changed settings.

<a id="oidc-auth"></a>
### OIDC Authentication Settings Feature

OpenID Connect (OIDC) is an interoperable authentication protocol based on the OAuth 2.0 framework. With OIDC, you can authenticate users with external authentication services. To learn more about how OIDC works, see [What is OpenID Connect](https://openid.net/developers/how-connect-works/).

The NKS cluster can be set up to handle authentication using OIDC. The configuration items for OIDC authentication are as follows

| Item | Required | Description |
| --- | --- | --- |
| Issuer URL | O | 'https://'로 OIDC provider URL starting with |
| Client ID | O | Client ID of the OIDC provider |
| Username claim | X | The claim to use as username. Default: 'sub'<br>Non-email claims are prefixed with the provider URL. |
| Groups claim | X | Claims to use as groups |
| Username prefix | X | A prefix to attach to the username claim to avoid conflicts.<br>If not set, username claims except email are prefixed with the provider URL.<br>To disable the prefix, enter '-'. |
| Groups prefix | X | Prefix to attach to groups claim to avoid conflicts |
| Required Claim | X | Key/value pairs that require verification in an ID token |
| CA File | X | The certificate file of the CA that signed the OIDC provider's web certificate. |
| Signing Algs| X | List of allowed JOSE asymmetric signature algorithms. Default: 'RS256' |

<a id="control-plane-k8s-log"></a>
### Store Logs of Kubernetes Control Plane Components
NHN Kubernetes Service (NKS) provides logs for key Kubernetes components running on the control plane. These logs help you better understand various events and operations occurring within the cluster, and can be useful for diagnosing service status and troubleshooting issues.

The characteristics of the control plane Kubernetes component log storage feature is as follows.

* You can delivery logs to one of two services: Log & Crash Search and Object Storage.
* The log level being delivered is fixed to `INFO`.
* The Kubernetes components that provide logs are listed below.
    * kube-apiserver
    * kube-scheduler
    * kube-controller-manager


> [Note]
You can only set one log delivery destination. If you want to manage logs in both Log & Crash Search and Object Storage, you can first set the delivery destination to Log & Crash Search, and then use the "Store logs offsite" feature to store additional logs in Object Storage.
You can also delivery to Log & Crash Search or Object Storage in other projects.

<a id="control-plane-k8s-log-lncs"></a>
#### Log Delivery to Log & Crash Search

<a id="control-plane-k8s-log-lncs-forward"></a>
##### Delivery Interval
Log delivery occurs after the user-specified delivery interval has elapsed since the log was generated. The delivery interval can be set between 1 and 60 minutes. 

> [Note]
If the log size exceeds 300KB before the delivery interval ends, it is immediately sent to Log & Crash Search.

<a id="control-plane-k8s-log-lncs-labels"></a>
##### Log & Crash Search label Information
The label information applied when logs are delivered to Log & Crash Search is as follows:

| Label | Description 
| --- | --- |
| body | "log" fixed value |
| logSource | "NKS" fixed value |
| logLevel | "INFO" fixed value |
| logVersion | "v2" fixed value |
| projectVersion | "1.0.0" fixed value |
| host | Master node name |
| UUID | Body |
| cluster_name | Cluster Name |
| nks_version | Cluster Version |
| component | Component name |

> [Note]
The four labels cluster_uuid, cluster_name, nks_version, and component are not included in the default fields when viewing logs in the Log & Crash Search console.
You can add them manually by adding labels in the Selected fields section.

<a id="control-plane-k8s-log-obs"></a>
#### Log Delivery to Object Storage

<a id="control-plane-k8s-log-obs-forward"></a>
##### Delivery Interval
Logs are collected and delivered every user-specified delivery interval. The delivery interval can be set between 1 and 60 minutes.

> [Note]
If the file size stored in Object Storage exceeds 300KB, it is partitioned.
Log files are delivered as soon as they exceed 300KB.
400KB or less: Stored as a single file with the \_index0 suffix
Over 400 KB: Split into multiple files suffixed with \_index1, \_index2, etc.

<a id="control-plane-k8s-log-obs-compression"></a>
##### Compressing files
You can choose to compress them into gzip when storing them in the storage.

<a id="control-plane-k8s-log-obs-authorization"></a>
##### Granting storage access permissions
On the NKS page of the console, click **NKS System Account Information**to display the tenant ID and user ID used by NKS. If you set the control plane log store type to Object Storage (OBS), this NKS system account must be granted write permissions to that container, otherwise the NKS system account cannot write data to your OBS.

How to Set

* Access the NHN Cloud console > Object Storage.
* Select a container to store control plane logs.
* At the bottom, click Basic info > Change Access Policy Settings.
* Under Role-Based Access Policy, click Enable.
* Enter the tenant ID and user ID from the NKS system account information you identified above, and grant Write permission.

> [Caution]
If a container in Object Storage is deleted during control plane log delivery, or if Write permissions are revoked from the container, log delivery will fail.

<a id="control-plane-k8s-log-path"></a>
##### Control plane log storage path
The control plane log storage path is based on OBS endpoint, AUTH tenant, Container, and Path information, and is configured in the following format.

* {OBS_https_endpoint}/{AUTH_OBS_TENANT}/{Container}/{Path}

For example, if the settings are as follows

* OBS https endpoint: https://kr1-api-object-storage.nhncloudservice.com/v1
* AUTH_OBS_TENANT: AUTH_e670167936434f85a03694184000ffe6
* Container: nks_log_container
* Desired storage path: example/my/folder

The actual control plane log storage path is as follows

* https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_e670167936434f85a03694184000ffe6/nks_log_container/example/my/folder

> [Note]
If the OBS endpoint, AUTH_tenant, and Container information set in obs_api_url does not exist, the setup request will fail.

The actual logs are stored under the URL above with the following structure.

* ${Custom OBS Container Name}/NKS/${Cluster UUID}/${Master Node Name}/${K8S Component Name}/${Year}/${Month}/${Year-Date-Hour-Second}-index${index_count}.gz

For example, if the settings are as follows

* Container: nks_log_container
* Cluster UUID: f31dd18f-4dab-49fa-97bb-8feba31cb30b
* Cluster name: nks-test
* Component: kube-apiserver
* Stored time: 2025-04-28 10:15:00

Logs are created in the OBS container by the following paths

* nks_log_container/NKS/f31dd18f-4dab-49fa-97bb-8feba31cb30b/
nks-test-master-0/kube-apiserver/2025/04/20250428-101500-index0.gz

<a id="worker-node-management"></a>
## Manage Worker Node

<a id="container-management"></a>
### Manage Container

#### Clusters of Kubernetes v1.24.3 or older
Clusters of Kubernetes v1.24.3 or older use Docker to comprise the container runtime. In the worker node, you can use the docker CLI to view the container status and the container image. For more details and usage on the docker CLI, see [Use the Docker command line](https://docs.docker.com/engine/reference/commandline/cli/).

#### Clusters of Kubernetes v1.24.3 and later

Clusters of Kubernetes v1.24.3 or later use containerd to comprise the container runtime. In the worker node, you can use nerdctl instead of the docker CLI to view the container status and the container image. For more details and usage on nerdctl, see [nerdctl: Docker-compatible CLI for containerd](https://github.com/containerd/nerdctl#nerdctl-docker-compatible-cli-for-containerd).

<a id="network-management"></a>
### Manage Network

#### Default Network Interface
Every worker node has a network interface that connects to the VPC/subnet entered when creating the cluster. This default network interface is named "eth0", and worker nodes connect to the control plane through this network interface.

#### Additional Network Interface
If you set additional networks when creating a cluster or worker node group, additional network interfaces are created on the worker nodes of that worker node group. Additional network interfaces are named eth1, eth2, ... in the order entered in the additional network settings.

#### Default Route Settings
If multiple network interfaces exist on a worker node, a default route is set for each network interface. If multiple default routes are set on a system, the default route with the lowest metric value acts as the system default route. Default routes per network interface have lower metric values for smaller interface numbers. This causes the smallest numbered network interface among active network interfaces to act as the system default route.

To set the system default route as an additional network interface, the following tasks are required. 

##### 1. Change Metric Settings per Network Interface
All network interfaces of a worker node are assigned an IP address through a DHCP server. Set the default route for each network interface when an IP address is assigned from a DHCP server. At this time, the metric value of each default route is preset for each interface. The storage location and setting items for each Linux distribution are as follows.

* CentOS
    * Configuration file location: /etc/sysconfig/network-scripts/ifcfg-{network interface name}
    * Configuration item for metric value: METRIC
* Ubuntu
    * Configuration file location: /etc/systemd/network/toastcloud-{network interface name}.network
    * Configuration item for metric value: RouteMetric in DHCP section

> [Note]
The metric value for each default route is determined when the default route is set.
Therefore, the changed settings will take effect at the time of setting the next default route.
To change the metric value for each route currently applied to the system, refer to `Change Metric Value for the Current Route` below.

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

Also delete the route of eth1 first, and set the metric value for eth1 to 0. 
```
# route del -net 0.0.0.0/0 dev eth1
# route add -net 0.0.0.0/0 gw 192.168.0.1 dev eth1 metric 0
```

When you look up the route, you can find that the metric value has changed.
```
# route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.0.1     0.0.0.0         UG    0      0        0 eth1
0.0.0.0         10.0.0.1        0.0.0.0         UG    100    0        0 eth0
0.0.0.0         172.16.0.1      0.0.0.0         UG    200    0        0 eth2
...
```

#### Change Default Route Settings using User Script
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
### Set kubelet Custom Arguments
A kubelet is a node agent that runs on all worker nodes. The kubelet receives input for many settings using command-line arguments. The kubelet custom arguments setting feature provided by NKS allows you to add arguments that are input at kubelet startup. You can set up kubelet custom arguments and apply them to your system as follows.

* Enter your custom arguments in the `/etc/kubernetes/kubelet-user-args` file on the worker node in the form `KUBELET_USER_ARGS="custom arguments` ".
* Run the `systemctl daemon-reload` command.
* Run the `systemctl restart kubelet` command.
* Determine whether the kubelet is working properly with the `systemctl status kubelet` command.

> [Caution]
> * This feature will only work for clusters newly created after November 28, 2023.
> * This feature runs on each worker node for which you want to set custom arguments.
> * The kubelet will not behave correctly when you enter an incorrectly formatted custom argument.
> * The set custom argument will remain in effect even when the system restarts.

<a id="containerd-registry-config"></a>
### Custom Containerd Registry Settings
NKS clusters in v1.24.3 and later use containerd v1.6 as the container runtime. NKS provides the feature to customize several settings in containerd, including those related to the registry, to suit your environment. For registry settings in containerd v1.6, see [Configure Image Registry](https://github.com/containerd/containerd/blob/release/1.6/docs/cri/registry.md).

While initializing worker nodes, if a custom containerd registry configuration file`(``/etc/containerd/registry-config``.json`) exists, the contents of this file are applied to the containerd configuration file`(/etc/containerd/config.toml`). If a custom containerd registry configuration file does not exist, the containerd configuration file applies the default registry settings. The default registry settings include the following

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

The following key/value formats can be set for one registry

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

#### Example 1 

If you want to register additional registries in addition to `docker.io`, you can set up as follows.

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

#### Example 2 

If you want to remove the `docker.io` registry and only register registries that support HTTP, you can set up as follows.
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

#### Example 3

To generate a custom containerd registry configuration file with the contents of Example 2 upon node creation, you can set up a user script as follows

```bash
mkdir -p /etc/containerd
echo '[ { "registry": "user-defined.registry.io", "endpoint_list": [ "http://user-defined.registry.io" ], "tls": { "insecure_skip_verify": true } } ]' > /etc/containerd/registry-config.json
```

> [Caution]
> * The containerd configuration file`(/etc/containerd/config.toml`) is a file that is managed by NKS. Arbitrary modifications to this file can cause errors in the behavior of NKS features or remove the modifications. 
> * If an incorrect registry is set with the custom containerd registry setup feature, worker nodes can work abnormally. 
> * The point at which the custom containerd registry settings feature is applied to the containerd configuration file is during worker node initialization. The worker node initialization process is part of the worker node creation process and the worker node group upgrade process.
>     * To apply the custom container registry settings feature when creating a worker node, you must have your user script generate this configuration file.
>     * To apply the custom container registry settings feature when upgrading a group of worker nodes, you must manually set this file on all worker nodes before proceeding with the upgrade.
> * If a custom containerd registry settings file exists, the settings in this file will be applied to containerd.
>     * To use the `docker.io` registry, you must also include settings for the `docker.io` registry. For settings for the `docker`. `io` registry, see Default registry settings.
>     * If you do not want to use the `docker.io` registry, you can simply not include any settings for the `docker.io` registry. However, at least one registry setting must exist.

<a id="worker-management-caution"></a>
### Precautions for Worker Node Management
* Do not arbitrarily delete container images that are pulled on worker nodes. This may cause the Pods required by the NKS cluster to stop working. 
* If you arbitrarily stop the system with commands like `shutdown`, `halt`, `poweroff`, etc., you cannot restart it on the console. Use the worker node start/stop feature.
* You must not modify various configuration files within the worker node or manipulate system services. Critical issues may occur in the NKS cluster.

<a id="cni"></a>
## Container Network Interface (CNI)
NHN Kubernetes Service (NKS) provides different types of Container Network Interfaces (CNI) depending on the version. After 7/23/2024, you can choose between Calico-VXLAN and Calico-eBPF CNIs when creating a cluster, with Calico-VXLAN being the default setting. Flannel and Calico-VXLAN CNIs organize container workload into an overlay network and communicate using VXLAN. Calico-eBPF organizes container workloads into BGP routing protocols, communicating directly based on eBPF technology, with some segments communicating using VXLAN. For more information about eBPF in Calico, see [about eBPF](https://docs.tigera.io/calico/latest/about/kubernetes-training/about-ebpf).

In addition, Rocky and Ubuntu are the only OSes for which you can choose Calico-eBPF CNI, while Flannel and Calico-VXLAN support all OSes (Centos, Rocky, Red Hat, Ubuntu).

<a id="calico-cni-types"></a>
### Calico CNI Types
Calico-VXLAN and Calic-eBPF provided by the NHN Kubernetes Service (NKS) have the following differences

|  | Calico-VXLAN | Calico-eBPF |
| :-: | :-: | :-: |
| Container Network Processing Module | Linux Kernel Network Stack | eBPF+Linux Kernel Network Stack |
| kube-proxy | Enabled | Disabled (eBPF replaces kube-proxy) |
| Network Method| VXLAN | Direct communication |
| Pod to Pod communication| VXLAN Encapsulated Communication | Direct communication<sup>[1](#footnote_calico_1)</sup> |
| Service ClusterIP to Pod Communication | VXLAN Encapsulated Communication | Direct communication |
| Service NodePort to Pod Communication | VXLAN Encapsulated Communication | VXLAN Encapsulated Communication |
| Apply Network Policies | ptables-based | eBPF-based (kernel-level) |
| Network Performance | Low Performance due to VXLAN encapsulation | High Performance (low latency) due to direct communication |

Notes

* <a name="footnote_calico_1">1</a>: The packet's source IP and destination IP are set to the Pod IP. When using enhanced security rules, you must set a security rule for this traffic separately. 

> [Cautions]
> Clusters using Calico v3.24.1 eBPF mode cannot create node groups using images from Rocky 9.5 or later or Ubuntu 24.04 or later.
> To use these images, you must update Calico to v3.28.2 or later via the Add-on Management feature.

<a id="security-group"></a>
## Security Group
If you set enhanced security rules to True at cluster creation, only mandatory security rules are created at worker node security group creation.

<a id="mandatory-sg-rules"></a>
### Required Security Rules for Cluster Worker Nodes

| Direction | IP protocol | Port range | Ether | Remote | Description | Considerations |
| :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| ingress | TCP | 10250 | IPv4 | Worker node | kubelet port, direction: metrics-server (worker node) → kubelet (worker node) | |
| ingress | TCP | 10250 | IPv4 | NKS Control Plane | kubelet port, direction: kube-apiserver (NKS Control plane) → kubelet (worker node) | |
| ingress | TCP | 5473 | IPv4 | Worker node |  calico-typha port, direction: calico-node (worker node) → calico-typha (worker node) | Created when CNI is Calico-VXLAN, Calico-eBPF |
| ingress | TCP | 179 | IPv4 |  Worker node | calico-node BGP port, direction: pod (worker node) → pod (worker node) | Created when CNI is Calico-eBPF |
| ingress | TCP | 179 | IPv4 | NKS Control Plane | calico-node BGP port, direction: pod (NKS Control plane) → pod (worker node) | Created when CNI is Calico-eBPF |
| ingress | UDP | 8472 | IPv4 | Worker node | flannel vxlan overlay network port, direction: pod (worker node) → pod (worker node) | Created when CNI is flannel |
| ingress | UDP | 8472 | IPv4 | Worker node | flannel vxlan overlay network port, direction: pod (NKS Control plane) → pod (worker node) | Created when CNI is flannel |
| ingress | UDP | 4789 | IPv4 | Worker node | calico-node vxlan overlay network port, direction: pod (worker node) → pod (worker node) | Created when CNI is Calico-VXLAN, Calico-eBPF |
| ingress | UDP | 4789 | IPv4 | NKS Control Plane | calico-node vxlan overlay network port, direction: pod (NKS Control plane) → pod (worker node) | Created when CNI is Calico-VXLAN, Calico-eBPF |
| egress | TCP | 2379 | IPv4 | NKS Control Plane | etcd port, direction: calico-kube-controller (worker node) → etcd (NKS Control plane)| |
| egress | TCP | 6443 | IPv4 | Kubernetes API endpoint | kube-apiserver port, direction: kubelet, kube-proxy (worker node) → kube-apiserver (NKS Control plane) | |
| egress | TCP | 6443 | IPv4 | NKS Control Plane | kube-apiserver port, direction: default kubernetes service (worker node) → kube-apiserver (NKS Control plane) | |
| egress | TCP | 5473 | IPv4 | Worker node | calico-typha port, direction: calico-node (worker node) → calico-typha (worker node) | Created when CNI is Calico-VXLAN, Calico-eBPF |
| egress | TCP | 53 | IPv4 | Worker node | DNS port, direction: Worker node → External | |
| egress | TCP | 443 | IPv4 | Allow all | HTTPS port, direction: Worker node → External | |
| egress | TCP | 80 | IPv4 | Allow all | HTTP port, direction: Worker node → External | |
| egress | TCP | 179 | IPv4 |  Worker node | calico-node BGP port, direction: pod (worker node) → pod (worker node) | Created when CNI is Calico-eBPF |
| egress | TCP | 179 | IPv4 | NKS Control Plane | calico-node BGP port, direction: pod (NKS Control plane) → pod (worker node) | Created when CNI is Calico-eBPF |
| egress | UDP | 8472 | IPv4 | Worker node | flannel vxlan overlay network port, direction: pod (worker node) → pod (worker node)| Created when CNI is flannel |
| egress | UDP | 8472 | IPv4 | NKS Control Plane | flannel vxlan overlay network port, direction: pod (worker node) → pod (NKS Control plane) | Created when CNI is flannel |
| egress | UDP | 4789 | IPv4 | Worker node | calico-node vxlan overlay network port, direction: pod (worker node) → pod (worker node) | Created when CNI is Calico-VXLAN, Calico-eBPF |
| egress | UDP | 4789 | IPv4 | NKS Control Plane | calico-node vxlan overlay network port, direction: pod (worker node) → pod (NKS Control plane) | Created when CNI is Calico-VXLAN, Calico-eBPF |
| egress | UDP | 53 | IPv4 | Allow all | DNS port, direction: Worker node → External | |

When using enhanced security rules, the NodePort type of service and the ports used by the NHN Cloud NAS service are not added to the security rules. You need to set the following security rules as needed. 

| Direction | IP protocol | Port range | Ether | Remote | Description |
| :-: | :-: | :-: | :-: | :-: | :-: |
| ingress, egress | TCP | 30000 - 32767 | IPv4 | Allow all | NKS service object NodePort, direction: external → worker node |
| egress | TCP | 2049 | IPv4 | NHN Cloud NAS service IP address | RPC NFS port of csi-nfs-node, direction: csi-nfs-node (worker node) → NHN Cloud NAS service |
| egress | TCP | 111 | IPv4 | NHN Cloud NAS service IP address | rpc portmapper port of csi-nfs-node, direction: csi-nfs-node (worker node) → NHN Cloud NAS service |
| egress | TCP | 635 | IPv4 | NHN Cloud NAS service IP address | rpc mountd port of csi-nfs-node, direction: csi-nfs-node (worker node) → NHN Cloud NAS service |

> [Caution when using Calico-eBPF CNI].
When using Calico-eBPF CNI, communication between pods and communication from nodes to pods is done through the ports set on the pods.
If you are using enhanced security rules, you must manually add ingress and egress security rules for those pod ports.

<a id="relaxd-sg-rules"></a>
### Rules that are generated when you don't use enhanced security rules

If you don't use enhanced security rules, additional security rules are created for services of type NodePort and for external network communication.

| Direction | IP protocol | Port range | Ether | Remote | Description | 
| :-: | :-: | :-: | :-: | :-: | :-: |
| ingress | TCP | 1 - 65535 | IPv4 | Worker node | All ports, direction: Worker node → Worker node |
| ingress | TCP | 1 - 65535 | IPv4 | NKS Control Plane | All ports, direction: NKS Control plane → Worker node |
| ingress | TCP | 30000 - 32767 | IPv4 | Allow all | NKS service object NodePort, direction: external → worker node |
| ingress | UDP | 1 - 65535 | IPv4 | Worker node | All ports, direction: Worker node → Worker node |
| ingress | UDP | 1 - 65535 | IPv4 | NKS Control Plane | All ports, direction: NKS Control plane → Worker node |
| egress | Random | 1 - 65535 | IPv4 | Allow all | All ports, direction: Worker node → External |
| egress | Random | 1 - 65535 | IPv6 | Allow all | All ports, direction: Worker node → External |

<a id="addon-mgmt"></a>
## Add-on Management
An add-on is a component that is not a required component of a Kubernetes cluster, but is provided to extend the functionality of an NKS cluster or to provide specialized functionality. Add-ons can include components that perform functions such as networking, service discovery, monitoring, storage provisioning, and more. Users can install/update/remove add-ons provided by NHN Cloud to the cluster through the add-on management feature.

> [Caution]
Add-on management features are not available for clusters that do not have the NKS registry enabled.

<a id="addon-mgmt-operation"></a>
### How it works
This section describes how the add-on management feature works.

#### Server-side apply
When installing/updating addons to a cluster using the addon management feature, Kubernetes' Server-side apply is used. Client-side apply is where the client computes the resource state locally and sends the entire resource to the API server. Server-side apply, on the other hand, allows the API server to perform resource merging and field ownership management, allowing the API server to perform resource merging and conflict detection. For more information about server-side apply, see [Server-side apply](https://kubernetes.io/docs/reference/using-api/server-side-apply/).


#### Conflict Resolution Options
Conflicts may occur during add-on installation or updates if users have modified fields that are managed by the add-on. Users can resolve these conflicts by selecting the appropriate conflict resolution option during installation or updates. The Add-on Management feature provides the following conflict resolution options.

* None: If a conflict occurs, the install/update will not be applied, and the install/update request will be treated as a failure.
* Overwrite: In the event of a conflict, overwrites the conflicting field with the default value defined by the add-on.
* Preserve: When a conflict occurs, the conflicting field is preserved with its existing value.

> [Caution when changing versions]
> When changing add-on versions, the default settings for required components may change. Conflicts can occur even if the user doesn't directly change these fields. Selecting "None" or "Preserve" for the conflict handling option may cause add-on installation/update failure. You can prevent conflicts by selecting "Override" for the conflict handling option.

> [Caution about the Preserve option]
You can't preserve all changes to the resources that make up an add-on.
If a conflict occurs in a non-preservable field, the install/update operation will fail.

#### Main Features
You can install/update/remove add-ons to the cluster using the add-on management feature.

* Installation
    * Install the add-on on the cluster.
    * Install by specifying the add-on version and add-on-specific options.
    * Specify conflict resolution options during installation.
* Update
    * Update the addons that are installed in the cluster.
    * You can change add-on versions, add-on-specific options, and more.
        * Depending on the add-on, you might not be able to change the option.
    * Specify conflict resolution options when updating.
* Removals
    * Remove all resources that make up the add-on from the cluster.
    * However, required types cannot be removed.

> [Note]
Upgrades of components such as CNI and CoreDNS are no longer provided through the Kubernetes version upgrade feature.
Instead, you can update the version of each add-on using the add-on update feature.

#### Enable Add-on Management
Existing clusters without the Add-on Management feature enabled can still use it. In clusters where add-ons have not been configured, components such as Calico and CoreDNS may still be running, but the system will show that the add-ons are not installed. In this case, you can install each add-on manually, after which they can be managed through the Add-on Management feature. If you have modified the configuration of resources that make up an add-on, selecting the 'Preserve' option during installation allows you to retain the existing resource settings.

<a id="addon-mgmt-types"></a>
### Add-on types
Add-on types classify the add-ons installed in a cluster based on their characteristics.

| Type | Required | Description|
|---|---|---|
| CNI | O | The type corresponding to the CNI that will be installed in the cluster. |
| kube-dns | O | The primary DNS server that operates within the NKS cluster. |
| cinder-csi-plugin | X | A CSI driver that can provision and manage block storage on NHN Cloud. |
| metrics-server | X | A Kubernetes component that collects resource usage metrics from nodes and pods for autoscaling and monitoring. |
| snapshot-controller | X | A Kubernetes component that manages the lifecycle of volume snapshots, including creating, deleting, and linking PVCs. |
| nfs-csi-plugin | X | A CSI driver that can provision and manage NFS on NHN Cloud. |

<a id="addon-mgmt-addon-list"></a>
### Add-on list

<a id="addon-mgmt-addon-calico"></a>
#### Calico
Calico is a CNI plugin that provides networking and network security for Kubernetes. For a description of Calico provided by NHN Cloud, see [Calico CNI Types](#calico_cni_types).

* Type: CNI
* Options
    * mode
        * Determine Calico's mode of operation.
        * Supported modes of operation: VXLAN, EBPF
* Supported version list
    * v3.28.1-nks1
    * v3.30.2-nks1

<a id="addon-mgmt-addon-coredns"></a>
#### CoreDNS
CoreDNS is the default DNS server for a Kubernetes cluster.

* Type: kube-dns
* Options: None
* Supported version list: v1.8.3-nks1

<a id="addon-mgmt-addon-cinder-csi-plugin">
#### Cinder CSI Plugin
The Cinder CSI Plugin is a CSI driver that allows you to provision and manage block storage in NHN Cloud.

* Type: cinder-csi-plugin
* Options: None
* Supported Versions
    * v1.27.101-nks1
    * v1.27.102-nks1

<a id="adoon-mgmt-addon-metrics-server">
#### Metrics Server
Metrics Server is a Kubernetes component that collects resource usage metrics from nodes and pods for autoscaling and monitoring.

* Type: metrics-server
* Options: None
* Supported Versions: v0.4.4-nks1

<a id="addon-mgmt-addon-snapshot-controller">
#### Snapshot Controller
The Snapshot Controller is a Kubernetes component that manages the lifecycle of volume snapshots, including creating, deleting, and linking PVCs.

* Type: snapshot-controller
* Options: None
* Supported Versions: v4.1.1-nks1

<a id="addon-mgmt-addon-nfs-csi-plugin">
#### NFS CSI Plugin
The NFS CSI Plugin is a CSI driver that allows you to provision and manage NFS on NHN Cloud.

* Type: nfs-csi-plugin
* Options: None
* Supported Versions: v1.0.1-nks1


<a id="loadbalancer-service"></a>
## LoadBalancer Service
Pod is a basic execution unit of a Kubernetes application and it is connected to a cluster network via CNI (container network interface). By default, pods are not accessible from outside the cluster. To expose a pod's services to the outside of the cluster, you need to create a path to expose to the outside using the Kubernetes `LoadBalancer` Service object. Creating a LoadBalancer service object creates an NHN Cloud Load Balancer outside the cluster and associates it with the service object.

<a id="create-webserver-pod"></a>
### Creating Web Server Pods
Write a deployment object manifest file that executes two nginx pods as follows and create an object.

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

With deployment object created, pods defined at manifest are automatically created.

```
$ kubectl apply -f nginx.yaml
deployment.apps/nginx-deployment created

$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE  
nginx-deployment-7fd6966748-pvrzs   1/1     Running   0          4m13s
nginx-deployment-7fd6966748-wv7rd   1/1     Running   0          4m13s
```

<a id="create-lb-service"></a>
### Creating LoadBalancer
To define service object of Kubernetes, a manifest comprised of the following items is required:

| Item | Description |
| --- | --- |
| metadata.name | Name of service object |
| spec.selector | Name of pod to be associated with service object |
| spec.ports | Interface setting to deliver incoming traffic from external load balancer to a pod |
| spec.ports.name | Name of interface |
| spec.ports.protocol | Protocol for an interface (e.g: TCP) |
| spec.ports.port | Port number to be made public out of service object |
| spec.ports.targetPort | Port number of a pod to be associated with service object |
| spec.type | Type of service object |

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

When it is associated with external load balancer, **EXTERNAL-IP** shows IP, which refers to floating IP of external load balancer.

```
$ kubectl get service
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)          AGE
nginx-svc    LoadBalancer   10.254.134.18   123.123.123.30   8080:30013/TCP   3m13s
```

> [Note]
You can view the created load balancer on the **Network > Load Balancer** page.
Load balancer IP is a floating IP allowing external access. You can check it on the **Network > Floating IP** page.

<a id="internet-test-via-service"></a>
### Testing Service on Internet
Send an HTTP request via floating IP associated with load balancer to see if the web server pod of a Kubernetes cluster responds. Since the TcP/8080 port of a service object is attached to TCP/80 port of a pod, request must be sent to the TCP/8080 port. If external load balancer, service object, and pod are well associated, the web server shall respond to nginx default page.

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
### Setting Detailed Options for Load Balancer
When defining service objects in Kubernetes, you can set several options for the load balancer. You can set the following.

* Global Setting and Per-Listener Setting
* Format of Per-Listener Setting
* Setting Load Balancer Name
* Setting keep-alive timeout
* Set load balancer type
* Set static routes
* Set the session affinity
* Set whether to preserve floating IP addresses
* Set the load balancer IP
* Set whether to use the floating IP
* Set VPC
* Set Subnet
* Set Member Subnet
* Set the listener connection limit
* Set the listener protocol
* Set the listener proxy protocol
* Set the load balancing method
* Set the health check protocol
* Set the health check interval
* Set the health check maximum response time
* Set the maximum number of retries for a health check
* Set the status check port
* Set the status check host header
* L7 rules and condtions

#### Global Setting and Per-Listener Setting
For each setting item, global setting or per-listener setting is supported. If neither global nor per-listener setting is available, the default value for each setting is used.

* Per-listener setting: A setting that applies only to the target listener.
* Global setting: If the target listener doesn't have a per-listener setting, this setting is applied.

#### Format of Per-Listener Setting
Per-listener settings can be set by appending a prefix representing the listener to the global settings key. The prefix representing the listener is the service object's port protocol (`spec.ports[].protocol`) and port number (`spec.ports[].port`) concatenated with a dash (`-`). For example, if the protocol is TCP and the port number is 80, the prefix is ​​`TCP-80`. If you want to set session affinity for the listener associated with this port, you can set it in TCP-80.loadbalancer.nhncloud/pool-session-persistence under .metadata.annotations.

The manifest below is an example of mixing the global setting and per-listener settings. 

```yaml
apiVersion: v1
kind: Service
metadata:
  name: echosvr-svc
  labels:
    app: echosvr
  annotations:
    # Global setting
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

When this manifest is applied, the per-listener settings are set as shown in the following table.

| Item | TCP-80 listener | TCP-433 listener| Description |
| --- | --- | --- | --- |
| Load Balancing Method | SOURCE_IP | LEAST_CONNECTIONS | TCP-80 listener is set to SOURCE_IP according to the global setting<br>TCP-443 listener is set to LEAST_CONNECTIONS according to the per-listener setting  |
| Session Affinity | SOURCE_IP | None | TCP-80 listener is set to SOURCE_IP according to the per-listener setting<br>TCP-443 listener is set to None by the default value |
| Listener Protocol | HTTP | TCP | Both the TCP-80 listener and the TCP-443 listener are set according to the per-listener settings   |

> [Note]
The features without additional version information are only applicable to clusters of Kubernetes v1.19.13 or later.
For clusters of Kubernetes v1.19.13 version, per-listener settings apply only to clusters created on January 25, 2022 or after.
>

> [Caution]
All setting values for the features below must be entered in string format. In the YAML file input format, to enter in string format regardless of the input value, enclose the input value in double quotation marks ("). For more information about the YAML file format, see [Yaml Cookbook](https://yaml.org/YAML_for_ruby.html).
>

#### Setting Load Balancer Name

You can set a name for the load balancer.

* The setting location is loadbalancer.nhncloud/loadbalancer-name under .metadata.annotations.
* Per-listener settings cannot be applied.
* Only alphanumerics, -, and _ are allowed.
    * If it contains invalid characters, the load balancer name is set according to the default load balancer name form.
    * Default load balancer name form: "kube_service_{CLUSTER_UUID}_{SERVICE_NAMESPACE}_{SERVICE_NAME}"
* The maximum length is 255 characters, and the load balancer name is truncated to 255 characters if the maximum length is exceeded.

> [Caution]
The following cases can cause serious malfunction of the load balancer.
> * Modifying the load balancer name after the service object is created
> * Creating a load balancer with a duplicate name within the same project

#### Set load balancer type
You can set the load balancer type. For more information, see [Load Balancer Console User Guide](/Network/Load%20Balancer/en/console-guide/).

* The setting location is loadbalancer.nhncloud/loadbalancer-type under .metadata.annotations.
* Per-listener settings cannot be applied.
* It can be set to one of the following.
    * shared: A load balancer in the 'regular' type is created. Default value when not set.
    * dedicated: A load balancer in the ‘dedicated’ type is created.
    * physical_basic: A load balancer in the 'physical basic' type is created.
    * physical_premium: A load balancer in the 'physical premium' type is created.

> [Caution]
Physical load balancer is only provided in Korea (Pyeongchon) region.
You cannot attach physical load balancers to floating IPs. Instead, a public IP that is automatically assigned when creating the physical load balancer is used as an IP to receive traffic targeted for balancing. This public IP is shown as a service IP in the console.
For the above characteristics, you cannot see the exact status of the load balancer (including the associated floating IP) through Kubernetes service objects. Please check the status of physical load balancers in the console.

#### Set Static Routes
You can set whether the load balancer applies static routes. 

* The setting is located in loadbalancer.nhncloud/apply-subnet-host-routes under .metadata.annotaions.
* Per-listener settings cannot be applied.
* It can be set to one of the following.
    * true: Apply a static route.
    * false: Do not apply static routes. Default when not set.

> [Caution]
Static route settings are available for clusters created on or after August 27, 2024 or that have upgraded their version of K8s.

#### Set the session affinity
You can set the session affinity for the load balancer.

* The setting location is loadbalancer.nhncloud/pool-session-persistence under .metadata.annotations.
* Per-listener settings can be applied.
* It can be set to one of the following.
    * Empty string (""): Set session affinity to 'None'. The default when not set.
    * SOURCE_IP: Set session affinity to SOURCE_IP.
* If the load balancing method is SOURCE_IP, the session affinity setting is ignored, and the session affinity setting is set to None.
* Clusters of v1.17.6, v1.18.19
    * Changes cannot be made after the load balancer is created.
* Clusters of v1.19.13 or later
    * The setting can be changed even after the load balancer is created.

#### Set whether to keep a floating IP address
The load balancer has a floating IP associated with it. You can set whether to delete or keep the floating IP associated with the load balancer when deleting the load balancer and changing the floating IP.

* The setting location is loadbalancer.openstack.org/keep-floatingip under .metadata.annotations.
* Per-listener settings cannot be applied.
* It can be set to one of the following.
    * true: Keep the floating IP.
    * false: Delete the floating IP. The default when not set.

> [Note]
> If floating IP address preservation is not enabled (default value: false), floating IPs that meet all of the following conditions will be automatically deleted when a load balancer is deleted or a floating IP is changed:
>
> * Floating IPs automatically created when a service object is created
> * Floating IPs do not have deletion protection enabled
>
> Floating IPs that do not meet the above conditions will not be deleted, regardless of whether floating IP address preservation is enabled.

> [Caution]
v1.18.19 clusters created before October 26, 2021 have an issue where floating IPs are not deleted when the load balancer is deleted. If you contact us through 1:1 inquiry of the Customer Center, we will provide detailed information on the procedure to solve this issue.


#### Set the load balancer IP
You can set the load balancer IP when creating a load balancer.

* The setting location is .spec.loadBalancerIP.
* Per-listener settings cannot be applied.
* It can be set to one of the following.
  * Empty string(""): Associate an automatically created floating IP with the load balancer. The default when not set.
  * <Floating_IP>: Associate the existing floating IP with the load balancer. It can be used when there is a floating IP that is allocated and not associated.

The following is an example of manifest for associating a custom floating IP to the load balancer.

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

#### Set whether to use the floating IP
You can set whether to use floating IPs when creating the load balancer.

* The setting location is service.beta.kubernetes.io/openstack-internal-load-balancer under .metadata.annotaions.
* Per-listener settings cannot be applied.
* It can be set to one of the following.
  * true: Use a VIP (Virtual IP), not a floating IP.
  * false: Use a floating IP. The default when not set.
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

| Set whether to use the floating IP | Set the load balancer IP | Description |
| --- | --- | --- |
| false | not set | Associate a floating IP with the load balancer. |
| false | set | Associate a specified floating IP with the load balancer. |
| true | not set | Automatically set a VIP associated with the load balancer. |
| true | set | Associate a specified VIP with the load balancer. |


#### Set VPC
You can set a VPC to which the load balancer is connected when creating a load balancer.

* The setting location is loadbalancer.openstack.org/network-id under .metadata.annotaions.
* Per-listener settings cannot be applied.
* If not set, it is set to the VPC configured when creating the cluster.

#### Set Subnet
You can set a subnet to which the load balancer is connected when creating a load balancer. The load balancer's private IP is connected to the set subnet. If no member subnet is set, worker nodes connected to this subnet are added as load balancer members.

* The setting location is loadbalancer.openstack.org/subnet-id under .metadata.annotaions.
* Per-listener settings cannot be applied.
* If not set, it is set to the subnet configured when creating the cluster.

Below is an manifest example of setting a VPC and subnet for the load balancer.

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

#### Set Member Subnet
When creating a load balancer, you can set a subnet to which load balancer members will be connected. Worker nodes connected to this subnet are added as load balancer members.

* The setting is located at loadbalancer.nhncloud/member-subnet-id under .metadata.annotaions.
* Per-listener settings cannot be applied.
* If not set, the load balancer's subnet setting is applied.
* The member subnet must be in the same VPC as the load balancer subnet.
* To set up more than one member subnet, enter them as a comma-separated list.

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
> If you set the load balancer's subnet and member subnets to be different, you must be careful with your network settings. Refer to the following examples.
>
> **Example 1.**
> 
> * Subnet of the load balancer: Subnet#1
> * Member subnet of the load balancer: Subnet#2
> * Set the subnet of the instance's network interface
>     * eth0: Subnet#1
>     * eth1: Subnet#2 (member)
> 
> In this case, the IP address of instance eth1 is registered as a member. Healthcheck packets sent by the load balancer are received by instance eth1 and attempted to be sent through eth0. Please note that the source IP address of the packet to eth0 is different from the IP address of eth0. If source/destination check is enabled on eth0's network interface, this packet will not be sent and discarded. In a configuration like this, you must disable source/destination check on eth0's network interface for the member to function properly. For more information on the source/destination check feature, see [Change source/target check](/Network/Network%20Interface/en/console-guide/#change-sourcetarget-check).
> 
> **Example 2.**
> 
> * Subnet of the load balancer: Subnet#1
> * Member subnet of the load balancer: Subnet#2
> * Set the subnet of the instance's network interface
>     * eth0: Subnet#3
>     * eth1: Subnet#2 (member)
> 
> In this case, the IP address of instance eth1 is registered as a member. Healthcheck packets sent by the load balancer are received by instance eth1.The response packets must be sent to the VIP of the load balancer, but, since subnet#1 is not a directly connected network, the egress interface is determined by the routing table. To enable communication without setting up the source/destination verification feature on the network interface, you must set up routing to allow the traffic destined for the load balancer's VIP to be sent through eth1. 

> [Caution]
> Member subnets can be set up on clusters that have been upgraded to v1.24.3 or later after November 28, 2023, or are newly created.

#### Set the listener connection limit
You can set the connection limit for a listener.

* The setting location is loadbalancer.nhncloud/connection-limit under .metadata.annotations.
* Per-listener settings can be applied.
* Clusters of v1.17.6, v1.18.19
    * Minimum value of 1, maximum value of 60000. 
    * It you do not set it, it is set to -1, and the value applied to the actual load balancer is 2000.
* Clusters of v1.19.13 or later
    * Minimum value of 1, maximum value of 60000. 
    * If not set or a value out of range is entered, it is set to the default value of 60000.


#### Set the listener protocol
You can set the protocol of the listener.

* The setting location is loadbalancer.nhncloud/listener-protocol under .metadata.annotations.
* Per-listener settings can be applied.
* It can be set to one of the following.
    * TCP: The default when not set.
    * HTTP
    * HTTPS
    * TERMINATED_HTTPS: Set it to TERMINATED_HTTPS. SSL version, certificate, and private key information must be set additionally.

> [Caution]
The listener protocol setting is not applied to the load balancer even if you change the service object.
To change the listener protocol setting, you must delete the service object and then create it again.
Note that the load balancer is deleted and then re-created in this case.


The SSL version can be set as follows:

* The setting location is loadbalancer.nhncloud/listener-terminated-https-tls-version under .metadata.annotations.
* Per-listener settings can be applied.
* It can be set to one of the following.
    * TLSv1.3: The default when not set.
    * TLSv1.2
    * TLSv1.1
    * TLSv1.0_2016
    * TLSv1.0
    * SSLv3

> [Caution]
TLSv1.3 can be set in clusters created after March 29, 2022.

Certificate information can be set as follows:

* The setting location is loadbalancer.nhncloud/listener-terminated-https-cert under .metadata.annotations.
* Per-listener settings can be applied.
* It must include start and end lines.

Private key information can be set as follows:

* The setting location is loadbalancer.nhncloud/listener-terminated-https-key under .metadata.annotations.
* Per-listener settings can be applied.
* It must include start and end lines.

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

Instead of registering certificate and private key information in the manifest, you can create a listener of type TERMINATED_HTTPS with a certificate registered in Certificate Manager.

* The setting location is loadbalancer.nhncloud/listener-terminated-https-cert-manager-name under .metadata.annotations.
* The value is the name of the certificate you enrolled in Certificate Manager.
* Per-listener settings can be applied.

The following is an example manifest that uses a certificate enrolled in Certificate Manager when setting the listener protocol to TERMINATED_HTTPS.

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


#### Set the listener proxy protocol
When the listener protocol is TCP or HTTPS, you set the proxy protocol to the listener. For more information on proxy protocol, see [Load Balancer Proxy Mode](/Network/Load%20Balancer/en/overview/#_4)

* The setting location is loadbalancer.nhncloud/listener-protocol under .metadata.annotations.
* Per-listener settings can be applied.
* It can be set to one of the following.
    * true: Enable the proxy protocol.
    * false: Disable the proxy protocol. Default when not set.

#### Set the load balancing method
You can set the load balancing method.

* The setting location is loadbalancer.nhncloud/pool-lb-method under .metadata.annotations.
* Per-listener settings can be applied.
* It can be set to one of the following.
    * ROUND_ROBIN: The default when not set.
    * LEAST_CONNECTIONS
    * SOURCE_IP


#### Set the health check protocol
You can set the health check protocol.

* The setting location is loadbalancer.nhncloud/healthmonitor-type under .metadata.annotations.
* Per-listener settings can be applied.
* It can be set to one of the following.
    * HTTP: You must additionally set HTTP URL, HTTP method, and HTTP status code.
    * HTTPS: You must additionally set HTTP URL, HTTP method, and HTTP status code.
    * TCP: The default when not set.

The HTTP URL can be set as follows:

* The setting location is loadbalancer.nhncloud/healthmonitor-http-url under .metadata.annotations.
* Per-listener settings can be applied.
* The setting value must start with /.
* If not set or a value that does not match the rule is entered, it is set to the default value of /.

The HTTP method can be set as follows:

* The setting location is loadbalancer.nhncloud/healthmonitor-http-method under .metadata.annotations.
* Per-listener settings can be applied.
* Currently, only GET is supported. If not set or another value is entered, it will be set to GET, which is the default value.

The HTTP status code can be set as follows:

* The setting location is loadbalancer.nhncloud/healthmonitor-http-expected-code under .metadata.annotations.
* Per-listener settings can be applied.
* You can enter the value in the form of a single value (e.g. 200), a list (e.g. 200,202), or a range (e.g. 200-204).
* If not set or a value that does not match the rule is entered, it is set to the default value of 200.

#### Set the health check interval
You can set the health check interval.

* The setting location is loadbalancer.nhncloud/healthmonitor-delay under .metadata.annotations.
* Per-listener settings can be applied.
* Set the value in seconds.
* Minimum value of 1, maximum value of 5000.
* If not set or a value out of range is entered, it is set to the default value of 60.

#### Set the health check maximum response time
You can set the maximum response time for health checks.

* The setting location is loadbalancer.nhncloud/healthmonitor-timeout under .metadata.annotations.
* Per-listener settings can be applied.
* Set the value in seconds.
* Minimum value of 1, maximum value of 5000.
* This setting must be smaller than the Health check interval setting setting value.
* If not set or a value out of range is entered, it is set to the default value of 30.
* However, if the input value or setting value is greater than the Health check interval setting, it is set to 1/2 of the Health check interval setting setting value.

#### Set the maximum number of retries for a health check
You can set the maximum number of retries for a health check.

* The setting location is loadbalancer.nhncloud/healthmonitor-max-retries under .metadata.annotations.
* Per-listener settings can be applied.
* Minimum value of 1, maximum value of 10.
* If not set or a value out of range is entered, it is set to the default value of 3.

#### Health Check Port Settings
You can set the member port for health checks.

* The configuration location is loadbalancer.nhncloud/healthmonitor-health-check-port under .metadata.annotations.
* You can apply listener-specific settings.
* The minimum value is 0 and the maximum value is 65,535.
* If 0 is specified, health checks will be performed on the specified port number for each member.
* If not specified or a value out of range is entered, the default value is 0.

#### Health Check Host Header Settings
You can set the host header field value to be used for health checks.

* The configuration location is loadbalancer.nhncloud/healthmonitor-http-host-header under .metadata.annotations.
* You can apply listener-specific settings.
* If the health check protocol is set to TCP, the value set in this field is ignored.

#### Setting keep-alive timeout
You can set a keep-alive timeout value.

* The setting is located at loadbalancer.nhncloud/keepalive-timeout under .metadata.annotations.
* Per-listener settings can be applied.
* Set the value in seconds.
* The minimum value is 0 and the maximum is 3600.
* If not set or a value out of range is entered, it is set to the default value of 300.

> [Caution]
> The keep-alive timeout can be set up on clusters that have been upgraded to v1.24.3 or later after November 28, 2023, or are newly created.

#### L7 Rules
You can set L7 rules on a per-listener basis. L7 rules work as follows

* L7 rules can only be created when the protocol of the listener is HTTP or TERMINATED_HTTPS.
* L7 rules are applied in the following order based on the action type: block, forward to URL, forward to member group.
* Within the same task type, smaller index values are prioritized higher.
* A member group is created that contains nodes connected to the member subnet, and this member group is set as the default member group for the listener.

L7 rules can be set up as follows

* You can set up to 10 L7 rules on a single listener.
* To identify each L7 rule, use the format `l7policy-%d`(where`%d is`an index starting at 0) in the settings location.

| Set up location | Meaning | Required | Value |
| --- | --- | :-: | --- |
| {LISTENER_SPEC}.{L7POLICY}.loadbalancer.nhncloud/name | Name | O | String of 255 characters or less |
| {LISTENER_SPEC}.{L7POLICY}.loadbalancer.nhncloud/description | Description | X | String of 255 characters or less |
| {LISTENER_SPEC}.{L7POLICY}.loadbalancer.nhncloud/action | Task type | O | One of REDIRECT_TO_POOL (forward to a member group), REDIRECT_TO_URL (forward to a URL), or REJECT (block). |
| {LISTENER_SPEC}.{L7POLICY}.loadbalancer.nhncloud/redirect-url | URL to redirect | X (but required if the action type is REDIRECT_TO_URL) | URLs that start with `HTTP://` or `HTTPS://` |

> [Note]
> * {LISTENER_SPEC} is in the form `[TCP|UDP]-%d`, ` where %d`is the port number (e.g., TCP-80).
> * {L7POLICY} is in the form `l7policy-%d`, ` where %d`is an index starting at 0. (For example, l7policy-0)

L7 rule settings have the following limitations

* The index used for setting L7 rules can be any integer value between 0-9.
* L7 rules that are set on one listener must be set to different index values.
* L7 rules that are set on one listener must be set to different names.

#### L7 Conditions
You can set L7 conditions per L7 rule. L7 conditions work as follows

* All L7 conditions in an L7 rule must be satisfied for that L7 rule to take effect.
* No priority among L7 conditions.

L7 conditions can be set as follows:

* You can set up to 10 L7 conditions in a single L7 rule.
* To identify each L7 condition, we use the format of `rule-%d``(where %d is`an index starting at 0) in the configuration location.

| Set up location | Meaning | Required | Value |
| --- | --- | :-: | --- |
| {LISTENER_SPEC}.{L7POLICY}.{RULE}.loadbalancer.nhncloud/type | Type | O | One of the following: HOST_NAME, PATH, FILE_TYPE, HEADER, or COOKIE |
| {LISTENER_SPEC}.{L7POLICY}.{RULE}.loadbalancer.nhncloud/compare-type | Comparison method| O |One of REGEX, STARTS_WITH, ENDS_WITH, CONTAINS, EQUAL_TO <br>(However, if the type is FILE_TYPE, only EQUAL_TO and REGEX are available)|
| {LISTENER_SPEC}.{L7POLICY}.{RULE}.loadbalancer.nhncloud/key | Key | X (but required if type is HEADER, COOKIE) | String of 255 characters or less |
| {LISTENER_SPEC}.{L7POLICY}.{RULE}.loadbalancer.nhncloud/value | Value | O | String of 255 characters or less |

> [Note]
> * {RULE} is in the form of `rule-%d`, ` where %d`is an index starting at 0. (For example, rule-0)

L7 conditions have the following limitations

* The index used to set the L7 condition can be any integer value between 0-9.
* L7 conditions that are set in one L7 rule must be set to different index values.
* L7 conditions with the same specification (conditions that have the same type, comparison method, key, and value) cannot be added to a single L7 rule.

> [Caution]
L7 rules and L7 conditions can be set on clusters upgraded to v1.24.3 or later after July 23, 2024 or newly created.

The following is an example of setting up L7 rules and conditions.

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
## Ingress Controller
Ingress Controller routes HTTP and HTTPS requests from cluster externals to internal services, in reference of the rules that are defined at ingress object so as to provide SSL/TSL closure and virtual hosting. For more details on Ingress Controller and Ingress, see [Ingress Controller](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/), [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/).

<a id="install-nginx-ingress-controller"></a>
### Installing NGINX Ingress Controller
NGINX Ingress Controller is one of the most frequently used ingress controllers. For more details, see [NGINX Ingress Controller](https://kubernetes.github.io/ingress-nginx/) and [NGINX Ingress Controller for Kubernetes](https://www.nginx.com/products/nginx-ingress-controller/). For installation of NGINX Ingress Controller, see [Installation Guide](https://kubernetes.github.io/ingress-nginx/deploy/).

<a id="uri-based-service-routing"></a>
### Diverging Service on URI
Ingress controller can diverge services based on URI. The following figure shows the structure of a simple example of service divergence based on URI.

![ingress-01.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/ingress-01.png)

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

Likewise, request for `/tea` as endpoint is delivered to the `tea-svc` service so as the `tea` can respond.

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
### Service Divergence on Host
Ingress controller can diverge services based on the host name. The following figure shows the structure of a simple example of service divergence based on the host name.

![ingress-02.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/ingress-02.png)

#### Create Services and Pods
Create services and pods by using the same manifest as [URI-based Service Divergence](/Container/NKS/en/user-guide/#uri).

#### Create Ingress
Write the ingress manifest connecting services based on the host name. Incoming request via the `tea.cafe.example.com` host is connected to the `tea-svc` service, while request via the `coffee.cafe.example.com` host is connected to the `coffee-svc` service.

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

#### Send HTTP Requests
HTTP request is sent from external host to IP configured at the ADDRESS of the ingress controller. Nevertheless, such request must be sent by using host name, since service divergence is based on the host name by configuration.

> [Note]
To test with a random host name, use the --resolve option of curl: enter the --resolve option in the `{Host Name}:{Port Number}:{IP}`format. This means to resolve a request for {Port Number} to be sent to {Host Name} as {IP}.
You may open up the `/etc/host` file and add `{IP} {Host Name}`.

When a request is sent to the `coffee.cafe.example.com` host, it is delivered to`coffee-svc` so that the `coffee` pod can respond.

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
### Internal communication structure and cautions for the ingress-nginx controller
When exposing a service externally through the ingress-nginx controller, the path the request takes to reach the workload varies depending on the location of the requesting client (inside or outside the cluster).

#### Cluster External Client
Requests from clients outside the cluster are forwarded to the Ingress Controller via the load balancer. The load balancer acts as an external endpoint for the Ingress Controller Service, and the Ingress Controller routes requests to the destination backend pod based on the Ingress rules.

```
Cluster External Client → Load Balancer → ingress-nginx Service → ingress-nginx Controller Pod → Backend Pod
```

#### Cluster Internal Client
When a Pod within the cluster makes a request to the Ingress address, the traffic bypasses the load balancer. The request is forwarded directly to the internal route via the ClusterIP of the Ingress Controller Service, and is routed according to the CNI as follows:

- **Calico (VXLAN)**: based on kube-proxy's iptables rules
- **Calico (eBPF)**: use BPF MAP-based data path

In both cases, traffic is routed only within the internal network and does not go through an external load balancer.
```
내부 Pod → ingress-nginx Service (ClusterIP) → ingress-nginx Controller Pod → Backend Pod
```

#### Cautions

- Internal requests are not subject to load balancer policies. The load balancer's TLS settings, security policies, firewall rules, etc. do not affect internal traffic.
- If the Ingress domain is called internally, it may not go through the load balancer, resulting in different TLS or redirect behavior than external traffic.
- We recommend using Service DNS instead of Ingress domains for internal communication. It's recommended to use the Service directly for internal Pod-to-Pod communication, and only use Ingress for externally exposed endpoints.


<a id="k8s-dashboard"></a>
## Kubernetes Dashboard
NHN Kubernetes Service (NKS) provides the default web UI dashboard. For more details on Kubernetes Dashboard, see [Web UI (Dashboard)](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/).

> [Caution]
> * Kubernetes Dashboard supports up to NKS v1.25.4 by default.
> * If you upgrade your NKS cluster version from v1.25.4 to v1.26.3, the Kubernetes Dashboard pods and related resources will remain in action.
> * You can view Kubernetes resources from the NHN Cloud console.

<a id="expose-dashboard"></a>
### Opening Dashboard Services
User Kubernetes has the `kubernetes-dashboard` service object which has been already created to publicly open dashboard.

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

However, the `kubernetes-dashboard` object belongs to ClusterIP type and is not open out of the cluster. To open up dashboard externally, the service object type must be changed into LoadBalancer, or ingress controller and ingress object must be created.

#### Change into LoadBalancer

Once the type of service object is changed into `LoadBalancer`, NHN Cloud Load Balancer is created out of the cluster, which is associated with load balancer and service object. By querying service object associated with the load balancer, IP of the load balancer is displayed in the **EXTERNAL-IP** field. See [LoadBalancer Service](/Container/NKS/en/user-guide/#loadbalancer) for the description of service objects of the `LoadBalancer` type. The following figure shows the structure of making dashboard public using the `LoadBalancer` type service.

![dashboard-01.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/dashboard-01.png)

Change the type of the `kubernetes-dashboard` service object to `LoadBalancer`, like below:

```
$ kubectl -n kube-system patch svc/kubernetes-dashboard -p '{"spec":{"type":"LoadBalancer"}}'
service/kubernetes-dashboard patched
```

After the type of service object `kubernetes-dashboard` is changed to `LoadBalancer`, you can check load balancer IP from the **EXTERNAL-IP** field.

```
$ kubectl get svc -n kube-system
NAME                   TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)                  AGE
...
kubernetes-dashboard   LoadBalancer   10.254.95.176   123.123.123.81   443:30963/TCP            2d23h
```

> [Note]
You can view the created load balancer on the **Network > Load Balancer** page.
Load balancer IP is a floating IP allowing external access. You can check it on the **Network > Floating IP** page.

If you access `https://{EXTERNAL-IP}` in a web browser, the Kubernetes dashboard page is loaded. See [Dashboard Access Token](/Container/NKS/en/user-guide/#dashboard-access-token) for the token required for login.

> [Note]
Since Kubernetes dashboard is based on a private certificate that is automatically created, the page may be displayed as unsafe, depending on the web browser or security setting.

#### Open Services with Ingress

Ingress refers to the network object providing routing to access many services within a cluster. The setting of an ingress object runs by ingress controller. The `kubernetes-dashboard` service object can go public through ingress. See [Ingress Controller](/Container/NKS/en/user-guide/#ingress-controller) regarding description on ingress and ingress controller. The following figure shows the structure of making dashboard public through ingress.

![dashboard-02.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/dashboard-02.png)

Install `NGINX Ingress Controller` by referring to [Install NGINX Ingress Controller](/Container/NKS/en/user-guide/#nginx-ingress-controller) and write the manifest for creating an ingress object as follows.

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

Apply the manifest to create an ingress and check the **ADDRESS** field of the ingress object.

```
$ kubectl apply -f kubernetes-dashboard-ingress-tls-passthrough.yaml
ingress.networking.k8s.io/k8s-dashboard-ingress created

$ kubectl get ingress -n kube-system
NAME                    CLASS   HOSTS   ADDRESS          PORTS     AGE
k8s-dashboard-ingress   nginx   *       123.123.123.44   80, 443   34s
```

If you access `https://{ADDRESS}` in a web browser, the Kubernetes dashboard page is loaded. See [Dashboard Access Token](/Container/NKS/en/user-guide/#dashboard-access-token) for the token required for login.

<a id="dashboard-access-token"></a>
### Dashboard Access Token
A token is required to log in to the Kubernetes dashboard. You can get the token with the following command:

```
# SECRET_NAME=$(kubectl -n kube-system get secrets | grep "kubernetes-dashboard-token" | cut -f1 -d ' ')

$ kubectl describe secret $SECRET_NAME -n kube-system | grep -E '^token' | cut -f2 -d':' | tr -d " "
eyJhbGc...-QmXA
```

Enter the printed token in the token input window on the browser, and you can log in as a user that has been granted the cluster admin privileges.

<a id="persistent-volume"></a>
## Persistent Volume
Persistent Volume or PV is a Kubernetes resource representing physical storage volume. One PV is attached to one NHN Cloud Block Storage. For more details, see [Persistent Volume](https://kubernetes.io/docs/concepts/storage/persistent-volumes/).

Persistent Volume Claims, or PVC is required to attach PV to pods. PVC defines necessary volume requirements, including volume and read/write modes.

With PV and PVC, user can define the attributes of a volume of choice, while the system seperates the use of resources from management by assigning volume resources for each user requirement.

<a id="pv-lifecycle"></a>
### Life Cycle of PV/PVC
PV and PVC support the four-phase life cycle.

* Provisioning
Using [storage classes](https://kubernetes.io/docs/concepts/storage/storage-classes/), users can manually secure a volume and create a PV (static provisioning) or dynamically create a PV (dynamic provisioning).

* Binding
Bind PV to PVC 1:1. With dynamic provisioning, binding is also automatically executed along with PV provisioning.

* Using
Mount PV to a pod to make it enabled.

* Reclaiming
Volume is reclaimed after it is used up, in three methods: Delete, Retain, or Recycle.

| Method | Description |
| --- | --- |
| Delete | PV is deleted along with its attached volumes. |
| Retain | PV is deleted but its attached volumes are not: volumes can be deleted or reused by user. |
| Recycle | PV is deleted and its attached volumes are made to be reused without being deleted. This method has been deprecated. |

<a id="storageclass"></a>
### Storage Class (StorageClass)
A storage class must be defined before provisioning. Storage classes provide a way to classify storages by certain characteristics. You can set the information such as media type and availability zone by including information about the storage provider (provisioner). 

#### Storage Provider (provisioner)
Set the provider information of storage. Depending on the Kubernetes version, the supported storage provider information is as follows:

* v1.19.13 or earlier: The provisioner field must be set to `kubernetes.io/cinder`.
* v1.20.12 or later: You can use the provisioner field by setting it to `cinder.csi.openstack.org`.

#### Parameters (parameter)
The storage class allows you to set the following parameters:

* Storage type (type): Enter the type of storage. (If not entered, General HDD is set.)
    * **General HDD**: The storage type is set to HDD.
    * **General SSD**: The storage type is set to SSD.
* Availability zone (availability): Set the availability zone. (If not entered, it will be set randomly.)
    * Pangyo Region: **kr-pub-a** or **kr-pub-b**
    * Pyeongchon region: **kr2-pub-a** or **kr2-pub-b**

#### Volume Binding Mode (VolumeBindingMode)
A volume binding mode controls the time when volume binding and dynamic provisioning start. This setting is configurable only if the storage provider is cinder.csi.openstack.org. 

* **Immediate**: Volume binding and dynamic provisioning start as soon as a persistent volume claim is created. When the persistent volume claim is created, there is no prior knowledge of the pods to which the volume will be attached. Therefore, if the availability zone of the volume and the availability zone of the node on which the pod will be scheduled are different, the pod will not work properly. 
* **WaitForFirstConsumer**: Volume binding and dynamic provisioning are not performed when a persistent volume claim is created. When this persistent volume claim is attached to a pod for the first time, volume binding and dynamic provisioning are performed based on the availability zone information of the node on which the pod is scheduled. Therefore, there is no case where the pod does not work properly because the availability zone of the volume and the availability zone of the instance are different, such as in Immediate mode.

#### Allow Volume Expansion (allowVolumeExpansion)
Set whether to allow expansion of the created volume (if not input, false is set).

* **True** : Volume expansion is allowed.
* **False** : Volume expansion is not allowed.

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

Create the storage class and check that it has been created.

```
$ kubectl apply -f storage_class.yaml
storageclass.storage.k8s.io/sc-ssd created

$ kubectl get sc
NAME     PROVISIONER            RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
sc-ssd   kubernetes.io/cinder   Delete          Immediate           false                  3s
```

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

Create the storage class and check that it has been created.

```
$ kubectl apply -f storage_class_csi.yaml
storageclass.storage.k8s.io/csi-storageclass created

$ kubectl get sc
NAME               PROVISIONER                RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
csi-storageclass   cinder.csi.openstack.org   Delete          WaitForFirstConsumer   false                  7s
```

<a id="static-provisioning"></a>
### Static Provisioning

Static provisioning requires the user to prepare a block storage manually. On the **Storage > Block Storage** service page of the NHN Cloud web console, click the **Create Block Storage** button to create a block storage to attach to the PV. See [Create Block Storage](/Storage/Block%20Storage/en/console-guide/#create-block-storage) in the Block Storage guide.

To create a PV, you need the ID of the block storage. On the **Storage > Block Storage** service page, select the block storage you want to use from the block storage list. You can find the ID under the block storage name section in the **Information** tab at the bottom.

Write a manifest for the PV to be attached to the block storage. Enter the storage class name in **spec.storageClassName**. To use NHN Cloud Block Storage, **spec.accessModes** must be set to `ReadWriteOnce`. **spec.presistentVolumeReclaimPolicy** can be set to `Delete` or `Retain`.

Clusters in v1.20.12 and later must use the **cinder.csi.openstack.org** storage provider. To define the storage provider, **specify** the value `pv.kubernetes.io/provisioned-by: cinder.csi.` `openstack`. `org` under **spec.annotations** and the value `driver: cinder.csi.openstack.org` under **csi** entries.

> [Caution]
You must set a storage class in which the storage provider suitable for your Kubernetes version has been defined.

```yaml
# pv-static.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  annotations: 
    pv.kubernetes.io/provisioned-by: 
cinder.csi.openstack.org
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

Create the PV and check that it has been created.

```
$ kubectl apply -f pv-static.yaml
persistentvolume/pv-static-001 created

$ kubectl get pv -o wide
NAME            CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE   VOLUMEMODE
pv-static-001   10Gi       RWO            Delete           Available           sc-default              7s    Filesystem
```

Write a PVC manifest to use the created PV. The PV name must be specified in **spec.volumeName**. Set other items the same as PV manifest.

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

Create and check PVC.

```
$ kubectl apply -f pvc-static.yaml
persistentvolumeclaim/pvc-static created

$ kubectl get pvc -o wide
NAME         STATUS   VOLUME          CAPACITY   ACCESS MODES   STORAGECLASS   AGE   VOLUMEMODE
pvc-static   Bound    pv-static-001   10Gi       RWO            sc-default     7s    Filesystem
```

After the PVC is created, query PV status, and you can find PVC name specified for **CLAIM** and **STATUS** changed into `Bound`.

```
$ kubectl get pv -o wide
NAME            CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                STORAGECLASS   REASON   AGE   VOLUMEMODE
pv-static-001   10Gi       RWO            Delete           Bound    default/pvc-static   sc-default              79s   Filesystem
```

<a id="dynamic-provisioning"></a>
### Dynamic Provisioning

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
A block storage created by dynamic provisioning cannot be deleted from the web console. It is not automatically deleted along with a cluster being deleted. Therefore, before a cluster is deleted, all PVCs must be deleted first; otherwise, you may be charged for PVC usage. The reclaimPolicy of PV created by dynamic provisioning is set to `Delete` by default, so deleting only the PVC will also delete the PV and block storage.

<a id="pod-pvc-mount"></a>
### Mounting PVC to Pods

To mount PVC to a pod, mount information must be defined at the pod manifest. Enter the PVC name to use in `spec.volumes.persistenVolumeClaim.claimName`; enter paths to mount in `spec.containers.volumeMounts.mountPath`.

In the following example, a PVC created with static provisioning is mounted to `/usr/share/nginx/html` of the pod.

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

Create the pod and see if the block storage has been mounted.

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
### Volume Expansion
You can adjust an existing volume by editing the PersistentVolumeClaim (PVC) object. You can change the volume size by editing the **spec.resources.requests.storage** item of the PVC object. Volume shrinking is not supported. To use the volume expansion feature, the **allowVolumeExpansion** property of StorageClass must be **True**.


#### Volume Expansion from v1.19.13 and older
**kubernetes.io/cinder**, the storage provider from v1.19.13 and older does not provide the volume expansion feature for the volume in use. To use the feature for the volume in use, you must use **cinder.csi.openstack.org**, the storage provider from v1.20.12 and later. The cluster upgrade feature allows you to upgrade the version to v1.20.12 or later in order to use the storage provider **cinder.csi.openstack.org**.

To use the storage provider **cinder.csi.openstack.org**from v1.20.12 and older instead of the storage provider **kubernetes.io/cinder** from v1.19.13 and older, you must modify the annotations of PVC as follows.

* pv.kubernetes.io/bind-completed: "yes" > Delete
* pv.kubernetes.io/bound-by-controller: "yes" > Delete
* volume.beta.kubernetes.io/storage-provisioner: kubernetes.io/cinder > volume.beta.kubernetes.io/storage-provisioner:cinder.csi.openstack.org
* volume.kubernetes.io/storage-resizer: kubernetes.io/cinder > volume.kubernetes.io/storage-resizer: cinder.csi.openstack.org
* pv.kubernetes.io/provisioned-by:cinder.csi.openstack.org > Add


Below is a modified PVC example.

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

#### Volume Expansion from v1.20.12 and older
The storage provider **cinder.csi.openstack.org** from v1.20.12 and later supports the expansion of the volume in use by default. You can change the volume size by modifying the **spec.resources.requests.storage** item of the PVC object to a desired value.

<a id="service-integration"></a>
## Integrate with NHN Cloud Service

<a id="ncr-integration"></a>
### Integrate with NHN Cloud Container Registry (NCR)
You can use images saved at NHN Cloud Container Registry. To use images registered in the registry, first create a secret to login to user registry.

To use NHN Cloud (Old) Container Registry, you need to create a secret as follows:

```
$ kubectl create secret docker-registry registry-credential --docker-server={user registry address} --docker-username={email address for NHN Cloud account} --docker-password={service Appkey or integrated Appkey}
secret/registry-credential created

$ kubectl get secrets
NAME                  TYPE                             DATA   AGE
registry-credential   kubernetes.io/dockerconfigjson   1      30m
```


To use NHN Cloud Container Registry, you need to create a secret as follows:

```
$ kubectl create secret docker-registry registry-credential --docker-server={User registry address} --docker-username={User Access Key ID} --docker-password={Secret Access Key}
secret/registry-credential created

$ kubectl get secrets
NAME                  TYPE                             DATA   AGE
registry-credential   kubernetes.io/dockerconfigjson   1      30m
```


By adding secret information to the deployment manifest file and changing the name of image, pods can be created by using images saved at user registry.

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
        image: {User registry address}/nginx:1.14.2
        ...
      imagePullSecrets:
      - name: registry-credential

```

> [Note]
Regarding how to use NHN Cloud Container Registry, see [NHN Cloud Container Registry (NCR) User Guide](/Container/NCR/ko/user-guide).

<a id="nas-integration"></a>
### Integrate with NHN Cloud NAS
You can utilize NAS volume provided by NHN Cloud as PV. In order to use NAS services, you must use a cluster of version v1.20 or later. For more information on using NHN Cloud NAS, please refer to the [NAS Console User Guide](/Storage/NAS/en/console-guide).

> [Note]
The NHN Cloud NAS service is currently (2024.08) only available in some regions. For more information on supported regions for NHN Cloud NAS service, see [NAS Service Overview](/Storage/NAS/en/overview).

#### Run the rpcbind service on All Worker Nodes
To use NAS volume, you must run the rpcbind service on all worker nodes. After connecting to all worker nodes, run the rpcbind service with the command below.

The command to run the rpcbind service is the same regardless of the image type.

```
$ systemctl start rpcbind
```

For clusters that are using enforced security rules, you must add security rules.

| Direction | IP protocol | Port range | Ether | Remote | Description | 
| :-: | :-: | :-: | :-: | :-: | :-: | 
| egress | TCP | 2049 | IPv4 | NAS IP address | NFS port in rpc, direction: csi-nfs-node(worker node) → NAS |
| egress | TCP | 111 | IPv4 | NAS IP address | portmapper port in rpc, direction: csi-nfs-node(worker node) → NAS |
| egress | TCP | 635 | IPv4 | NAS IP address |  rpc's mountd port, direction: csi-nfs-node(worker node) → NAS |

#### Install csi-driver-nfs
To use the NHN Cloud NAS service, you must deploy the csi-driver-nfs components.

csi-driver-nfs is a driver that supports NFS storage provisioning that works by creating new subdirectories on NFS storage.
csi-driver-nfs works by presenting NFS storage information to storage classes, reducing what you have to manage.

If you configure multiple PVs using the nfs-csi-driver, the nfs-csi-driver registers the NFS storage information in the StorageClass, removing the need to configure an NFS-Provisioner pod.
<br>
![nfs-csi-driver-02.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/nfs-csi-driver-02.png)

> [Note]
During the internal execution of the csi-driver-nfs execution script, the kubectl apply command is performed. Therefore, the installation should proceed with the `kubectl` command operating normally.
The csi-driver-nfs installation process is based on the Linux environment.

##### 1. Save the absolute path of a cluster configuration file in an environment variable.
```
$ export KUBECONFIG={Absolute path of a cluster configruration file}
```

##### 
ORAS (OCI Registry As Storage) is a tool that provides a way to push and pull OCI artifacts from an OCI registry.
Refer to [](https://oras.land/docs/installation)ORAS installation[](https://oras.land/docs/installation) to install ORAS command line tools. For detailed usage of the ORAS command line tools, see the [](https://oras.land/docs/)ORAS docs[](https://oras.land/docs/).


| Region | Internet Connection | Download Command |
| --- | --- | --- |
| Korea (Pangyo) region | O |  oras pull dfe965c3-kr1-registry.container.nhncloud.com/container_service/oci/nfs-deploy-tool:v1 |
| | X | oras pull private-dfe965c3-kr1-registry.container.nhncloud.com/container_service/oci/nfs-deploy-tool:v1 |
| Korea (Pyengchon) region | O | oras pull 6e7f43c6-kr2-registry.container.cloud.toast.com/container_service/oci/nfs-deploy-tool:v1 |
| | X | oras pull private-6e7f43c6-kr2-registry.container.cloud.toast.com/container_service/oci/nfs-deploy-tool:v1 |


> [Note]
The csi-driver-nfs container images and artifacts are maintained in NHN Cloud NCR. Since the cluster configured in a closed network environment is not connected to the Internet, it is necessary to configure the environment to use a private URI in order to receive images and artifacts normally. For information on how to use Private URI, refer to the [NHN Cloud Container Registry (NCR) User Guide](Container/NCR/ko/user-guide/#private-uri).

##### 3. Unzip the installation package and run **./install-driver.sh {REGISTRY} {INTERNET_USAGE}** command to install the csi-driver-nfs component.
Enter the correct {REGISTRY} and {INTERNET_USAGE} values based on the region where the cluster was created and the availability of internet connectivity. 

* {REGISTRY}
  * Korea (Pangyo) Region: **dfe965c3-kr1-registry.container.nhncloud.com**
  * Korea (Pyeongchon) Region: **6e7f43c6-kr2-registry.container.cloud.toast.com**
* {INTERNET_USAGE}
  * Cluster available for internet connection: **true**
  * Cluster unavailable for internet connection: **false**

The following is an example of installing csi-driver-nfs on an internet-connected cluster created in the Korea (Pangyo) region.

```
$ tar -xvf nfs-deploy-tool.tar

$ ./install-driver.sh dfe965c3-kr1-registry.container.nhncloud.com public
INTERNET_USAGE set to true. Container image registry set with value dfe965c3-kr1-registry.container.nhncloud.com
Installing NFS CSI driver
serviceaccount/csi-nfs-controller-sa created
serviceaccount/csi-nfs-node-sa created
clusterrole.rbac.authorization.k8s.io/nfs-external-provisioner-role created
clusterrolebinding.rbac.authorization.k8s.io/nfs-csi-provisioner-binding created
csidriver.storage.k8s.io/nfs.csi.k8s.io created
deployment.apps/csi-nfs-controller created
daemonset.apps/csi-nfs-node created
NFS CSI driver installed successfully.
```

##### 4. Check that the components are installed properly.
```
$ kubectl get pods -n kube-system
NAMESPACE     NAME                                         READY   STATUS    RESTARTS   AGE
kube-system   csi-nfs-controller-844d5989dc-scphc          3/3     Running   0          53s
kube-system   csi-nfs-node-hmps6                           3/3     Running   0          52s

$ kubectl get clusterrolebinding
NAME                                                                                                ROLE                                                               AGE
clusterrolebinding.rbac.authorization.k8s.io/nfs-csi-provisioner-binding                            ClusterRole/nfs-external-provisioner-role                          52s

$ kubectl get clusterrole
NAME                                                                                                         CREATED AT
clusterrole.rbac.authorization.k8s.io/nfs-external-provisioner-role                                          2022-08-09T06:21:20Z

$ kubectl get csidriver
NAME                                                ATTACHREQUIRED   PODINFOONMOUNT   MODES                  AGE
csidriver.storage.k8s.io/nfs.csi.k8s.io             false            false            Persistent,Ephemeral   47s

$ kubectl get deployment -n kube-system
NAMESPACE     NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
kube-system   coredns                     2/2     2            2           22d
kube-system   csi-nfs-controller          1/1     1            1           4m32s

$ kubectl get daemonset -n kube-system
NAMESPACE     NAME                    DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR                   AGE
kube-system   csi-nfs-node            1         1         1       1            1           kubernetes.io/os=linux          4m23s
```

#### How to Use existing NHN Cloud NAS Volume When Provisioning
You can use existing NAS volume as a PV by entering the NAS information when creating the PV manifest or by entering the NAS information in the StorageClass manifest.

##### Method 1. Define NAS volume information when creating a PV manifest
When creating the PV manifest, you must define the NHN Cloud NAS Volume information. The setting location is **csi** under .spec.

* driver: Enter **nfs.csi.k8s.io**
* readOnly: Enter **false**
* volumeHandle: Enter a unique, non-duplicate id within the cluster.
* volumeAttributes: Enter connection information for NAS volume.
  * server: Enter the value of the **ip** part of the NAS volume connection information.
  * share: Enter the value of the **volume name** part of the NAS volume connection information.

Below is an example manifest.
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

Create the PV and check that it has been created.
```
$ kubectl apply -f pv.yaml
persistentvolume/pv-onas created

$ kubectl get pv -o wide
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM                      STORAGECLASS   REASON   AGE    VOLUMEMODE
pv-onas                                    300Gi      RWX            Retain           Available                                                      101s   Filesystem
```

Write a PVC manifest to use the created PV. The PV name must be specified in **spec.volumeName**. Set other items the same as PV manifest.
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

Create and check PVC.
```
$ kubectl apply -f pvc.yaml
persistentvolumeclaim/pvc-onas created

$ kubectl get pvc -o wide
NAME              STATUS   VOLUME    CAPACITY   ACCESS MODES   STORAGECLASS   AGE    VOLUMEMODE
pvc-onas   Bound    pv-onas   300Gi      RWX                           2m8s   Filesystem
```

If you create a PVC and then check the status of the PV, you can see that the **CLAIM** entry is specified with the PVC name and the STATUS entry is changed to `Bound`.
```
$ kubectl get pv -o wide
NAME      CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                     STORAGECLASS   REASON   AGE     VOLUMEMODE
pv-onas   300Gi      RWX            Retain           Bound    default/pvc-onas                           3m20s   Filesystem
```

##### Method 2. Define NAS information when creating a StorageClass manifest
Define Storage provider information and NHN Cloud NAS volume information when the StorageClass manifest is created.

* provisioner: Enter**nfs.csi.k8s.io**.
* parameters: See the table below for input.

| Item | Description | Example |  Required | Default value |
| ------- |------- | --------------------------- | ---------------------------- | ------------- |
| server | The connection information for the NAS volume, which is **the IP**. | 192.168.0.81 | O |  |
| share | The **name of the volume** in the connection information for the NAS volume. | /onas_300gb | O |  |
| mountPermissions | Specify the permissions to set on the NAS volume mount point directory. | "0700" | X | 0741 |
| uid | Enter the UID you want to set in the NAS volume mount point directory. | 1000 | X | root(0) |
| gid | Enter the GID you want to set for the NAS volume mount point directory. | 1000 | X | root(0) |

Below is an example manifest.
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

Create and check StorageClass.
```
$ kubectl apply -f storageclass.yaml
storageclass.storage.k8s.io/onas-sc created

$ kubectl get sc
NAME      PROVISIONER      RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
onas-sc   nfs.csi.k8s.io   Retain          Immediate           false                  3s
```

There is no need to create PV and create PVC manifest. PVC manifest does not require the setting of **spec.volumeName**.
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

To mount PVC to a pod, mount information must be defined at the pod manifest. Enter the PVC name to use in spec.volumes.persistenVolumeClaim.claimName and enter paths to mount in spec.containers.volumeMounts.mountPath.

Below is an example manifest that mounts the PVC you created into the pod's `/tmp/nfs`.
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

Create the pod and make sure the NAS volume is mounted.
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

#### How to create new NHN Cloud NAS volume when provisioning
You can use the automatically created NAS volume as a PV by entering the NAS information when creating the StorageClass and PVC manifest.

In the StorageClass manifest, define the storage provider information, snapshot policy, access control list (ACL), and subnet information of the NAS volume to be created.

* provisioner: Enter**nfs.csi.k8s.io**.
* parameters: Refer to the table below for input items. If multiple values are defined in the parameter value, use \*\*,** to separate the values.

| Item | Description | Example | Multi Value | Required | Default |
| ------- |------- | --------------------------- | ---------------------------- | --------- | ------------- |
| maxscheduledcount | The maximum number of snapshots that can be stored. When the maximum number of saves is reached, the first automatically created snapshot is deleted. Only numbers between 1 and 20 can be entered. | "7" | X | X |  |
| reservepercent | The maximum snapshot storage capacity. If the total amount of snapshot capacity exceeds the set size, the snapshot created first among all snapshots is deleted. Only numbers between 0 and 80 can be entered. | "80" | X | X |  |
| scheduletime | The time at which the snapshot will be created. |  | X | X |  |
| scheduletimeoffset | Offset to the snapshot creation time. It is based on UTC, and when used in KST, specify +09:00 value. | "+09:00" | X | X |  |
| scheduleweekdays | Snapshot creation cycle. Sunday through Saturday are represented by the numbers 0 through 6, respectively. | "6" | O | X |  |
| subnet | The subnet to access the storage. Only subnets in the selected VPC can be chosen. | "59526f1c-c089-4517-86fd-2d3dac369210" | X | O |  |
| acl | A list of the IPs or CIDR blocks that allow read and write permissions. | "0.0.0.0/0" | O | X | 0.0.0.0/0 |
| onDelete | Whether to delete the NAS volume when deleting PVC. | "delete" / "retain" | X | X | delete |
| mountPermissions | Specify the permissions to set for the NAS volume mount point directory. | "0700" | X | X | 0741 |
| uid | Enter the UID to set for the NAS volume mount point directory. | 1000 | X | X | root(0) |
| gid | Enter the GID to set for the NAS volume mount point directory. | 1000 | X | X | root(0) |

> [Note]
When using snapshot parameters, all relevant parameter values must be defined. Snapshot related parameters are as follows.
> + maxscheduledcount
> + reservepercent
> + scheduletime
> + scheduletimeoffset
> + scheduleweekdays

<br>

> [Caution] Limitations in multi-subnet environments
> 
> NAS volume is attached to the subnet defined in the storage class.
> To integrate pods with NAS volume, all worker node groups must be connected to the subnet.

Below is an example manifest.
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
| nfs-volume-description | A description of the NAS volume to create. | "nas sample volume" | X |
| nfs-volume-sizegb | The size of NAS volume to create. It is set in GB unit. It can be entered from a minimum of 300 to a maximum of 10,000. | "300" | O |

Below is an example manifest.
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

Create and verify StorageClass and PVC.
```
$ kubectl apply -f storage_class.yaml
storageclass.storage.k8s.io/sc-nfs created

$ kubectl get sc
NAME         PROVISIONER      RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
sc-nfs       nfs.csi.k8s.io   Delete          Immediate           false                  50s
```

You don't need to create a PV separately, just create a PVC manifest. Do not set **spec.volumeName** in the PVC manifest.
If you do not set the volume binding mode or set it to Immediate and create a PVC, the PV will be created automatically. After the NAS volume is created, it takes about 1 minute to be bound.
You can also check the created NAS volume information on the **Storage > NAS** Service page in the NHN Cloud console.

```
$ kubectl apply -f pvc.yaml
persistentvolumeclaim/pvc-nfs created

$ kubectl get pv,pvc
NAME                                                        CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM             STORAGECLASS   REASON   AGE
persistentvolume/pvc-a8ea2054-0849-4fe8-8207-ee0e43b8a103   50Gi       RWX            Delete           Bound    default/pvc-nfs   sc-nfs                  2s

NAME                            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
persistentvolumeclaim/pvc-nfs   Bound    pvc-a8ea2054-0849-4fe8-8207-ee0e43b8a103   50Gi       RWX            sc-nfs         75s
```

To mount PVC to a pod, mount information must be defined at the pod manifest. Enter the PVC name to use in **spec.volumes.persistenVolumeClaim.claimName** and enter paths to mount in **spec.containers.volumeMounts.mountPath**.

Below is an example manifest that mounts a PVC created to `/tmp/nfs` in a Pod.
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

Create the pod and make sure the NAS volume is mounted.
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
> csi-driver-nfsworks by creating a subdirectory inside the NFS storage when provisioning.
> In the process of mounting the PV to the pod, not only the subdirectory is mounted, but the entire nfs storage is mounted, so it is not possible to force the application to use the volume by the provisioned size.

<a id="encrypted-block-storage-integration"></a>
### NHN Cloud Encrypted Block Storage Integration
You can utilize encrypted block storage provided by NHN Cloud as PV. For more information about NHN Cloud encrypted block storage, see [Encrypted Block Storage](/Storage/Block%20Storage/ko/console-guide/#_2).

> [Note]
The Encrypted Block Storage service integration is available for clusters in v1.24.3 and later versions.
Newly created clusters on or after November 28, 2023 have the Encrypted Block Storage integration feature built in by default.
Clusters created before November 28, 2023 can enable encrypted block storage integration by upgrading to v1.24.3 or later, or by replacing the cinder-csi-plugin images in the csi-cinder-controllerplugin statefulset and csi-cinder-nodeplugin daemonset with newer versions.

> [Caution]
If you are using a cluster with a version prior to v1.24.3 without upgrading it and just replacing the cinder-csi-plugin container image, it can cause malfunctions.

#### Updating cinder-csi-plugin image for encrypted block storage integration
You can run the command below to see the tags of the cinder-csi-plugin images currently deployed in your cluster.

```
$ kubectl -n kube-system get statefulset csi-cinder-controllerplugin -o=jsonpath="{$.spec.template.spec.containers[?(@.name=='cinder-csi-plugin')].image}"

> registry.k8s.io/provider-os/cinder-csi-plugin:v1.27.101
```

If the tag in the cinder-csi-plugin image is v1.27.101 or later, you can integrate encrypted block storage without taking any action.
If the tag of the cinder-csi-plugin image is less than v1.27.101, you can update the image of the cinder-csi-plugin using the steps below and then integrate encrypted block storage.

| Region | Internet Connection | cinder-csi-plugin image |
| --- | --- | --- |
| Korea (Pangyo) region | O | dfe965c3-kr1-registry.container.nhncloud.com/container_service/cinder-csi-plugin:v1.27.101 |
| | X | private-dfe965c3-kr1-registry.container.nhncloud.com/container_service/cinder-csi-plugin:v1.27.101 |
| Korea (Pyeongchon) region | O | 6e7f43c6-kr2-registry.container.cloud.toast.com/container_service/cinder-csi-plugin:v1.27.101 |
| | X | private-6e7f43c6-kr2-registry.container.cloud.toast.com/container_service/cinder-csi-plugin:v1.27.101 |

##### 1. Enter a valid cinder-csi-plugin image value for container_image.
```
$ container_image={cinder-csi-plugin image}
```

##### 2. Replace the container image.
```
$ kubectl -n kube-system patch statefulset csi-cinder-controllerplugin -p "{\"spec\": {\"template\": {\"spec\": {\"containers\": [{\"name\": \"cinder-csi-plugin\", \"image\": \"${container_image}\"}]}}}}"

$ kubectl -n kube-system patch daemonset csi-cinder-nodeplugin -p "{\"spec\": {\"template\": {\"spec\": {\"containers\": [{\"name\": \"cinder-csi-plugin\", \"image\": \"${container_image}\"}]}}}}"
```

> [Note]
> The cinder-csi-plugin container image is maintained in NHN Cloud NCR. Since the cluster configured in a closed network environment is not connected to the Internet, it is necessary to configure the environment to use a private URI in order to receive images normally. For information on how to use Private URI, refer to the [NHN Cloud Container Registry (NCR)](/Container/NCR/ko/user-guide/#private-uri).


#### Static Provisioning
To create a PV, you need the ID of the encrypted block storage. On the Storage > Block Storage service page, select the block storage you want to use from the block storage list. You can find the ID under the block storage name section in the Information tab at the bottom.

When creating the PV manifest, enter the encrypted block storage information. The setting location is under **.spec.csi**.

* driver: Enter `cinder.csi.openstack.org`.
* fsType: Enter `ext3`. 
* volumeHandle: Enter the ID of the encrypted block storage you created.

Below is an example manifest.
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

#### Dynamic Provisioning
You can use automatically generated encrypted block storage as a PV by entering the information required to create encrypted block storage when creating the storage class manifest.

In the storage class manifest, enter the information required to create encrypted block storage. The settings are located under **.parameters**.

* Storage type: Enter the type of storage.
    * **Encrypted HDD**: The storage type is set to Encrypted HDD.
    * **Encrypted SSD**: The storage type is set to Encrypted SSD.
* Encryption key ID (volume_key_id): Enter the ID of the symmetric key generated by the Secure Key Manager (SKM) service.
* Encryption appkey (volume_appkey): Enter the appkey verified by the Secure Key Manager (SKM) service.

Below is an example manifest.
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
