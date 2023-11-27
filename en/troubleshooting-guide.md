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


### > Failed to pull image "k8s.gcr.io/pause:3.2" occurs in a closed network environment.

This problem occurs because NKS in a closed network environment cannot receive images from public registries. Images distributed by default, such as the "k8s.gcr.io/pause:3.2" image, are pulled from NHN Cloud's internal registry when creating a worker node. The list of images distributed by default when creating a cluster is as follows.

* kubernetesui/dashboard
* k8s.gcr.io/pause
* k8s.gcr.io/kube-proxy
* kubernetesui/dashboard
* kubernetesui/metrics-scraper
* quay.io/coreos/flannel
* quay.io/coreos/flannel-cni
* docker.io/calico/kube-controllers
* docker.io/calico/typha
* docker.io/calico/cni
* docker.io/calico/node
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
imageGCHighThresholdPercent : 85
imageGCLowThresholdPercent : 80
```

The workaround is as follows.
If the image pull fails, you can pull the image from the NHN Cloud internal registry using the command below. For NKS 1.24.3 version or higher, you must use nerdctl, not docker.
```
TARGET_IMAGE="image with failed to pull occurred"
INFRA_REGISTRY="harbor-kr1.cloud.toastoven.net/container_service/$(basename $TARGET_IMAGE)"
docker pull $INFRA_REGISTRY
docker tag $INFRA_REGISTRY $TARGET_IMAGE
docker rmi $INFRA_REGISTRY
```


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