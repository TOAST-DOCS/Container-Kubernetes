## Container > Kubernetes > Overview
This document briefly describes what Kubernetes is about, as well as NHN Cloud Kubernetes in particular. 

## Kubernetes
Kubernetes is an open-source platform which manages contianerized workload and services. Following features are available: 

* Service discovery and load balancing 
* Storage orchestration 
* Automated rollouts and rollbacks 
* Automated bin packing 
* Self-healing
* Secret and configuration management 

For more details, see the following documents on Kubernetes:  

* [What is Kubernetes?](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/)

## Kubernetes Cluster 
A Kubernetes cluster is a computer cluster connected to each other and run as one unit. Kubernetes provides cluster-based features

### Configuration 
A Kubernetes cluster consists of a master and nodes.  

#### Master 
Master is in charge of cluster management. All activites of a cluster, such as application scheduling, scaling, or updating are managed. Master components, in general, run on seperate machines (virtual or physical machine). To ensure high availability, a cluster can be composed of many masters. 

#### Node
A node is a worker machine where user's application runs. One cluster may contain many nodes, which can run when connected to the master node. Nodes follow the commands of a master to run or suspend applications. 


## NHN Cloud Kubernetes
NHN Cloud Kubernetes creates and manages Kubernetes clusters to properly and safely run Kubernetes in the cloud. Users can create and manage Kubernetes clusters that suit just right for NHN Cloud on the web console. For safe and efficient operations, masters are managed by NHN Cloud, while nodes, services, and pods are under user's supervision.  

NHN Cloud Kubernetes provides the following features: 

* Creating and Managing Kubernetes just right for NHN Cloud
    * Integrate with NHN Cloud infrastructure services
    * Open services on load balancer 
    * Support persistent volume (PV) by integrating with block storage

* Managing Masters to Ensure High Availability 

* Easy Operations on Web Console 
    * Create, delete, and query clusters
    * Create, delete, and query node groups
    * Support kubectl configuration 
