## Container > NHN Kubernetes Service (NKS) > Troubleshooting Guide

This guide explains how to solve various problems that you might encounter while using NHN Kubernetes Service (NKS).

### > Disk space is reduced as the size of the worker node's container log file increases.

#### Set log rotation
For container log file management (setting the maximum file size, the number of log files, and so on), add the following setting to the worker node.

```
$ sudo bash -c "cat > /etc/logrotate.d/docker" <<EOF
/var/lib/docker/containers/*/*.log {
    rotate 10
    copytruncate
    missingok
    notifempty
    compress
    maxsize 100M
    daily
    dateext
    dateformat -%Y%m%d-%s
    create 0644 root root
}
EOF
```

In the worker node, container log rotation based on the setting above is performed through cron at around 3 AM every day.

> [Note] For instance images later than `CentOS 7.8 - Container (2021.07.27)`, the log rotation setting as above is provided by default.
<br>

#### Synchronize log rotation setting

While operating clusters, log rotation setting for some of the worker nodes might change in the following cases.

  * When the instance images are different between node groups
    * Node based on images with log rotation setting applied vs. unapplied
  * When the setting is added directly to the node based on images with log rotation setting unapplied
    * New node added by cluster auto scaler or node group size adjustment vs. existing node
  * When the log rotation setting details are changed and applied directly
    * New node added by cluster auto scaler or node group size adjustment vs. existing node

To keep the log rotation setting consistent for all worker nodes in the circumstances above, you can consider the following method for synchronization.

##### ```Synchronize the log rotation setting file via SSH```

The code shown below is the shell commands to create a script that compares the log rotation setting file for the cluster's all worker nodes based on SSH, and copies the file to the nodes that require the setting.

The following are the prerequisites for executing the commands.

* Open SSH port for the worker node (open TCP port 22 from the security group)
* The key pair file that was used to create the worker node
* The kubectl binary
* The kubeconfig file for the target cluster
* The logrotate setting file to be used as the synchronization source

You need to modify the first parameter of 3 cp commands before running the commands.<br>
A synchronization task is performed every midnight using the shell script and cron job generated after the command execution is completed.
```
$ cd ~
$ mkdir logrotate_for_container
$ cd logrotate_for_container
$
$ cp /path/to/my/kubeconfig/file kubeconfig.yaml
$ cp /path/to/my/keypair/file keypair.pem
$ cp /path/to/my/docker/logrotate/file docker_logrotate_config
$
$ cat > sync_logrotate.sh <<EOF
#!/bin/bash

set -o errexit

##################################################################
# KUBECONFIG:   kubeconfig file for a target cluster             #
# KEYPAIR:      keypair file for worker nodes                    #
# LOCAL_CONFIG: logrotate configuration file used as sync source #
##################################################################
KUBECONFIG="kubeconfig.yaml"
KEYPAIR="keypair.pem"
LOCAL_CONFIG="docker_logrotate_config"
REMOTE_CONFIG="/etc/logrotate.d/docker"

base_config_hash=`md5sum ${LOCAL_CONFIG} | awk '{print $1}'`
worker_nodes=$(kubectl --kubeconfig=$KUBECONFIG get nodes -A -o jsonpath='{.items[*].status.addresses[?(@.type=="InternalIP")].address}')

echo "[`date`] Start to synchronize the logrotate configuration for docker container"
echo "  * Worker nodes list = ${worker_nodes}"
echo "  * Comparing local config hash with remote config hash (local config hash = ${base_config_hash})"

sync_nodes=""
for node in ${worker_nodes}; do
  node_conf_hash=`ssh -i ${KEYPAIR} -o StrictHostKeyChecking=no centos@${node} "md5sum ${REMOTE_CONFIG}"| awk '{print $1}'`

  if [ "${base_config_hash}" != "${node_conf_hash}" ]; then
    echo "    -> Different hash with /etc/logrotate.d/docker@${node} (remote config hash = ${node_conf_hash})"
    sync_nodes="${sync_nodes} ${node}"
  fi
done

if [ -n "${sync_nodes}" ]; then
  echo "  * Copying ${LOCAL_CONFIG} to ${REMOTE_CONFIG} at target nodes: ${sync_nodes}"
  for node in ${sync_nodes}; do
    scp -i ${KEYPAIR} -o StrictHostKeyChecking=no ${LOCAL_CONFIG} centos@${node}:~/${LOCAL_CONFIG}.tmp >/dev/null
    node_conf_hash=`ssh -i ${KEYPAIR} -o StrictHostKeyChecking=no centos@${node} "sudo cp ${LOCAL_CONFIG}.tmp ${REMOTE_CONFIG} && rm ${LOCAL_CONFIG}.tmp && md5sum ${REMOTE_CONFIG}" | awk '{print $1}'`
    if [ $? == 0 ]; then
      echo "    -> Copy done... New hash of ${REMOTE_CONFIG}@${node} = ${node_conf_hash}"
    else
      echo "    -> Something's wrong at ${node}"
    fi
  done
else
  echo "  * Logrotate configurations are up to date on all worker nodes"
fi
echo "[`date`] Finish to synchronize logrotate configuration"
EOF
$
$ chmod +x sync_logrotate.sh
$
$ crontab <<EOF
0 0  * * * ~/logrotate_for_container/sync_logrotate.sh > ~/logrotate_for_container/sync_logrotate.log
EOF
$
```



> [Note] The description above is only one of the methods for synchronization. If there is a better way for your environment, you can use it to perform synchronization.


### > The status of the Pod appears as ImagePullBackOff.

Since November 20, 2020, dockerhub has implemented a policy that places the following limits on the number of pull requests for container image. For more information on limits, see [Understanding Docker Hub Rate Limiting](https://www.docker.com/increase-rate-limits) and [Pricing & Subscriptions](https://www.docker.com/pricing).


| Account level | Before November 20, 2020 | Since November 20, 2020 |
| --- | --- | --- |
| Unauthenticated user | 2,500req/6H | 100req/6H |
| Free Tier | 2,500 req/6H | 200 req/6H |
| Pro/Team/Large Tier | Unlimited | Unlimited |

In the case of pulling container images from dockerhub on the worker node of NKS, if you download more than 100 images within 6 hours without logging in to dockerhub, you will no longer be able to download images. In particular, workers that do not have floating IPs associated can reach the limit faster because they use public IPs.

The workaround is as follows.

* If you log in to dockerhub, the number of images you can download increases, and you are limited by account level, not by public IP. Create a dockerhub account, sign up for a tier that provides the desired number of pulls, and use NKS. See [How to use a Private Registry with Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/).
* If you want to be limited by an independent public IP without logging in to dockerhub, assign a floating IP to the worker node. 


### > Failed to pull image `k8s.gcr.io/pause:3.2` in a closed network environment.
This issue is caused by clusters in a closed network environment not receiving images from the public registry, and can occur in clusters created before August 2024. Images that are deployed by default, such as the `k8s.gcr.io/pause:3.2` image, are pulled from the NHN Cloud internal registry when a worker node is created. However, if the image is deleted after the initial image is pulled, the problem may occur. The list of images that are deployed by default when creating a cluster is shown below.

* kubernetesui/dashboard
* k8s.gcr.io/pause
* k8s.gcr.io/kube-proxy
* kubernetesui/dashboard
* kubernetesui/metrics-scraper
* quay.io/coreos/flannel
* quay.io/coreos/flannel-cni
* calico-kube-controllers
* calico-typha
* calico-cni
* calico-node
* coredns/coredns
* k8s.gcr.io/metrics-server-amd64
* k8s.gcr.io/metrics-server/metrics-server
* gcr.io/google_containers/cluster-proportional-autoscaler-amd64
* k8s.gcr.io/cpa/cluster-proportional-autoscaler-amd64
* k8s.gcr.io/cpa/cluster-proportional-autoscaler-amd64
* k8s.gcr.io/sig-storage/csi-attacher
* k8s.gcr.io/sig-storage/csi-provisioner
* k8s.gcr.io/sig-storage/csi-snapshotter
* k8s.gcr.io/sig-storage/csi-resizer
* k8s.gcr.io/sig-storage/csi-node-driver-registrar
* k8s.gcr.io/sig-storage/snapshot-controller
* docker.io/k8scloudprovider/cinder-csi-plugin
* k8s.gcr.io/node-problem-detector
* k8s.gcr.io/node-problem-detector/node-problem-detector
* k8s.gcr.io/autoscaling/cluster-autoscaler
* nvidia/k8s-device-plugin

The same problem can occur for the image.

Base images can be deleted by kubelet's Image garbage collection. For more information on kubelet garbage, see [Garbage Collection](https://kubernetes.io/docs/concepts/architecture/garbage-collection/). For NKS, imageGCHighThresholdPercent, imageGCLowThresholdPercent are set by default.
```
imageGCHighThresholdPercent=85: Always run image Garbage Collection to remove unused images when disk utilization is greater than 85%.
imageGCLowThresholdPercent=80: Do not run image Garbage Collection when disk utilization is 80% or less.
```

#### Workaround
By enabling NKS registry, you can change the cluster settings to receive container images from the NHN Cloud internal registry instead of from a public registry in a closed network environment. You can enable the NKS registry from the Cluster inquiry screen.


### > Image pull for Flannel CNI related images from `quay.io` fails.

The repository address for Flannel-related container images is based on `quay.io`. The pull service for these images on `quay.io` has been terminated, so they can no longer be pulled.

Here's how to solve the problem:

* Solution 1: Activate the NKS Registry
    * Activating the NKS registry changes the repository addresses of required containers, including Flannel, to NKS internal addresses, preventing image pull issues.

* Solution 2: Change the repository addresses of Flannel-related images
    * Change the repository addresses to those that allow image pulls.
    * Target resource: `kube-flannel` or `kube-flannel-ds-amd64` DaemonSet  of `kube-system` namespace
    * AS-IS: `quay.io/coreos/flannel*`
    * TO-BE: `ghcr.io/flannel-io/flannel*`

### > In k8s v1.24 and later, the `pull from host docker.pkg.github.com failed` error occurs and the image pull fails. 

This issue is caused by the change of the package registry on github from the Docker registry to the Container registry. Clusters in v1.24 or earlier used Docker as the container runtime and could pull images from the `docker.pkg.github.com` registry, but NKS clusters in v1.24 and later use cotainerd as the container runtime and can no longer pull images from the `docker.pkg.github.com` registry. For more information about package registry migration, see Migration to Container registry from the Docker registry[migrating](https://docs.github.com/en/packages/working-with-a-github-packages-registry/migrating-to-the-container-registry-from-the-docker-registry).


The workaround is as follows
Change the base of the image URL defined in the Pod manifest `from docker.pkg.github.com` to `gchr.io`.

### > `cannot allocate memory` error occurs and the Pod's status appears `as FailedCreatePodContainer`.

A bug in the Linux kernel's kernel object accounting feature for memory cgroups. It occurs primarily in versions 3.x and 4.x of the Linux kernel and is known as the dying memory cgroup problem issue. Users can bypass this issue by disabling the kernel object accounting feature for memory cgroups at the image level. 

#### Apply the workaround to existing clusters
Connect to the worker node, change the boot options, and restart it.

1. Open the `/etc/default/grub` file and add `cgroup.memory=nokmem`to the existing value `of GRUB_CMDLINE_LINUX`.

```diff
# vim /etc/default/grub
- GRUB_CMDLINE_LINUX="..."
+ GRUB_CMDLINE_LINUX="... cgroup.memory=nokmem"
```

2. Reflect your settings.
```
$ grub2-mkconfig -o /boot/grub2/grub.cfg
```

3. Restart the worker node.
```
$ reboot
```

This issue may not always occur, and may depend on the nature of your application. If you are concerned about this issue, you can use the custom image feature in NKS to use a worker node image with the above workaround applied from the start.

#### Apply the workaround to newly created clusters using the NKS Custom Image feature
NKS provides the feature to create a group of worker nodes based on your custom image. You can use the NKS custom image feature to create an image with kernel object accounting disabled for memory cgroups and utilize it when creating a cluster. For more information about the feature, see [](/Container/NKS/ko/user-guide/#_25)Use Custom Image as Worker Image[](/Container/NKS/ko/user-guide/#custom-image).

1. While creating the image template, enter the following in the user script.
```
#!/bin/bash
args="cgroup.memory=nokmem"
grub_file="/etc/default/grub"
sudo sed -i "s/GRUB_CMDLINE_LINUX=\"\(.*\)\"/GRUB_CMDLINE_LINUX=\"\1 $args\"/" "$grub_file"

sudo grub2-mkconfig -o /boot/grub2/grub.cfg
```

### > rpc.statd is not running but is required for remote locking error occurs and the Pod fails to mount the NAS volume.

This issue is caused by the rpc.statd process on the worker node becoming a zombie process or being stopped by an administrator command. The rpcbind and rpc.statd processes on the worker node must be running normally in order to mount the volume. The workaround is as follows
```
systemctl restart rpc-statd
systemctl restart rpcbind
```

### > The Pod's file system does not reflect the increased capacity after the PV capacity is increased.
This is an issue that can occur in clusters with version 1.20 or later that were created before August 2024. You can run the script below to update the cinder-csi-driver deployed in your cluster to resolve the issue. Only newly created or increased capacity PVs after running the script will reflect the configuration update.

Define the absolute path value where the cluster's kubeconfig file is located in the kubeconfig_file_path variable, and then run the script.
```
#!/bin/bash
kubeconfig_file_path={kubeconfig file absolute path}
SECRET_NAME="cinder-csi-cloud-config"
NAMESPACE="kube-system"
# Fetch the secret using kubectl and parse the JSON output with jq
secret_data=$(kubectl --kubeconfig=$kubeconfig_file_path get secret "$SECRET_NAME" -n "$NAMESPACE" -o json)
# Extract the 'cloud-config' key and decode its value
cloud_config_base64=$(echo "$secret_data" | jq -r '.data["cloud-config"]')
if [[ "$cloud_config_base64" != "null" ]]; then
    # Decode the base64 value
    cloud_config=$(echo "$cloud_config_base64" | base64 --decode)
    # Add the [BlockStorage] section with rescan-on-resize=true
    modified_cloud_config=$(cat <<EOF
$cloud_config
[BlockStorage]
rescan-on-resize=true
EOF
)
    # Encode the modified cloud-config back to base64
    modified_cloud_config_base64=$(echo "$modified_cloud_config" | base64 | tr -d '\n')
    # Update the Kubernetes secret with the new base64-encoded data
    kubectl --kubeconfig=$kubeconfig_file_path patch secret "$SECRET_NAME" -n "$NAMESPACE" --type=json \
        -p="[{'op': 'replace', 'path': '/data/cloud-config', 'value':'$modified_cloud_config_base64'}]"
    echo "Secret $SECRET_NAME updated successfully."
else
    echo "cloud-config key not found in secret $SECRET_NAME"
fi
kubectl -n kube-system rollout restart daemonet cinder-csi-nodeplugin
kubectl -n kube-system rollout restart statefulset cinder-csi-controllerplugin
```

### > Error of timed out waiting for condition occurs and the volume mount to the Pod fails.
This is an issue that can occur when you mount large volumes in a Pod. By default, when Kubernetes mounts a volume, it changes the ownership and permissions on the contents of each volume to match the fsGroup specified in the SecurityContext of the Pod. If the volume is large, it can take a lot of time to check and change ownership and permissions, which can cause a timeout. 

To prevent timeouts from occurring, you can use the fsGroupChangePolicy field of the securityContext to change the way Kubernetes checks and manages ownership and permissions for volumes. For more information, see [Configure volume permission and ownership change policy for pods](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/#configure-volume-permission-and-ownership-change-policy-for-pods).


### > Setting the hostnetwork: true, dnsPolicy: ClusterFirstWithHostNet option on a Pod in a cluster with Calico-eBPF CNI causes UDP communication issues.
This is caused by the BPF NAT table not handling network packets correctly during UDP communication in Calico v3.28.0. When using eBPF, TCP communicates `in a connect-time load balancing (CTLB)` method and UDP communicates over `a NAT table`managed by BPF. This issue can be resolved by changing UDP communication to CTLB as well.

`Connect-time load balancing (CTLB)` is a network load balancing technique in which a backend server is selected in the first packet when a client first connects to a server, and all subsequent traffic is directed to the selected backend server. This ensures session persistence and reduces the overhead of performing load balancing every time.

You can resolve the issue by changing the UDP CTLB setting in the calico-node daemonset.
Below is how to change the setting.
```
kubectl edit daemonset.apps/calico-node -n kube-system
```
The following settings need to be added to the spec.template.spec.containers.env entry.
When the Pod template is modified, calico-node will resume on a rolling update basis.
```
- name: FELIX_BPFCONNECTTIMELOADBALANCING
    value: "Enabled"
- name: FELIX_BPFHOSTNETWORKEDNATWITHOUTCTLB
    value: "Disabled"
```

#### Cautions for setting up UDP communication after applying the workaround
UDP is a connectionless protocol, meaning that server/client communication transfers data without establishing or maintaining a separate session. However, UDP's `connect(` ) function, like the Golang `net.DialUDP(` ) function, allows you to associate a UDP socket with a specific address to send and receive data only to the specified address.
With Calico's eBPF, if you deploy a Pod that uses the UD `connect()` function in a cluster with connect-time load balancing (CTLB) enabled for UDP, you might experience communication issues when the Pod acting as a server is redeployed. This is because the UDP socket only attempts to send data to the initially connected server address. When a server Pod is redeployed, its IP address or network path might change, and because the UDP connect() socket only sends data to the old server address, communication might fail.
This is a known issue that occurs in CTLB environments due to the way UDP connect() works and the CTLB environment, you should be aware and careful when using UDP's connect() function in a cluster with Calico eBPF and UDP CTLB that you might encounter this communication issue.

### > istio is not working properly in a Calico-eBPF cluster.
When eBPF is enabled, it performs `connect-time` load balancing `(CTLB)`, which means that when a client tries to connect to a service, it selects a backend pod on the first packet and all subsequent packets are forwarded directly to that backend. Meanwhile, Istio deploys sidecar proxies to organize the service mesh, and the proxies intercept application traffic to act as control and monitoring.
When CTLB is enabled, packets are forwarded directly from the BPF MAP to the destination Pod, and the packets are tampered with in the process. As a result, packets are forwarded directly to the destination Pod, bypassing Istio's proxy. Because of this eBPF networking structure, Istio might not work properly. If you need to manage a cluster with istio, you must consider using a Calico-VXLAN cluster.


### > In a cluster using Calico-eBPF CNI, network failures occur when scaling up after node reduction.
This issue is caused by a bug found in calico/kube-controllers in Calico v3.28.0. During a node reduction, if the node on which the calico/kube-controllers pod is deployed is removed, the pod is scheduled and run on a different node. While calico/kube-controllers is being rerun, the node information is out of sync. In this state, if a node with the same name as the node that was removed is added, a network failure can occur.

This issue has been resolved in Calico v3.28.2. To use Calico v3.28.2, you must upgrade your Kubernetes version or recreate your cluster. 


### > Failed to upgrade clusters.

#### When creating an NKS, check whether finalizers are set on the resources that are deployed by default.
If finalizers are set on deployed resources when the NKS is created, the upgrade fails because the resources cannot be removed. When the upgrade of all worker node groups is complete, the NKS initial deployment resources are redeployed.If finalizers are set on the NKS initial deployment resource during this process, the redeployment of the resources will fail and the upgrade will be stopped. To resolve this issue, you must remove the finalizers setting on the NKS initial deployment resource before upgrading.

The command to remove the finalizers setting is as follows
```
kubectl patch {resource type} {resource name} -n {namespace} --type=json -p='[{"op": "remove", "path": "/metadata/finalizers"}]'
```
Example
```
kubectl patch clusterrole calico-kube-controllers --type=json -p='[{"op": "remove", "path": "/metadata/finalizers"}]'
```


### > When scaling out nodes or adding node groups in a cluster running v1.29.3 or earlier with an inactive NKS registry, the calico-node pod deployment fails, causing the node initialization task to fail.
This issue occurs when calico-related pods (calico-node, calico-kube-controllers, calico-typha) are not deployed when scaling out nodes or adding node groups due to incorrect image repository settings.

This issue mainly results from clusters created before May 2024. The cluster created at that time had the NKS-specific image registry disabled by default, and the repository path for the Calico container image was incorrect, making image downloads impossible.

#### How to check if the symptom occurs
When checking with the `kubectl get all -n kube-system` command, the status of the pods below deployed on the node where the scale-out failed remains as **ImagePullBackOff** or **ErrImagePull**.
- calico-node
- calico-kube-controllers
- calico-typha

#### Solution
You can resolve this issue by changing the Calico-related image repository URL to a public repository. However, this only applies to clusters with internet access. Caution is required during this process, as cluster pod networking may be temporarily interrupted during the process. The steps are as follows:

1. Delete the node that failed to be scaled out.
2. Change the calico-related image repository url to a public repository.
3. Continue to scale out the node.

The command to change the calico-related image repository url to a public repository is as follows:
"${CALICO_TAG}" is the Calico version currently used in the cluster.

**Change calico-node Daemonset image repo**
```
kubectl -n kube-system set image daemonset/calico-node \
  calico-node=calico/node:${CALICO_TAG} \
  install-cni=calico/cni:${CALICO_TAG} \
  mount-bpffs=calico/node:${CALICO_TAG}

[예제]
kubectl -n kube-system set image daemonset/calico-node \
  calico-node=calico/node:v3.24.1 \
  install-cni=calico/cni:v3.24.1 \
  mount-bpffs=calico/node:v3.24.1
```

**Change calico-typha Deployment iamge repo**
```
kubectl -n kube-system set image deployment/calico-typha \
  calico-typha=calico/typha:${CALICO_TAG}

[Example]
kubectl -n kube-system set image deployment/calico-typha \
  calico-typha=calico/typha:v3.24.1
```

**calico-kube-controller Deployment iamge repo**
```
kubectl -n kube-system set image deployment/calico-kube-controllers \
  calico-kube-controllers=calico/kube-controllers:${CALICO_TAG}

[Example]
kubectl -n kube-system set image deployment/calico-kube-controllers \
  calico-kube-controllers=calico/kube-controllers:v3.24.1
```