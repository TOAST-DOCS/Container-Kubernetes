## Container > NHN Kubernetes Service (NKS) > Application Guide > Istio

### Overview
This document is a guide that describes examples of configuring and testing Istio on NHN Kubernetes Service (NKS) clusters. NKS provides guides and Istio images from the NCR registry to help you configure Istio on clusters that are not connected to the internet. You can use this guide to configure Istio using the images provided by NKS, or you can configure Istio yourself by referring to the official Istio document.

### Istio
A service mesh is an infrastructure layer for microservices that specializes in controlling and efficiently managing communication between services in a distributed system. 
Istio is one of the mostly used open source service meshes. This example shows how the Istio service of demo profile works after being deployed to clusters. For more information about Istio, see [Istio](https://istio.io/latest/about/service-mesh/).


### Prerequisites for istio deployment
Check the correct registry information for the Istio version and cluster to deploy and set them in the variables. The variables you set will be used during the deployment process.

```
$VERSION="{Version of istio you want to download}"
$ REGISTRY="{Region-specific registry}"
```

#### Supported Kubernetes versions by Istio version

| Version | Supported Kubernetes versions |
| --- | --- |
| 1.20.0 | 1.25, 1.26, 1.27, 1.28 |
| 1.19.0 | 1.25, 1.26, 1.27, 1.28 |

> [Note]
NKS provides installation only for the Istio versions specified in the table above. If you want to use a version not provided by NKS, you must configure Istio yourself. For information about supported Istio versions, see the [istio support policy](https://istio.io/latest/docs/releases/supported-releases/).


#### Registry information by region and Internet environment
| Region | Internet Connection | Registry |
| --- | --- | --- |
| Korea (Pangyo) region | O | dfe965c3-kr1-registry.container.nhncloud.com/container_service/istio |
| | X | private-dfe965c3-kr1-registry.container.nhncloud.com/container_service/istio |
| Korea (Pyeongchon) region | O | 6e7f43c6-kr2-registry.container.cloud.toast.com/container_service/istio |
| | X | private-6e7f43c6-kr2-registry.container.cloud.toast.com/container_service/istio |

> [Note]
For clusters to use NCR registries that are not connected to the internet, it is necessary to configure an environment to use a private URI. For information on how to use Private URI, refer to the [](/Container/NCR/ko/user-guide/#private-uri)NHN Cloud Container Registry (NCR) User Guide[](/Container/NCR/ko/user-guide/#private-uri).


#### Install the ORAS command-line tool
Download the OCI Registry As Storage (ORAS) command-line tool to pull artifacts. ORAS is a tool that provides a way to push and pull OCI artifacts from the OCI Registry.
Run the command below to install the ORAS command-line tool. For detailed instructions on how to use the ORAS command line tool, see the [ORAS docs](https://oras.land/docs/).

```
oras_version="1.1.0"
$ curl -LO "https://github.com/oras-project/oras/releases/download/v${ORAS_VERSION}/oras_${ORAS_VERSION}_linux_amd64.tar.gz"
$ mkdir -p oras-install/
$ tar -zxf oras_${ORAS_VERSION}_*.tar.gz -C oras-install/
$ sudo mv oras-install/oras /usr/local/bin/
$ rm -rf oras_${ORAS_VERSION}_*.tar.gz oras-install/
$ export PATH="/usr/local/bin:$PATH"
```


### Install Istio

#### 1. Download and install istioctl using the ORAS command line tool.
istioctl is a configuration command-line utility that allows you to debug and diagnose the service mesh deployment provided by Istio, offering various features, such as setting Istio deployment configuration options. For more information on istioctl, see [istioctl](https://istio.io/latest/docs/reference/commands/istioctl/). 

You must download the istioctl tool that satisfies the version of Istio you are deploying. Run the command below to download istioctl.
```
$ oras pull ${REGISTRY}/istioctl-tool:${VERSION}
$ tar -zxvf istio-${VERSION}-linux-amd64.tar.gz
$ cd istio-${VERSION}
$ export PATH=$PWD/bin:$PATH
```

#### 2. Deploy Istio components using istioctl.
Profiles provide custom deployment features for sidecars in the Istio control plane and data plane. The profile that is set up determines which components are enabled upon deployment. Depending on the environmental configuration of your cluster, you can use the default profile or customize the configuration to meet your specific needs. For more information on Istio profiles, see [Istio installation configuration profiles](https://istio.io/latest/docs/setup/additional-setup/config-profiles/). In this example, demo profile that is provided by default is used for deployment.

Deploy Istio components through the `istioctl install` command. If you omit a profile, the default profile is applied. Below is an example of deploying an istio component using the `istioctl install` command.
```
$ istioctl install --set profile=demo --set hub=${REGISTRY} -y
✔ Istio core installed
✔ Istiod installed
✔ Egress gateways installed
✔ Ingress gateways installed
✔ Installation complete
```

To create and deploy a manifest, use the `istioctl manifest generate` command. The following is an example of using the `istioctl manifest generate` command to deploy an istio component.
```
$ istioctl manifest generate --set profile=demo --set hub=${REGISTRY} > istio-manifest-demo.yaml
$ kubectl apply -f istio-manifest-demo.yaml
customresourcedefinition.apiextensions.k8s.io/authorizationpolicies.security.istio.io created
customresourcedefinition.apiextensions.k8s.io/destinationrules.networking.istio.io created
customresourcedefinition.apiextensions.k8s.io/envoyfilters.networking.istio.io created
customresourcedefinition.apiextensions.k8s.io/gateways.networking.istio.io created
customresourcedefinition.apiextensions.k8s.io/istiooperators.install.istio.io created
customresourcedefinition.apiextensions.k8s.io/peerauthentications.security.istio.io created
customresourcedefinition.apiextensions.k8s.io/proxyconfigs.networking.istio.io created
customresourcedefinition.apiextensions.k8s.io/requestauthentications.security.istio.io created
customresourcedefinition.apiextensions.k8s.io/serviceentries.networking.istio.io created
customresourcedefinition.apiextensions.k8s.io/sidecars.networking.istio.io created
customresourcedefinition.apiextensions.k8s.io/telemetries.telemetry.istio.io created
customresourcedefinition.apiextensions.k8s.io/virtualservices.networking.istio.io created
customresourcedefinition.apiextensions.k8s.io/wasmplugins.extensions.istio.io created
customresourcedefinition.apiextensions.k8s.io/workloadentries.networking.istio.io created
customresourcedefinition.apiextensions.k8s.io/workloadgroups.networking.istio.io created
serviceaccount/istio-egressgateway-service-account created
serviceaccount/istio-ingressgateway-service-account created
serviceaccount/istio-reader-service-account created
serviceaccount/istiod created
clusterrole.rbac.authorization.k8s.io/istio-reader-clusterrole-istio-system created
clusterrole.rbac.authorization.k8s.io/istiod-clusterrole-istio-system created
clusterrole.rbac.authorization.k8s.io/istiod-gateway-controller-istio-system created
clusterrolebinding.rbac.authorization.k8s.io/istio-reader-clusterrole-istio-system created
clusterrolebinding.rbac.authorization.k8s.io/istiod-clusterrole-istio-system created
clusterrolebinding.rbac.authorization.k8s.io/istiod-gateway-controller-istio-system created
validatingwebhookconfiguration.admissionregistration.k8s.io/istio-validator-istio-system created
configmap/istio created
configmap/istio-sidecar-injector created
mutatingwebhookconfiguration.admissionregistration.k8s.io/istio-sidecar-injector created
deployment.apps/istio-egressgateway created
deployment.apps/istio-ingressgateway created
deployment.apps/istiod created
poddisruptionbudget.policy/istio-egressgateway created
poddisruptionbudget.policy/istio-ingressgateway created
poddisruptionbudget.policy/istiod created
role.rbac.authorization.k8s.io/istio-egressgateway-sds created
role.rbac.authorization.k8s.io/istio-ingressgateway-sds created
role.rbac.authorization.k8s.io/istiod created
rolebinding.rbac.authorization.k8s.io/istio-egressgateway-sds created
rolebinding.rbac.authorization.k8s.io/istio-ingressgateway-sds created
rolebinding.rbac.authorization.k8s.io/istiod created
service/istio-egressgateway created
service/istio-ingressgateway created
service/istiod created
```

Check whether the component is deployed successfully.
```
$ kubectl get all -n istio-system
name ready status restarts age
pod/istio-egressgateway-d859c887c-khf6r 1/1 Running 0 86s
pod/istio-ingressgateway-b8844867c-5nqjj 1/1 Running 0 86s
pod/istiod-8c7fbbdc8-xwjbt 1/1 Running 0 90s


name type cluster-ip external-ip port(s) age
service/istio-egressgateway ClusterIP 10.254.107.64 <none> 80/TCP,443/TCP 86s
service/istio-ingressgateway LoadBalancer 10.254.64.122 114.110.160.100 15021:31991/TCP,80:32023/TCP,443:32227/TCP,31400:32213/TCP,15443:30244/TCP 86s
service/istiod ClusterIP 10.254.110.81 <none> 15010/TCP,15012/TCP,443/TCP,15014/TCP 90s


name ready up-to-date available age
deployment.apps/istio-egressgateway 1/1 1 1 1 86s
deployment.apps/istio-ingressgateway 1/1 1 1 1 86s
deployment.apps/istiod 1/1 1 1 90s

name desired current ready age
replicaset.apps/istio-egressgateway-d859c887c 1 1 1 1 86s
replicaset.apps/istio-ingressgateway-b8844867c 1 1 1 86s
replicaset.apps/istiod-8c7fbbdc8 1 1 1 90s
```

### Deploy application
Deploy an application to verify that Istio is installed and works properly. For this example, the Bookinfo sample application that comes with Istio is used. The sample application consists of four microservices: productpage, details, reviews, and ratings. For more information about the Bookinfo sample application, see [Istio Bookinfo Application](https://istio.io/latest/docs/examples/bookinfo/).

#### 1. Label namespace
To use the Istio sidecar proxy, you need to label the namespace where the pods will be created. The label is used to identify the namespace where you want to deploy Istio components. When a Pod is deployed to a labeled namespace, the Istio sidecar proxy is automatically deployed to the Pod.

Below is an example of setting up labels in the default namespace.
```
$ kubectl label namespace default istio-injection=enabled
namespace/default labeled
```

#### 2. Deploy application components
Deploy the application components and check whether they are deployed successfully.

Deploy the application with the command below.
```
$ kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
service/details created
serviceaccount/bookinfo-details created
deployment.apps/details-v1 created
service/ratings created
serviceaccount/bookinfo-ratings created
deployment.apps/ratings-v1 created
service/reviews created
serviceaccount/bookinfo-reviews created
deployment.apps/reviews-v1 created
deployment.apps/reviews-v2 created
deployment.apps/reviews-v3 created
service/productpage created
serviceaccount/bookinfo-productpage created
deployment.apps/productpage-v1 created
```

Check whether the component is deployed successfully.
```
$ kubectl get svc,pods
name type cluster-ip external-ip port(s) age
service/details ClusterIP 10.254.91.224 <none> 9080/TCP 13m
service/kubernetes ClusterIP 10.254.0.1 <none> 443/TCP 37d
service/productpage ClusterIP 10.254.111.194 <none> 9080/TCP 13m
service/ratings ClusterIP 10.254.232.169 <none> 9080/TCP 13m
service/reviews ClusterIP 10.254.29.51 <none> 9080/TCP 13m

name ready status restarts age
pod/details-v1-5f4d584748-flpdl 2/2 Running 0 13m
pod/productpage-v1-564d4686f-z97zr 2/2 Running 0 13m
pod/ratings-v1-686ccfb5d8-g68rq 2/2 Running 0 13m
pod/reviews-v1-86896b7648-sv5vc 2/2 Running 0 13m
pod/reviews-v2-b7dcd98fb-vppzv 2/2 Running 0 13m
pod/reviews-v3-5c5cc7b6d-4hpr8 2/2 Running 0 13m
```

Use the commands below to verify that your application is working properly.
```
$ kubectl exec "$(kubectl get pod -l app=ratings -o jsonpath='{.items[0].metadata.name}')" -c ratings -- curl -sS productpage:9080/productpage | grep -o "<title>.*</title>"
<title>Simple Bookstore App</title>
```

### Expose service
Up to this point, the service is set up as a `ClusterIP`, which is only accessible to hosts inside the cluster. While Kubernetes provides various types of `Service` objects ( `NodePort`, `LoadBalancer`, etc.) for exposing services, Istio provides gateway objects to handle a wider range of network traffic. The following example shows the process of exposing a service using the sample bookinfo gateway provided by Istio. For more information on Istio gateways, see [Istio Gateways](https://istio.io/latest/docs/concepts/traffic-management/#gateways).

#### Configure ingress gateway
Further configure the Istio ingress gateway to expose the service.

Deploy the Istio Ingress Gateway component with the command below.
```
$ kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml
gateway.networking.istio.io/bookinfo-gateway created
virtualservice.networking.istio.io/bookinfo created
```

Check whether there are no issues with the ingress gateway configuration with the `istioctl analyze` command.
```
$ istioctl analyze
✔ No validation issues found when analyzing namespace: default.
```

#### Access service
Check the gateway URL information to access the exposed service. The gateway URL defaults to the configuration `Ingress Host:Ingress IP`. How you find the gateway URL information depends on whether the cluster is configured in the Internet or an isolated network environment. Clusters configured on the Internet have the gateway URL configured based on the `EXTERNAL-IP` information of the load balancer, and clusters configured in an isolated network environment have the gateway URL configured based on the node ports.

For clusters configured on the internet network, you can check the gateway URL information by running the command below.
```
$ export INGRESS_HOST=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
$ export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].port}')
$ export GATEWAY_URL=$INGRESS_HOST:$INGRESS_PORT

$ echo "$GATEWAY_URL"
114.110.160.79:80
```

For clusters configured on an isolated network, you can check the gateway URL information by running the command below.
```
$ export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].nodePort}')
$ export INGRESS_HOST=$(kubectl get po -l istio=ingressgateway -n istio-system -o jsonpath='{.items[0].status.hostIP}')
$ export GATEWAY_URL=$INGRESS_HOST:$INGRESS_PORT

$ echo "$GATEWAY_URL"
172.16.0.42:30745
```

Send a request to the gateway URL to verify that the service is properly exposed. 
```
$ curl http://114.110.160.79:80/productpage
<!DOCTYPE html>
<html>
  <head> <title>Simple Bookstore App</title>
    <title>Simple Bookstore App</title>
<meta charset="utf-8"> <head>Simple Bookstore App</head
...
```


### Delete Istio
Perform the command below to remove the components related to the bookinfo application that were used in the example.
```
$ kubectl delete -f samples/bookinfo/networking/bookinfo-gateway.yaml
$ kubectl delete -f samples/bookinfo/platform/kube/bookinfo.yaml
```

If you want to remove all components of Istio from your cluster, run the command below.
```
$ istioctl unintall --purge -y
$ kubectl delete namespace istio-system
$ kubectl label namespace default istio-injection- $ kubectl label namespace default istio-injection-
```

If you want to remove only a specific Istio control plane instead of deleting all components of Istio, perform the command below.
```
$ istioctl uninstall <your original installation options>
```