## Container > NHN Kubernetes Service (NKS) > Release Notes


### March 28, 2023

#### Added Features

* Added the change cluster CNI feature.
    * For more details, see [Console User Guide](/Container/NKS/zh/user-guide/#cni).
* You can change the instance flavor of a node group.
* You can use a feature to view Kubernetes resources fron the console.

#### Feature Updates

* The NKS API domain has been changed.
    * Korea (Pangyo) region
        * AS-IS: https://kr1-api-kubernetes.infrastructure.cloud.toast.com
        * TO-BE: https://kr1-api-kubernetes-infrastructure.nhncloudservice.com
    * Korea (Pyeongchon) region
        * AS-IS: https://kr2-api-kubernetes.infrastructure.cloud.toast.com
        * TO-BE: https://kr2-api-kubernetes-infrastructure.nhncloudservice.com

#### Feature Updates

* Image update
    * Ubuntu Server 18.04.6 LTS - Container (2023.02.21)
    * Debian 11.6 Bullseye - Container (2023.02.21)
    * Rocky Linux 8.6 - Container (2023.02.21)

### January 31, 2023

#### Added Features

* Added a feature to change cluster OWNER.
    * For more details, see [User Guide](/Container/NKS/zh/user-guide/#_4).
* Kubernetes v1.25.4 is supported.
* Kubernetes v1.21.6 is no longer supported when creating clusters. But, clusters in use are not affected.
* Proxy protocol can be set for the listener of load balancer.

* You can create physical load balancers.

### December 27, 2022

#### Added Features

* Image added
    * Rocky Linux 8.6 - Container (2022.12)

### November 29, 2022

#### Feature Updates

* Image update
    * Changes
        * NVDIA device plugin version changed from 450.156.00 to 450.191.01.
        * Docker version changed from 19.03 to 20.10.
    * Target Image
        * Ubuntu Server 18.04.6 LTS - Container (2022.11.22)
        * CentOS 7.9 - Container (2022.11.22)

#### Added Features

* The start node/stop node features are available.
* Various types of load balancers can be created.
* You can create a cluster name and node group name with up to 32 characters each.
* Image added
    * Debian 11.5 Bullseye - Container (2022.11.22)

### September 27, 2022

#### Added Features

* Added support for Kubernetes v1.24.3.
* Kubernetes v1.20.12 is no longer supported for cluster creation. However, the clusters in use are not affected.


### July 26, 2022

#### Added Features

* A user script can be changed after creating node groups.
* Added Change User Script API.
* When upgrading the worker node group, maximum number of nodes and maximum number of unavailable nodes can be specified.

### May 24, 2022

#### Feature Updates
* Enhanced the performance and reliability of the service by improving the internal architecture


### March 29, 2022

#### Added Features

* Added support for Kubernetes v1.23.3.
* Kubernetes v1.19.13 is no longer supported for cluster creation. However, the clusters in use are not affected.
* When you set the load balancer's listener protocol to TERMINATED_HTTPS, the SSL version can be set to TLSv1.3.

#### Feature Updates

* Changed a feature name:
    * Before change: Scheduled script
    * After change: User script

### January 25, 2022

#### Feature Updates
* The name of the Kubernetes service has been changed to NHN Kubernetes Service (NKS).

#### Added Features

* Added support for the following Kubernetes versions:
    * v1.20.12
    * v1.21.6
    * v1.22.3

* Cluster creation is not supported for the following Kubernetes versions. However, the clusters in use are not affected.
    * v1.17.6
    * v1.18.19

* Added support for per-listener settings when creating a LoadBalancer type service object.

* Image added
    * CentOS 7.8 - Container (2022.01.20)
    * Ubuntu Server 18.04.6 LTS - Container (2022.01.20)
        * You can use an Ubuntu worker image for cluster creation and node group creation.

### December 28, 2021

#### Feature Updates

* Updated the NVIDIA driver used in the GPU worker nodes.
    * Previous version: 450.119.04
    * Changed version: 450.156.00
* Changed so that Prometheus compatible exporter is not automatically installed when creating an instance.
* Image update
    * CentOS 7.8 - Container (2021.12.21)

### November 23, 2021

#### Added Features
* Released the public API for the Kubernetes service.
     * For details on the public API, refer to [API Guide](/Container/NKS/zh/public-api).

### October 26, 2021

#### Added Features

* Supports Kubernetes v1.19.13.
* When creating a LoadBalancer type service object, various options for the load balancer can be set.
* The minimum value of the autoscaler's 'Scale Down Delay After Add' setting has been changed to 10 minutes.
* In new clusters, the default worker node group can be deleted if there are two or more worker node groups.

### July 27, 2021

#### Added Features

* A user script feature is available when creating node groups.
* Added container log rotation setting to the worker nodes.
    * Image update
        * CentOS 7.8 - Container (2021.07.27)
    * Refer to [Troubleshooting Guide](/Container/NKS/zh/troubleshooting-guide) for details on container log management.

### June 29, 2021

#### Added Features

* Supports Kubernetes v1.18.19.
* Can upgrade the cluster version.

### March 23, 2021

#### Added Features

* Events occurred in a user cluster can be checked in NHN CloudTrail.

#### Bug Fixes
* Fixed an issue where initialization does not work properly when a node group is created with a graphic-optimized instance type (g2).

### February 23, 2021

#### Feature Updates
* The PodSecurityPolicy plugin has been added to Kubernetes admission controller.
* Changed the distribution version of the images used at the time of generating clusters or node groups.
    * Image update
        * CentOS 7.8 - Container (2021.02.23)

### January 26, 2021
#### Bug Fixes
* Fixed an issue where autoscaler does not work in an environment with no internet gateway connection.
    * Image update
        * CentOS 7.5 - Container (2021.01.26)

### December 29, 2020
#### Feature Updates
* Kubernetes CSR (Certificate Signing Request) is now available.

### November 24, 2020
#### Added Features
* Autoscaling is now available.

#### Feature Updates
* Remaining load balancers and floating IPs are also deleted when deleting clusters.

### October 27, 2020
#### More Feature
* Kubernetes clusters now support GPU-based node groups.
    * Image update
        * CentOS 7.5 - Container (2020.10.27)

### 2020. 09. 22.
#### Feature Updates
* Nodes can be added to or deleted from a running node group.

#### Release of New Service
* Kubernetes service is now available in the Korea (Pyeongchon) region.

### August 25, 2020
#### Feature Updates
* A random zone can be selected when creating a Kubernetes cluster on console.

### June 23, 2020
#### Release of New Service 
* Kubernetes clusters can be created and managed on console. 
