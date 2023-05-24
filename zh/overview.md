## Container > NHN Kubernetes Service (NKS) > Overview
This document briefly describes what Kubernetes is, and outlines NHN Kubernetes Service (NKS) provided by NHN Cloud. 

## Kubernetes
Kubernetes is an open-source platform which manages containerized workload and services. Kubernetes provides the following features: 

* Service discovery and load balancing 
* Storage orchestration 
* Automated rollouts and rollbacks 
* Automated bin packing 
* Self-healing
* Secret and configuration management 

For more details, see the following documents on Kubernetes:  

* [What is Kubernetes?](https://kubernetes.io/docs/concepts/overview/)

## Kubernetes Cluster 
A Kubernetes cluster is a computer cluster connected to each other and run as one unit. Features provided by Kubernetes operate on a cluster-by-cluster basis and can be configured on a cluster-by-cluster basis.

### Configuration 
A Kubernetes cluster consists of a master and nodes.  

#### Master 
A master is in charge of cluster management. A master manages all activities of a cluster, such as application scheduling, scaling, or updating. In general, the components of master run on separate machines (virtual or physical machines). To ensure high availability, multiple masters can be configured on a cluster. 

#### Node
A node is a worker machine where user's application runs. One cluster may contain many nodes. The nodes can run when connected to the master. Nodes follow the commands of a master and perform the operations, such as running or suspending applications.


## NHN Kubernetes Service (NKS)
NHN Kubernetes Service (NKS) is a service that allows users to create and manage Kubernetes clusters to run Kubernetes in the cloud properly and safely. Users can use the web console to create and manage Kubernetes clusters that are suitable for NHN Cloud. For safe and efficient operations, masters are managed by NHN Cloud, while nodes, services, and pods are managed by users.

The main features of NKS are as follows: 

* Creating and managing Kubernetes clusters suitable for NHN Cloud
    * Integrate with NHN Cloud infrastructure services
    * Open services using load balancer 
    * Support persistent volume (PV) by integrating with block storage

* Managing masters to ensure high availability 

* Easy Operations on Web Console 
    * Create, delete, and query clusters
    * Create, delete, and query node groups
    * Support kubectl configuration 
