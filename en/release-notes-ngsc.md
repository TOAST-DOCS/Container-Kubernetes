## Container > NHN Kubernetes Service (NKS) > Release Notes

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
     * For details on the public API, refer to [API Guide](/Container/NKS/en/public-api).

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
    * Refer to [Troubleshooting Guide](/Container/NKS/en/troubleshooting-guide) for details on container log management.

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
