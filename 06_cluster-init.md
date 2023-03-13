---
layout: page
title: Cluster initialization
permalink: /k8s-cluster-initialization/
---

Now we need to use one of the `control-plane` nodes to initialize the cluster.

First, we create the initialization file:

```
cat <<EOF | sudo tee ~/kubeadm-config.yaml
---
kind: ClusterConfiguration
apiVersion: kubeadm.k8s.io/v1beta3
kubernetesVersion: stable
controlPlaneEndpoint: "cpe.k8s.blue:6443"
clusterName: "k8s-blue"
networking:
  dnsDomain: k8s.blue
  podSubnet: 10.233.64.0/18
  serviceSubnet: 10.233.0.0/18
apiServer:
  extraArgs:
    authorization-mode: "Node,RBAC"
    bind-address: 0.0.0.0
---
kind: KubeletConfiguration
apiVersion: kubelet.config.k8s.io/v1beta1
cgroupDriver: systemd
...

EOF
```

Now we can initalize the cluster using the `kubeadm-config.yaml` file:

```
kubeadm init --config kubeadm-config.yaml
```

To avoid copying around the k8s certificates, we can temporarily (2h) upload them to the cluster. The secrets will be saved in a secret named `kubeadm-certs` and after running the command we are going to receive a certificate key that we'll use to join the other nodes:

```
kubeadm init phase upload-certs --upload-certs
```

The cluster should be functional at this point, though the deployment is not yet completed. Now we are going to save the `/etc/kubernetes/admin.conf` file in the home directory as `~/.kube/config`. This is the default path where the `kubectl` is looking for the config files:


```
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
chown $(id -u):$(id -g) $HOME/.kube/config
```

Now we should be able to run some basic commands like:
  * `kubectl version --short` - to check the cluster version
  * `kubectl get nodes` - to see the node details
  * `kubectl get pods -A` - to see all the pods deployed

