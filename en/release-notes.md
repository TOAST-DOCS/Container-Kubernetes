<!-- pre-align:aligned sig=a9faa149ecf3 -->

<a id="container-nhn-kubernetes-service-nks-release-notes"></a>
## Container > NHN Kubernetes Service (NKS) > Release Notes { #container-nhn-kubernetes-service-nks-release-notes }

<a id="may-27-2026"></a>

### August 25, 2026 { #aug-25-2026 }

#### Added Features
* Added support for Kubernetes v1.35.5.

<a id="aug-25-2026-platform-version-updates"></a>

#### Platform Version Updates
* Added 1.202608.0
    * Kubernetes compatible versions: v1.31–v1.35

<a id="aug-25-2026-add-on-updates"></a>

#### Addon Updates
* Addons Added
    * calico v3.28.2-nks4
    * calico v3.30.2-nks4
    * calico v3.31.4-nks2
    * cilium v1.18.0-nks2
    * cinder_csi_plugin v1.27.101-nks3
    * cinder_csi_plugin v1.27.102-nks4
    * coredns 1.8.4-nks3
    * metrics_server v0.4.4-nks3
    * nfs_csi_plugin v1.0.1-nks3
    * nfs_csi_plugin v1.0.2-nks2
    * nfs_csi_plugin v1.0.3-nks1
    * snapshot_controller v4.1.1-nks3

<a id="may-27-2026"></a>

### May 27, 2026 { #may-27-2026 }

<a id="may-27-2026-added-features"></a>

#### Added Features
* Added support for Kubernetes v1.34.3.
    * In k8s v1.34 and later, there are constraints depending on the CGroup version of the worker node image. For more information, see [Constraints on Kubernetes Version and CGroup Version](/Container/NKS/en/version-guide/#constraints-on-cgroup).
    * In k8s v1.34 and later, the custom containerd registry settings feature does not work. For more information, see [Custom containerd Registry Settings Feature](/Container/NKS/en/version-guide/#containerd-registry-config).
* Added support for Cilium CNI.

<a id="may-27-2026-platform-version-updates"></a>

#### Platform Version Updates
* Added 1.202605.0
    * Kubernetes compatible versions: v1.30–v1.34
    * Added Features
        * Support for worker node CGroup v1 → v2 migration
        * Support for konnectivity for communication between kube-apiserver and pods
        * Enabled `ImageVolume` feature gate for k8s v1.34 clusters

<a id="may-27-2026-add-on-updates"></a>

#### Add-on Updates
* Add-ons added
    * nfs_csi_plugin v1.0.2-nks1
    * cilium v1.18.0-nks1
    * calico v3.31.4-nks1
    * calico v3.28.2-nks3
    * calico v3.30.2-nks3

<a id="march-17-2026"></a>
### March 17, 2026 { #march-17-2026 }

<a id="march-17-2026-platform-version-updates"></a>

#### Platform Version Updates
* Added 1.202602.1
    * Improved
        * Enhanced the stability of LBs created by Service objects

<a id="march-10-2026"></a>
### March 10, 2026 { #march-10-2026 }

<a id="march-10-2026-platform-version-updates"></a>

#### Platform Version Updates
* Added 1.202602.0
    * Kubernetes compatible versions: v1.29–v1.33
    * Added Features
        * Kubernetes taint configuration
        * Support for Kubernetes component configuration for max-pods
        * Support for etcd data encryption with Secure Key Manager
        * Support for CGroup v2 OS image
    * Feature Updates
        * Support for improved traffic handling during node and node group deletion

<a id="march-10-2026-add-on-updates"></a>

#### Addon Updates
* Added addons
    * calico v3.30.2-nks2
    * coredns 1.8.4-nks2
    * metrics_server v0.4.4-nks2
    * cinder_csi_plugin v1.27.102-nks3
    * snapshot_controller v4.1.1-nks2
    * nfs_csi_plugin v1.0.1-nks2

<a id="march-10-2026-added-features"></a>

#### Added Features
* Added support for OS images with CGroup set to v2.
    * OS images released after March 2026 have CGroup set to v2.
* You can configure kubelet's --max-pods using the Kubernetes component configuration feature.
* Added the Kubernetes taint configuration feature.
* You can encrypt and decrypt confidential data in a cluster using the Secure Key Manager service.

<a id="december-23-2025"></a>
### December 23, 2025 { #december-23-2025 }

<a id="december-23-2025-platform-version-updates"></a>

#### Platform Version Updates
* Added 1.202511.1
    * Fixed an issue with the health check port configuration.

<a id="december-23-2025-add-on-updates"></a>
#### Add-on Updates
* Added Cinder CSI Plugin v1.27.101-nks2, v1.27.102-nks2.
    * Updated internal container versions as follows:
        * csi-attacher: v3.0.2 → v3.3.0
        * csi-provisioner: v2.0.4 → v2.2.2
        * csi-snapshotter: v3.0.2 → v3.0.3
        * csi-resizer: v1.0.1 → v1.3.0
        * csi-node-driver-registrar: v2.0.1 → v2.3.0

<a id="november-25-2025"></a>
### November 25, 2025 { #november-25-2025 }

<a id="november-25-2025-changed-service-support-policy"></a>
#### Changed Service Support Policy
* Changed the support policy for the Kubernetes version of NKS.
    * For more information, refer to the [Version Guide](/Container/NKS/en/version-guide).

<a id="november-25-2025-updated-add-on"></a>

#### Addon Updates
* Added addons
    * Calico CNI v3.30.2-nks1
    * Cinder CSI Plugin v1.27.101-nks1, v1.27.102-nks1
    * Metrics Server v0.4.4-nks1
    * Snapshot Controller v4.1.1-nks1
    * NFS CSI Plugin v1.0.1-nks1

<a id="november-25-2025-added-features"></a>

#### Added Features
* Added support for Kubernetes v1.33.4.
* Added the feature to view and upgrade the platform version of the control plane and worker node groups.
* Added the feature to set the health check port as setting detailed options for load balancer.
* Added the feature to set the health check host header as setting detailed options for load balancer.

<a id="july-15-2025"></a>
### July 15, 2025 { #july-15-2025 }

<a id="july-15-2025-image-update"></a>
#### Image Update
* The following images are no longer supported when creating clusters and node groups:
    * Target image
        * Ubuntu Server 20.04.3 LTS - Container

<a id="may-27-2025"></a>
### May 27, 2025 { #may-27-2025 }

<a id="may-27-2025-added-features"></a>

#### Added Features
* Added support for Kubernetes v1.32.3.
* Added the feature to store logs of Kubernetes control plane components.
* Added metric-based autoscaler.
* The feature to automatically assign floating IPs per node group is available.
* The feature to set Kubernetes labels by node group is available.
* Addon management feature is available.
    * For more information, see the [User Guide](/Container/NKS/en/user-guide/#addon-mgmt).

<a id="may-27-2025-feature-updates"></a>
#### Feature Updates
* The feature to change cluster CNI is no longer supported.
* Physical load balancers are no longer supported.
* Removed the system pod upgrade step during Blue/Green Upgrade.
    * System pods like Calico, CoreDNS, and others can be updated through the add-on management.

<a id="march-4-2025"></a>
### March 4, 2025 { #march-4-2025 }

<a id="march-4-2025-added-features"></a>

#### Added Features
* Added support for Kubernetes v1.31.4.
* The OIDC(openID connect) setting feature is available.
* You can change the cluster key pair.
    * Clusters that enabled a cluster key pair operate with the service user's permissions.
    * Clusters that operate under the service user's permissions do not need to change/manage owners.

<a id="march-4-2025-feature-updates"></a>
#### Feature Updates
* The feature to change cluster owners is not supported.

<a id="november-26-2024"></a>
### November 26, 2024 { #november-26-2024 }

<a id="november-26-2024-added-features"></a>

#### Added Features
* Added the feature to set Kubernetes component.

<a id="november-26-2024-feature-updates"></a>
#### Feature Updates
* Changed to automatically set the maximum number of Pods that can be created per node based on the pod subnet size.

<a id="november-26-2024-image-update"></a>
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

<a id="november-26-2024-deprecated-image-support"></a>
#### Deprecated Image Support
* You can't create new clusters and node groups using CentOS images.

<a id="october-29-2024"></a>
### October 29, 2024 { #october-29-2024 }

<a id="october-29-2024-feature-updates"></a>
#### Feature Updates

* CNI Updates
    * Changed the Calico CNI version for Kubernetes v1.27.3 and later clusters from v3.28.0 to v3.28.2.

<a id="august-27-2024"></a>
### August 27, 2024 { #august-27-2024 }

<a id="august-27-2024-added-features"></a>

#### Added Features
* Added the feature to specify additional security groups to a node group.
* Added the feature to specify additional block storage for a group of nodes.
* Added support for Kubernetes v1.30.3.
* You can set whether the load balancer applies static routes.
* You can enable the NKS registry.

<a id="august-27-2024-deprecated-image-support"></a>
#### Deprecated Image Support
* You can't create new clusters and node groups using Debian images.

<a id="july-23-2024"></a>
### July 23, 2024 { #july-23-2024 }

<a id="july-23-2024-added-features"></a>

#### Added Features

* You can apply L7 rules and conditions by setting the load balancer details options.
* You can select Calico-VXLAN and Calico-eBPF CNIs when creating a cluster.

<a id="may-28-2024"></a>
### May 28, 2024 { #may-28-2024 }

<a id="may-28-2024-added-features"></a>

#### Added Features

* Support for Kubernetes v1.29.3.
* You can use a certificate from Certificate Manager when you set the load balancer's listener protocol to TERMINATED_HTTPS.
* Added the feature to renew certificates for a cluster.
* Added the feature to change the service gateway set when creating a cluster.
* The Resource Watcher service allows you to receive notifications about events that occur in your cluster.
    * For more information, see [Resource Watcher](/Governance%20&%20Audit/Resource%20Watcher/en/overview).

<a id="march-26-2024"></a>
### March 26, 2024 { #march-26-2024 }

<a id="march-26-2024-feature-updates"></a>
#### Feature Updates
* Changed the valid range for the service gateway you enter when creating a cluster.
    * Previous: A service gateway created on the same subnet as the cluster's subnet.
    * Current: A service gateway created on a subnet that is included in a VPC in the cluster.
    
<a id="february-27-2024"></a>
### February 27, 2024 { #february-27-2024 }

<a id="february-27-2024-added-features"></a>

#### Added Features

* You can apply enforced security rules to clusters.
* Added IP access control to cluster API endpoints.
* Added support for Kubernetes v1.28.3.
* You can view the history of tasks on the clusters and node groups lookup screen.

<a id="november-28-2023"></a>
### November 28, 2023 { #november-28-2023 }

<a id="november-28-2023-added-features"></a>

#### Added Features
* Added the feature to set kubelet custom arguments.
* Added the feature to set member subnets as setting detailed options for load balancer
* Added the feature to set the keep-alive timeout value as setting detailed options for load balancer
* Added the feature to create PVs using encrypted block storage.

<a id="november-28-2023-image-update"></a>
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

<a id="august-29-2023"></a>
### August 29, 2023 { #august-29-2023 }

<a id="august-29-2023-added-features"></a>

#### Added Features

* Added support for Kubernetes v1.27.3
* You can set the properties of Kubernetes API endpoint when creating a cluster.
* You can use encrypted block storage on worker nodes.
* Provided more detailed status information on the cluster and node group query screen.
* Added a feature to create new NAS Volume upon provisioning.

<a id="august-29-2023-feature-updates"></a>
#### Feature Updates
* Changed the distribution version of the images used at the time of generating clusters or node groups.
    * AS-IS
        * Rocky Linux 8.7 - Container (2023.07.25)
    * TO-BE
        * Rocky Linux 8.8 - Container (2023.08.22)

<a id="august-29-2023-image-update"></a>
#### Image update 
* Changes
    * Changed the nvidia-device-plugin version from 470.182.03 to 470.199.02.
    * Changed the dcgm version from 3.1.7 to 3.1.8.
    * Changed the nvidia-mig-manager version from 0.5.2 to 0.5.3.
* Target image
    * CentOS 7.9 - Container (2023.08.22)
    * Rocky Linux 8.8 - Container (2023.08.22)
    * Ubuntu Server 20.04.6 LTS - Container (2023.08.22)

<a id="july-19-2023"></a>
### July 19, 2023 { #july-19-2023 }

<a id="july-19-2023-image-update"></a>
#### Image Update
* Fixed an issue where, when creating node groups, the iptables kernel module is not initialized normally on some images.
    * AS-IS: Rocky Linux 8.7 - Container (2023.05.25)
    * TO-BE: Rocky Linux 8.7 - Container (2023.07.25)
* Fixed an issue where, when creating GPU node groups, the nvidia-container-runtime module is not deployed on some images.
    * AS-IS: CentOS 7.9 - Container (2023.05.25)
    * TO-BE: CentOS 7.9 - Container (2023.07.25)


<a id="may-30-2023"></a>
### May 30, 2023 { #may-30-2023 }

<a id="may-30-2023-added-features"></a>

#### Added Features

* Added support for Kubernetes v1.26.3.
* Added a feature to use common images as worker images.
    * For more information, see [User Guide](/Container/NKS/en/user-guide/#_24).
* Added a feature to change the size of cluster service network, pod network, and pod subnet.

<a id="may-30-2023-feature-updates"></a>
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

<a id="march-28-2023"></a>
### March 28, 2023 { #march-28-2023 }

<a id="march-28-2023-added-features"></a>

#### Added Features

* Added the change cluster CNI feature.
    * For more details, see [Console User Guide](/Container/NKS/en/user-guide/#cni).
* You can change the instance flavor of a node group.
* You can use a feature to view Kubernetes resources fron the console.

<a id="march-28-2023-feature-updates"></a>
#### Feature Updates

* The NKS API domain has been changed.
    * Korea (Pangyo) region
        * AS-IS: https://kr1-api-kubernetes.infrastructure.cloud.toast.com
        * TO-BE: https://kr1-api-kubernetes-infrastructure.nhncloudservice.com
    * Korea (Pyeongchon) region
        * AS-IS: https://kr2-api-kubernetes.infrastructure.cloud.toast.com
        * TO-BE: https://kr2-api-kubernetes-infrastructure.nhncloudservice.com

<a id="march-28-2023-march-28-2023-feature-updates"></a>
#### Feature Updates

* Image update
    * Ubuntu Server 18.04.6 LTS - Container (2023.02.21)
    * Debian 11.6 Bullseye - Container (2023.02.21)
    * Rocky Linux 8.6 - Container (2023.02.21)

<a id="january-31-2023"></a>
### January 31, 2023 { #january-31-2023 }

<a id="january-31-2023-added-features"></a>

#### Added Features

* Added a feature to change cluster OWNER.
    * For more details, see [User Guide](/Container/NKS/en/user-guide/#_4).
* Kubernetes v1.25.4 is supported.
* Kubernetes v1.21.6 is no longer supported when creating clusters. But, clusters in use are not affected.
* Proxy protocol can be set for the listener of load balancer.

<a id="december-27-2022"></a>
### December 27, 2022 { #december-27-2022 }

<a id="december-27-2022-added-features"></a>

#### Added Features

* Image added
    * Rocky Linux 8.6 - Container (2022.12)

<a id="november-29-2022"></a>
### November 29, 2022 { #november-29-2022 }

<a id="november-29-2022-feature-updates"></a>
#### Feature Updates

* Image update
    * Changes
        * NVDIA device plugin version changed from 450.156.00 to 450.191.01.
        * Docker version changed from 19.03 to 20.10.
    * Target Image
        * Ubuntu Server 18.04.6 LTS - Container (2022.11.22)
        * CentOS 7.9 - Container (2022.11.22)

<a id="november-29-2022-added-features"></a>

#### Added Features

* The start node/stop node features are available.
* Various types of load balancers can be created.
* You can create a cluster name and node group name with up to 32 characters each.
* Image added
    * Debian 11.5 Bullseye - Container (2022.11.22)

<a id="september-27-2022"></a>
### September 27, 2022 { #september-27-2022 }

<a id="september-27-2022-added-features"></a>

#### Added Features

* Added support for Kubernetes v1.24.3.
* Kubernetes v1.20.12 is no longer supported for cluster creation. However, the clusters in use are not affected.


<a id="july-26-2022"></a>
### July 26, 2022 { #july-26-2022 }

<a id="july-26-2022-added-features"></a>

#### Added Features

* A user script can be changed after creating node groups.
* Added Change User Script API.
* When upgrading the worker node group, maximum number of nodes and maximum number of unavailable nodes can be specified.

<a id="may-24-2022"></a>
### May 24, 2022 { #may-24-2022 }

<a id="may-24-2022-feature-updates"></a>
#### Feature Updates
* Enhanced the performance and reliability of the service by improving the internal architecture


<a id="march-29-2022"></a>
### March 29, 2022 { #march-29-2022 }

<a id="march-29-2022-added-features"></a>

#### Added Features

* Added support for Kubernetes v1.23.3.
* Kubernetes v1.19.13 is no longer supported for cluster creation. However, the clusters in use are not affected.
* When you set the load balancer's listener protocol to TERMINATED_HTTPS, the SSL version can be set to TLSv1.3.

<a id="march-29-2022-feature-updates"></a>
#### Feature Updates

* Changed a feature name:
    * Before change: Scheduled script
    * After change: User script

<a id="january-25-2022"></a>
### January 25, 2022 { #january-25-2022 }

<a id="january-25-2022-feature-updates"></a>
#### Feature Updates
* The name of the Kubernetes service has been changed to NHN Kubernetes Service (NKS).

<a id="january-25-2022-added-features"></a>

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

<a id="december-28-2021"></a>
### December 28, 2021 { #december-28-2021 }

<a id="december-28-2021-feature-updates"></a>
#### Feature Updates

* Updated the NVIDIA driver used in the GPU worker nodes.
    * Previous version: 450.119.04
    * Changed version: 450.156.00
* Changed so that Prometheus compatible exporter is not automatically installed when creating an instance.
* Image update
    * CentOS 7.8 - Container (2021.12.21)

<a id="november-23-2021"></a>
### November 23, 2021 { #november-23-2021 }

<a id="november-23-2021-added-features"></a>

#### Added Features
* The Public API for the Kubernetes service has been released.
    * For more information about the Public API, see the [API Guide](/Container/NKS/en/public-api).

<a id="october-26-2021"></a>
### October 26, 2021 { #october-26-2021 }

<a id="october-26-2021-added-features"></a>

#### Added Features

* Supports Kubernetes v1.19.13.
* When creating a LoadBalancer type service object, various options for the load balancer can be set.
* The minimum value of the autoscaler's 'Scale Down Delay After Add' setting has been changed to 10 minutes.
* In new clusters, the default worker node group can be deleted if there are two or more worker node groups.

<a id="july-27-2021"></a>
### July 27, 2021 { #july-27-2021 }

<a id="july-27-2021-added-features"></a>

#### Added Features

* A user script feature is available when creating node groups.
* Added container log rotation setting to the worker nodes.
    * Image update
        * CentOS 7.8 - Container (2021.07.27)
    * Refer to [Troubleshooting Guide](/Container/NKS/en/troubleshooting-guide) for details on container log management.

<a id="june-29-2021"></a>
### June 29, 2021 { #june-29-2021 }

<a id="june-29-2021-added-features"></a>

#### Added Features

* Supports Kubernetes v1.18.19.
* Can upgrade the cluster version.

<a id="march-23-2021"></a>
### March 23, 2021 { #march-23-2021 }

<a id="march-23-2021-added-features"></a>

#### Added Features

* Events occurred in a user cluster can be checked in NHN CloudTrail.

<a id="march-23-2021-bug-fixes"></a>
#### Bug Fixes
* Fixed an issue where initialization does not work properly when a node group is created with a graphic-optimized instance type (g2).

<a id="february-23-2021"></a>
### February 23, 2021 { #february-23-2021 }

<a id="february-23-2021-feature-updates"></a>
#### Feature Updates
* The PodSecurityPolicy plugin has been added to Kubernetes admission controller.
* Changed the distribution version of the images used at the time of generating clusters or node groups.
    * Image update
        * CentOS 7.8 - Container (2021.02.23)

<a id="january-26-2021"></a>
### January 26, 2021 { #january-26-2021 }
<a id="january-26-2021-bug-fixes"></a>
#### Bug Fixes
* Fixed an issue where autoscaler does not work in an environment with no internet gateway connection.
    * Image update
        * CentOS 7.5 - Container (2021.01.26)

<a id="december-29-2020"></a>
### December 29, 2020 { #december-29-2020 }
<a id="december-29-2020-feature-updates"></a>
#### Feature Updates
* Kubernetes CSR (Certificate Signing Request) is now available.

<a id="november-24-2020"></a>
### November 24, 2020 { #november-24-2020 }
<a id="november-24-2020-added-features"></a>

#### Added Features
* Autoscaler feature is available.

<a id="november-24-2020-feature-updates"></a>
#### Feature Updates
* Remaining load balancers and floating IPs are also deleted when deleting clusters.

<a id="october-27-2020"></a>
### October 27, 2020 { #october-27-2020 }
<a id="october-27-2020-more-feature"></a>

#### Added Features
* You can use GPU-based node groups in a Kubernetes cluster.
    * Image updates
        * CentOS 7.5 - Container (2020.10.27)

<a id="09-22"></a>
### 2020. 09. 22. { #09-22 }
<a id="09-22-feature-updates"></a>
#### Feature Updates
* Nodes can be added to or deleted from a running node group.

<a id="09-22-release-of-new-service"></a>
#### Release of New Service
* Kubernetes service is now available in the Korea (Pyeongchon) region.

<a id="august-25-2020"></a>
### August 25, 2020 { #august-25-2020 }
<a id="august-25-2020-feature-updates"></a>
#### Feature Updates
* A random zone can be selected when creating a Kubernetes cluster on console.

<a id="june-23-2020"></a>
### June 23, 2020 { #june-23-2020 }
<a id="june-23-2020-release-of-new-service"></a>
#### Release of New Service 
* Kubernetes clusters can be created and managed on console. 
