<!-- pre-align:aligned sig=c2b4f6b0a381 -->

<a id="container-nhn-kubernetes-servicenks-version-guide"></a>
## Container > NHN Kubernetes Service(NKS) > Version Guide { #container-nhn-kubernetes-servicenks-version-guide }

<a id="cluster-version-management"></a>
## Manage Cluster Version { #cluster-version-management }

An NKS cluster manages Kubernetes and platform versions for each cluster control plane and worker node group. The differences between the two versions are as follows:

<a id="kubernetes-version"></a>
### Kubernetes Version { #kubernetes-version }
- A version defined by upstream Kubernetes.
- It determines the version of the main Kubernetes components that make up the NKS cluster.
- Main components: `kube-apiserver`, `kube-controller-manager`, `kube-scheduler`, `kubelet`, `kube-proxy`

<a id="cluster-version-management-platform-version"></a>
### Platform Version { #cluster-version-management-platform-version }
- A version defined at the NKS service level.
- Multiple components that make up a cluster are defined and managed as a single version.
- Key components: Control plane components such as `containerd` and `etcd`, key worker node components, various system components, and system management tools.

<a id="upgrade-targets-by-version-status"></a>
### Upgrade Targets by Version Status { #upgrade-targets-by-version-status }

| Kubernetes Version Up-to-Date | Platform Version Up-to-Date | Upgrade Target |
|----------------------------|----------------------|----------------|
| X | X | Kubernetes Version and Platform Version |
| X | O | Kubernetes Version |
| O | X | Platform Version |
| O | O | None |

> Control plane version information can be found on the **View Cluster** screen, and worker node group version information can be found on each **View Worker Node Group** screen.

<a id="support-policy"></a>
## Support Policy for Kubernetes Version { #support-policy }

<a id="available-cluster-versions"></a>
### Available Cluster Versions { #available-cluster-versions }
- You can create a new cluster using the three most recent Kubernetes versions released by NKS.
- When a new version is released, the oldest version from the existing list of available versions will be automatically removed.
- Kubernetes patch versions may be updated according to internal policies.

<a id="service-support-policy"></a>
### Service Support Policy { #service-support-policy }
To ensure stable service operation, we apply a service support policy for each Kubernetes version.

- NKS service support is determined based on the **Kubernetes version**.
- The service support policy applies based on the release date of the new Kubernetes version (`x.y`) of NKS.
- NKS continuously provides the latest Kubernetes version, and existing clusters can be maintained up-to-date through upgrades.

| Service Support Policy | Application Criteria | Guaranteed NKS Functionality | Cluster Upgrade |
|-----------------|------------|------------------|------------------|
| **Service Support** | Less than about 14 months after the Kubernetes version release | Guaranteed | Possible |
| **Service Not Supported** | More than about 14 months after the Kubernetes version release | Not Guaranteed | However, upgrade support is provided for 10 months after the end of service support |

<a id="service-support-policy-example-lifecycle-of-version-v133-released-in-november-2025"></a>
#### Example: Lifecycle of version v1.33 released in November 2025

| Category | Period | Key Features |
|------|------|-----------|
| **Service Support** | Nov, 2025 - January 31, 2027 (about 14 months) | - All functions operate normally and technical support is provided |
| **Service Not Supported (Upgrade Support)** | February 1, 2027 - November 30, 2027 (about 10 months) | - No technical support except for upgrades |
| **Service Not Supported (Support End - EOS)** | December 1, 2027 and later | - No technical support, including upgrades <br>- Migration required after creating a new cluster |

> ### Operational Tips
> - Each version is maintained for up to approximately 24 months after release.
> - Once in the service not supported phase, it is recommended to complete the upgrade within 10 months. > - In the End of Support (EOS) state, a new cluster creation and workload transfer (Self-Migration) are required.


<a id="k8s-version-support-time-table"></a>
## Schedule for Kubernetes Version-Specific Support { #k8s-version-support-time-table }

> [Notice] The Kubernetes version support policy will change starting in November 2025.
> - The existing schedule and policy will apply up to v1.32.
> - The new policy will apply starting from v1.33.
> - The dates in the table are based on UTC+00:00.

<a id="new-policy-applied-version-v133-or-later"></a>
### New Policy Applied Version (v1.33 or later) { #new-policy-applied-version-v133-or-later }

| Version | Release | End of Service Support (Upgrade Support) | End of Service Support (EOS) |
|------|--------|---------------------------------|---------------------|
| v1.33 | November, 2025 | January 31, 2027 | November 30, 2027 |
| v1.34 | May, 2026 | July 31, 2027 | May 31, 2028 |
| v1.35 | August, 2026 | October 30, 2027 | August 31, 2028 |

<a id="old-policy-applied-version-v132-or-earlier"></a>
### Old Policy Applied Version (v1.32 or earlier) { #old-policy-applied-version-v132-or-earlier }

| Version | Release | End of Service Support (Upgrade Support) | End of Service Support (EOS) |
|------|--------|---------------------------------|---------------------|
| v1.28.3 | 2024.02 | 2025.11.30 | 2025.11.30 |
| v1.29.3 | 2024.05 | 2026.02.28 | February 28, 2026 |
| v1.30.3 | August 2024 | May 31, 2026 | May 31, 2026 |
| v1.31.4 | February 2025 | August 31, 2026 | August 31, 2026 |
| v1.32.3 | May 2025 | February 28, 2027 (Planned) | February 28, 2027 (Planned) |

<a id="platform-version"></a>
## Platform Version { #platform-version }

<a id="platform-version-info"></a>
### Platform Version-Specific Information { #platform-version-info }

| Version | Release Date | Kubernetes Compatible Version | Description |
|------|------------|--------------------|-----|
| 1.202505.0 | 2025.11 | v1.28–v1.32 | Indicates a cluster that does not have a platform version applied. |
| 1.202511.0 | 2025.11 | v1.28–v1.33 | Initial release of the platform version |
| 1.202511.1 | 2025.12 | v1.28–v1.33 | Fixed an issue for health check port configuration |
| 1.202602.0 | 2026.02 | v1.29–v1.33 | Added features<br>- Kubernetes taint configuration <br>- Support for Kubernetes component configuration for max-pods <br>- Support for etcd data encryption with Secure Key Manager <br>- Support for CGroup v2 OS image<br><br>Feature Updates<br>- Support for improved traffic handling during node and node group deletion |
| 1.202605.0 | 2026.05 | v1.30–v1.34 | Feature Updates<br>- Added support for worker node CGroup v1 → v2 migration<br>- Enabled `ImageVolume` feature gate for Kubernetes v1.34 clusters<br>- Added support for konnectivity for communication between kube-apiserver and pods<br>- Added support for etcd upgrade |
| 1.202608.0 | August, 2026 | v1.31–v1.35 | Improved error message display when addon installation/update fails |

<a id="platform-component-versions"></a>
### Key component versions by platform version { #platform-component-versions }

| Platform Version | etcd | containerd |
| :--- | :--- | :--- |
| **1.202505.0** | v3.4.16 | 1.6.21 |
| **1.202511.0** | v3.4.16 | 1.6.21 |
| **1.202511.1** | v3.4.16 | 1.6.21 |
| **1.202602.0** | v3.4.16 | 1.6.21 |
| **1.202605.0** | v3.5.29 | k8s v1.33 or earlier: 1.7.27 </br> k8s v1.34 or later: 2.2.1 |
| **1.202608.0** | v3.5.29 | k8s v1.33 or earlier: 1.7.27 </br> k8s v1.34 or later: 2.2.1 |

<a id="platform-version-cgroup-v2-support"></a>
### Platform version for using CGroup v2 OS images { #platform-version-cgroup-v2-support }
* Clusters with a platform version earlier than 1.202602.0 cannot use OS images configured with CGroup v2.
* The configured CGroup version differs based on the release date of the OS image.
  * Images released before 2026/03/10: CGroup v1
  * Images released after 2026/03/10: CGroup v2
* Therefore, clusters with a platform version earlier than 1.202602.0 may have no available images when adding a node group.
* To use OS images with CGroup v2, the platform version must be upgraded to 1.202602.0 or later.