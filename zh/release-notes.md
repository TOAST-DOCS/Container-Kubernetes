## Container > Kubernetes > Release Notes

### November 23, 2021

#### Added Features
* Released the public API for the Kubernetes service.
     * For details on the public API, refer to [API Guide](/Container/Kubernetes/zh/public-api).

### October 26, 2021

#### Added Features

* Supports Kubernetes v1.19.13.
* When creating a LoadBalancer type service object, various options for the load balancer can be set.
* The minimum value of the autoscaler's 'Scale-down Delay After Scale-up' setting has been changed to 10 minutes.
* In new clusters, the default worker node group can be deleted if there are two or more worker node groups.

### July 27, 2021

#### Added Features

* Scheduled script feature is available when creating node groups.
* Added container log rotation setting to the worker nodes.
    * Image update
        * CentOS 7.8 - Container (2021.07.27)
    * Refer to [Troubleshooting Guide](/Container/Kubernetes/zh/troubleshooting-guide) for details on container log management.

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
