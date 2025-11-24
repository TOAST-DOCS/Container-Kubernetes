## Container > NHN Kubernetes Service(NKS) > Version Guide

<a id="cluster-version-management"></a>
## Manage Cluster Version

An NKS cluster manages Kubernetes and platform versions for each cluster control plane and worker node group. The differences between the two versions are as follows:

### Kubernetes Version
- A version defined by upstream Kubernetes.
- It determines the version of the main Kubernetes components that make up the NKS cluster.
- Main components: `kube-apiserver`, `kube-controller-manager`, `kube-scheduler`, `kubelet`, `kube-proxy`

### Platform Version
- A version defined at the NKS service level.
- Multiple components that make up a cluster are defined and managed as a single version.
- Key components: Control plane components such as `containerd` and `etcd`, key worker node components, various system components, and system management tools.

### Upgrade Targets by Version Status

| Kubernetes Version Up-to-Date | Platform Version Up-to-Date | Upgrade Target |
|----------------------------|----------------------|----------------|
| X | X | Kubernetes Version and Platform Version |
| X | O | Kubernetes Version |
| O | X | Platform Version |
| O | O | None |

> Control plane version information can be found on the **View Cluster** screen, and worker node group version information can be found on each **View Worker Node Group** screen.

<a id="support-policy"></a>
## Support Policy for Kubernetes Version

### Available Cluster Versions
- You can create a new cluster using the three most recent Kubernetes versions released by NKS.
- When a new version is released, the oldest version from the existing list of available versions will be automatically removed.
- Kubernetes patch versions may be updated according to internal policies.

### Service Support Policy
To ensure stable service operation, we apply a service support policy for each Kubernetes version.

- NKS service support is determined based on the **Kubernetes version**.
- The service support policy applies based on the release date of the new Kubernetes version (`x.y`) of NKS.
- NKS continuously provides the latest Kubernetes version, and existing clusters can be maintained up-to-date through upgrades.

| Service Support Policy | Application Criteria | Guaranteed NKS Functionality | Cluster Upgrade |
|-----------------|------------|------------------|------------------|
| **Service Support** | Less than about 14 months after the Kubernetes version release | Guaranteed | Possible |
| **Service Not Supported** | More than about 14 months after the Kubernetes version release | Not Guaranteed | However, upgrade support is provided for 10 months after the end of service support |

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
## Schedule for Kubernetes Version-Specific Support 

> [Notice] The Kubernetes version support policy will change starting in November 2025.
> - The existing schedule and policy will apply up to v1.32.
> - The new policy will apply starting from v1.33.
> - The dates in the table are based on UTC+00:00.

### New Policy Applied Version (v1.33 or later)

| Version | Release | End of Service Support (Upgrade Support) | End of Service Support (EOS) |
|------|--------|---------------------------------|---------------------|
| v1.33 | November, 2025 | January 31, 2027 | November 30, 2027 |

### Old Policy Applied Version (v1.32 or earlier)

| Version | Release | End of Service Support (Upgrade Support) | End of Service Support (EOS) |
|------|--------|---------------------------------|---------------------|
| v1.28.3 | 2024.02 | 2025.11.30 | 2025.11.30 |
| v1.29.3 | 2024.05 | 2026.02.28 (Planned) | February 28, 2026 (Planned) |
| v1.30.3 | August 2024 | May 31, 2026 (Planned) | May 31, 2026 (Planned) |
| v1.31.4 | February 2025 | August 31, 2026 (Planned) | August 31, 2026 (Planned) |
| v1.32.3 | May 2025 | February 28, 2027 (Planned) | February 28, 2027 (Planned) |

<a id="platform-version"></a>
## Platform Version

### Platform Version-Specific Information

| Version | Release Date | Kubernetes Compatible Version | Description |
|------|------------|--------------------|-----|
| 1.202505.0 | 2025.11 | v1.28–v1.32 | Indicates a cluster that does not have a platform version applied. |
| 1.202511.0 | 2025.11 | v1.28–v1.33 | Initial release of the platform version |
