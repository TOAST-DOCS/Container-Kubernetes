## Container > NHN Kubernetes Service (NKS) > Release Notes

### 2025. 07. 15.

#### Image Update
* The following images are no longer supported when creating clusters and node groups.
    * Target image
        * Ubuntu Server 20.04.3 LTS - Container

### May 27, 2025

#### Added Features
* Support for Kubernetes v1.32.3.
* Added the feature to save control plane Kubernetes component logs.
* Added metric-based autoscaler.
* The feature to automatically assign floating IPs per node group is available.
* The feature to set Kubernetes labels by node group is available.
* Addon management feature is available.
    * For more information, see [User Guide](/Container/NKS/ko/user-guide/#addon_mgmt).

#### Feature Updates
* The feature to change cluster CNI is no longer supported.
* Physical load balancers are no longer supported.
* Removed the system pod upgrade step during Blue/Green Upgrade.
    * System pods like Calico, CoreDNS, and others can be updated through the add-on management.

### March 4, 2025

#### Added Features
* Support for Kubernetes v1.31.4.
* The OIDC(openID connect) setting feature is available.
* You can change the cluster key pair.
    * Clusters that enabled a cluster key pair operate with the service user's permissions
    * Clusters that operate under the service user's permissions do not need to change/manage owners.

#### Feature Updates
* The feature to change cluster owners is not supported.

### November 26, 2024

#### Added Features
* Added the feature to set Kubernetes component.

#### Feature Updates
* Changed to automatically set the maximum number of Pods that can be created per node based on the pod subnet size.

#### Image Update
Chagned the version of the GPU driver installed in images.

| Image | AS-IS | TO-BE |
| --- | --- | --- |
| Rocky Linux 8.10 (2024.11.19)  | 535.183.06 | 535.216.01 |
| Ubuntu Server 20.04.6 LTS (2024.11.19)  | 535.183.06 | 535.216.01 |
| Ubuntu Server 22.04.6 LTS (2024.11.19)  | 535.183.06 | 535.216.01 |

Changed the kernel version installed in images.

| Image | AS-IS | TO-BE |
| --- | --- | --- |
| Rocky Linux 8.10 (2024.11.19)  | 4.18.0-553.8.1 | 4.18.0-553.22.1 |
| Ubuntu Server 20.04.6 LTS (2024.11.19)  | 5.4.0-190 | 5.4.0-198 |
| Ubuntu Server 22.04.6 LTS (2024.11.19)  | 5.15.0-117 | 5.15.0-124 |

Changed the minimum disk size required to create worker nodes in images.

| Image | AS-IS | TO-BE |
| --- | --- | --- |
| Rocky Linux 8.10 (2024.11.19) | 20GB | 30GB |
| Ubuntu Server 20.04.6 LTS (2024.11.19) | 20GB | 30GB |
| Ubuntu Server 22.04.6 LTS (2024.11.19) | 20GB | 30GB |

#### Deprecated Image Support
* You can't create new clusters and node groups using CentOS images.

### October 29, 2024

#### Feature Updates

* CNI Updates
    * Changed the Calico CNI version for Kubernetes v1.27.3 and later clusters from v3.28.0 to v3.28.2.

### August 27, 2024

#### Added Features
* Added the feature to specify additional security groups to a node group.
* Added the feature to specify additional block storage for a group of nodes.
* Support for Kubernetes v1.30.3.
* You can set whether the load balancer applies static routes.
* You can enable the NKS registry.

#### Deprecated Image Support
* You can't create new clusters and node groups using Debian images.

### July 23, 2024

#### Added Features

* You can apply L7 rules and conditions by setting the load balancer details options.
* You can select Calico-VXLAN and Calico-eBPF CNIs when creating a cluster.

### May 28, 2024

#### Added Features

* Support for Kubernetes v1.29.3.
* You can use a certificate from Certificate Manager when you set the load balancer's listener protocol to TERMINATED_HTTPS.
* Added the feature to renew certificates for a cluster.
* Added the feature to change the service gateway set when creating a cluster.
* The Resource Watcher service allows you to receive notifications about events that occur in your cluster.
    * For more information, see [Resource Watcher](/Governance%20&%20Audit/Resource%20Watcher/en/overview).

### March 26, 2024

#### Feature Updates
* Changed the valid range for the service gateway you enter when creating a cluster.
    * Previous: A service gateway created on the same subnet as the cluster's subnet.
    * Current: A service gateway created on a subnet that is included in a VPC in the cluster.
    
### February 27, 2024

#### Added Features

* You can apply enforced security rules to clusters.
* Added IP access control to cluster API endpoints.
* Added support for Kubernetes v1.28.3.
* You can view the history of tasks on the clusters and node groups lookup screen.

### November 28, 2023

#### Added Features
* Added the feature to set kubelet custom arguments.
* Added the feature to set member subnets as setting detailed options for load balancer
* Added the feature to set the keep-alive timeout value as setting detailed options for load balancer
* Added the feature to create PVs using encrypted block storage.

#### Image update
* Added new images that can be used when creating clusters and node groups.
    * Target image
        * Ubuntu Server 22.04.3 LTS - Container (2023.11.21)
* Changed existing images to support GPU worker node functionality.
    * Target image
        * Debian 11.8 Bullseye - Container (2023.11.21)
* The version of the GPU driver installed in the image has changed.
    * Changes
        * Changed the nvidia-device-plugin version from 470.199.02 to 535.104.12.
        * Changed the dcgm version from 11.4 to 12.2.
        * Changed the nvidia-mig-manager version from 0.5.3 to 0.5.5.
    * Target image
        * CentOS 7.9 - Container (2023.11.21)
        * Rocky Linux 8.8 - Container (2023.11.21)
        * Ubuntu Server 20.04.6 LTS - Container (2023.11.21)

### August 29, 2023

#### Added Features

* Added support for Kubernetes v1.27.3
* You can set the properties of Kubernetes API endpoint when creating a cluster.
* You can use encrypted block storage on worker nodes.
* Provided more detailed status information on the cluster and node group query screen.
* Added a feature to create new NAS storage upon provisioning.

#### Feature Updates
* Changed the distribution version of the images used at the time of generating clusters or node groups.
    * AS-IS
        * Rocky Linux 8.7 - Container (2023.07.25)
    * TO-BE
        * Rocky Linux 8.8 - Container (2023.08.22)

#### Image update 
* Changes
    * Changed the nvidia-device-plugin version from 470.182.03 to 470.199.02.
    * Changed the dcgm version from 3.1.7 to 3.1.8.
    * Changed the nvidia-mig-manager version from 0.5.2 to 0.5.3.
* Target image
    * CentOS 7.9 - Container (2023.08.22)
    * Rocky Linux 8.8 - Container (2023.08.22)
    * Ubuntu Server 20.04.6 LTS - Container (2023.08.22)

### July 19, 2023

#### Image Update
* Fixed an issue where, when creating node groups, the iptables kernel module is not initialized normally on some images.
    * AS-IS: Rocky Linux 8.7 - Container (2023.05.25)
    * TO-BE: Rocky Linux 8.7 - Container (2023.07.25)
* Fixed an issue where, when creating GPU node groups, the nvidia-container-runtime module is not deployed on some images.
    * AS-IS: CentOS 7.9 - Container (2023.05.25)
    * TO-BE: CentOS 7.9 - Container (2023.07.25)


### May 30, 2023

#### Added Features

* Added support for Kubernetes v1.26.3.
* Added a feature to use common images as worker images.
    * For more information, see [User Guide](/Container/NKS/ko/user-guide/#_24).
* Added a feature to change the size of cluster service network, pod network, and pod subnet.

#### Feature Updates

* Changed the distribution version of the image used when creating clusters and node groups.
  * AS-IS
        * Ubuntu Server 18.04.6 LTS - Container (2023.03.21)
        * Rocky Linux 8.6 - Container (2023.03.21)
    * TO-BE
        * Ubuntu Server 20.04.6 LTS - Container (2023.05.25)
        * Rocky Linux 8.7 - Container (2023.05.25)

* Image update
    * Changes
        * nvidia-device-plugin version changed from 450.216.04 to 470.182.
        * cuda version changed from 11.0.3 to 11.4.
        * dcgm version changed from 3.0.0 to 3.1.7.
        * Docker version changed from 20.10.23 to 20.10.24.
    * Target Images
        * CentOS 7.9 - Container (2023.05.25)
        * Rocky Linux 8.7 - Container (2023.05.25)
        * Ubuntu Server 20.04.6 LTS - Container (2023.05.25)

### March 28, 2023

#### Added Features

* Added the change cluster CNI feature.
    * For more details, see [Console User Guide](/Container/NKS/en/user-guide/#cni).
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
    * For more details, see [User Guide](/Container/NKS/en/user-guide/#_4).
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
