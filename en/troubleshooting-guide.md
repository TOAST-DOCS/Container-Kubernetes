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
NKS provides the feature to create a group of worker nodes based on your custom image. You can use the NKS custom image feature to create an image with kernel object accounting disabled for memory cgroups and utilize it when creating a cluster. For more information about the feature, see [](/Container/NKS/ko/user-guide/#_25)Use Custom Image as Worker Image[](/Container/NKS/ko/user-guide/#_25).

1. While creating the image template, enter the following in the user script.
```
#!/bin/bash
args="cgroup.memory=nokmem"
grub_file="/etc/default/grub"
sudo sed -i "s/GRUB_CMDLINE_LINUX=\"\(.*\)\"/GRUB_CMDLINE_LINUX=\"\1 $args\"/" "$grub_file"

sudo grub2-mkconfig -o /boot/grub2/grub.cfg
```


### > calico-typha, calico-kube-controller image pull failed error and calico-node pod is not working properly, causing cluster network failure.
This issue occurs when a Calico image is removed by Kubelet's Garbage Collection and then becomes unavailable for re-download due to an invalid container image repository address. The Kubelet provides the Garbage Collection feature to clean up unused container images to manage disk usage on nodes. You can find more information about this feature in the [Garbage Collection](https://kubernetes.io/docs/concepts/architecture/garbage-collection/) documentation. For NKS, the Kubelet's imageGCHighThresholdPercent and imageGCLowThresholdPercent are set to the default values.
```
imageGCHighThresholdPercent=85: Always run image Garbage Collection to remove unused images when disk utilization is greater than 85%.
imageGCLowThresholdPercent=80: Do not run image Garbage Collection when disk utilization is 80% or less.
```

#### What to do if you experience symptoms
Clusters created before May 2024 might experience the issue. The state of the calico-kube-controller or calico-typha pod remains **ImagePullBackOff** or **ErrImagePull** when checking with the `kubectl get all -n kube-system` command. The calico-node Pod appears to be **Running**, but its Ready entry appears to be **0/1**. The calico-node Pod is deployed as a daemonset, so it is not subject to image deletion by the kubelet's GC. However, it may not behave normally due to communication failure with calico-typha, causing the above issue. For clusters created after May 2024, the calico image repository settings have been changed so that this issue does not occur. 

#### Workaround
You can run a script to change the CALICO-specific image repository URL to a public repository to work around this. However, this workaround is only applicable to **clusters with internet connectivity** and **may temporarily disconnect cluster pods from networking** while running the script, so proceed with caution. Before running the script, you must ensure that all worker nodes are in the 'Ready' state. The troubleshooting script is shown below.

```
#!/bin/bash
tag="v3.24.1"
namespace="kube-system"
calico_cni_image="calico/cni:$tag"
calico_node_image="calico/node:$tag"
calico_typha_image="calico/typha:$tag"
calico_kube_controllers_image="calico/kube-controllers:$tag"
images=($calico_cni_image $calico_node_image $calico_typha_image $calico_kube_controllers_image)
default_timeout=4
declare -a failed_updates
check_image_match() {
    local resource_type=$1
    local resource_name=$2
    local namespace=$3
    local expected_image=$4
    current_image=$(kubectl get $resource_type $resource_name -n $namespace -o jsonpath="{.spec.template.spec.containers[*].image}")
    echo "Current $resource_type $resource_name image: $current_image"
    if [ "$current_image" == "$expected_image" ]; then
        echo "The image repo is not a target because it does not match the $expected_image"
        exit 1
    fi
}
pull_and_verify_image() {
    local node=$1
    local image=$2
    local pod_name=$(kubectl debug node/"$node" --image="$image" --namespace=$namespace -- sleep 1 --quiet | awk '{print $4}')
    echo "Created pod $pod_name in $namespace namespace"
    local start_time=$(date +%s)
    local timeout_seconds=360
    while :; do
        local current_time=$(date +%s)
        local elapsed_time=$((current_time - start_time))
        if [ $elapsed_time -ge $timeout_seconds ]; then
            echo "Timeout reached: $timeout_seconds seconds for node $node, image $image."
            echo "Exiting due to timeout failure."
            kubectl delete pod $pod_name -n $namespace >/dev/null 2>&1
            exit 1
        fi
        local container_state=$(kubectl get pod $pod_name -n $namespace -o jsonpath='{.status.containerStatuses[0].state}' 2>/dev/null)
        if echo "$container_state" | grep -q "terminated"; then
            echo "Container has terminated, deleting pod $pod_name from $namespace namespace"
            kubectl delete pod $pod_name -n $namespace >/dev/null 2>&1
            break
        elif echo "$container_state" | grep -q "running"; then
            echo "Container is running, deleting pod $pod_name from $namespace namespace"
            kubectl delete pod $pod_name -n $namespace >/dev/null 2>&1
            break
        elif echo "$container_state" | grep -q "waiting"; then
            local reason=$(kubectl get pod $pod_name -n $namespace -o jsonpath='{.status.containerStatuses[0].state.waiting.reason}' 2>/dev/null)
            if [[ "$reason" == "ImagePullBackOff" || "$reason" == "ErrImagePull" ]]; then
                echo "Failed to pull image $image on node $node due to $reason. Exiting."
                kubectl delete pod $pod_name -n $namespace >/dev/null 2>&1
                exit 1
            fi
        fi
        sleep 5
    done
}
update_image() {
    local resource_type=$1
    local resource_name=$2
    local timeout=$3
    shift 3
    echo ""
    echo "Updating $resource_type $resource_name with timeout ${timeout} minutes..."
    local update_command="kubectl set image $resource_type/$resource_name"
    for arg in "$@"; do
        local container_name=$(echo $arg | cut -d'=' -f1)
        local image_name=$(echo $arg | cut -d'=' -f2)
        update_command+=" $container_name=$image_name"
    done
    update_command+=" -n $namespace"
    if ! eval $update_command; then
        echo "Failed to update $resource_type $resource_name"
        failed_updates+=("$resource_type/$resource_name")
        return
    fi
    check_rollout_status $resource_type $resource_name $timeout
    return $?
}
check_rollout_status() {
    local resource_type=$1
    local resource_name=$2
    local timeout=$3
    echo "Checking rollout status for $resource_type $resource_name..."
    if ! kubectl rollout status $resource_type $resource_name -n $namespace --timeout=${timeout}m; then
        echo "Rollout status check failed for $resource_type $resource_name"
        failed_updates+=("$resource_type/$resource_name")
        return 1
    fi
    echo "$resource_type $resource_name updated successfully."
    return 0
}
delete_old_pods() {
    local resource_type=$1
    local resource_name=$2
    local old_pods=$3
    for pod in $old_pods; do
        if kubectl get pods $pod -n $namespace &> /dev/null; then
            echo "Deleting old pod: $pod"
            kubectl delete pod $pod -n $namespace
        fi
    done
}
update_calico_node() {
    local resource_type="daemonset"
    local resource_name="calico-node"
    local timeout=$(( $(kubectl get nodes --no-headers | wc -l) * 4 ))
    update_image $resource_type $resource_name $timeout "$resource_name=$calico_node_image" "install-cni=$calico_cni_image" "mount-bpffs=$calico_node_image"
}
update_calico_kube_controller() {
    local resource_type="deployment"
    local resource_name="calico-kube-controllers"
    update_image $resource_type $resource_name $default_timeout "$resource_name=$calico_kube_controllers_image"
}
update_calico_typha_image() {
    local resource_type="deployment"
    local resource_name="calico-typha"
    local old_pods=$(kubectl get pods -n $namespace -l k8s-app="$resource_name" -o jsonpath="{.items[*].metadata.name}")
    
    if ! update_image $resource_type $resource_name $default_timeout "$resource_name=$calico_typha_image"; then
        delete_old_pods $resource_type $resource_name $old_pods
    fi
}
check_image_match "daemonset" "calico-node" $namespace $calico_node_image
check_image_match "deployment" "calico-kube-controllers" $namespace $calico_kube_controllers_image
check_image_match "deployment" "calico-typha" $namespace $calico_typha_image
for node in $(kubectl get nodes --no-headers | awk '{print $1}'); do
    echo ""
    echo "Worker node : [$node] calico images pull start!!"
    for image in "${images[@]}"; do
        echo "Pulling $image"
        pull_and_verify_image $node $image
    done
done
echo "The calico image pull has been completed!"
echo ""
update_calico_node
update_calico_kube_controller
update_calico_typha_image
echo ""
if [ ${#failed_updates[@]} -eq 0 ]; then
    echo "Calico images update completed!"
    exit 0
else
    echo "[WARNING] Please check to resources status:"
    for resource in "${failed_updates[@]}"; do
        echo "- $resource"
    done
    exit 1
fi
```

The script process is as follows
1. Pull calico related images to all worker nodes.
2. Run a rolling update that changes the calico-node daemonset image repository.
3. Run a rolling update that makes changes to the calico-kube-controllers deployment image repository.
4. Run a rolling update that makes changes to the calico-typha deployment image repository.

This script can be run in any environment where kubectl commands are available. Here's how to run it
* vim calico_manifest_image_change.sh
* Save body script content
* Store the path to the kubeconfig configuration file in the KUBECONFIG environment variable
* chmod 755 calico_manifest_image_change.sh
* ./calico_manifest_image_change.sh


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