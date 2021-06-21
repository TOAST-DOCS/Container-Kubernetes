## Container > Kubernetes > Release Notes

### 2021. 06. 29.
#### More Features

* Supports Kubernetes v1.18.19.
* Can upgrade the cluster version.

### March 23, 2021
#### More Features
* Events occurred in a user cluster can be checked in NHN CloudTrail.

#### Bug Fixes
* Fixed an issue where it does not initialize properly when a node group is created with a graphic-optimized instance type (g2).

### February 23, 2021
* Changed the distribution version of the images used at the time of generating clusters or node groups.
  * Image update

### January 26, 2021
#### Bug Fixes

* Fixed an issue where autoscaler does not work in an environment with no internet gateway connection.
  * Image update

### December 29, 2020
#### Feature Updates
- Kubernetes CSR (Certificate Signing Request) is now available.

### November 24, 2020
#### More Features
- Autoscaling is now available.
#### Feature Updates
- Remaining load balancers and floating IPs are also deleted when deleting clusters.

### October 27, 2020
#### More Feature
* Kubernetes clusters now support GPU-based node groups.

### August 25, 2020
#### Feature Updates
* A random zone can be selected when creating a Kubernetes cluster on console.

### June 23, 2020
#### Release of New Service 
* Kubernetes clusters can be created and managed on console. 
