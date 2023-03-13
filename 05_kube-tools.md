---
layout: page
title: Kubernetes binaries
permalink: /k8s-kubeadm-kubelet-kubectl/
---

Now we need to install the binaries required for the kubernetes deployment:
  * `kubeadm` - CLI tool for initializing kubernetes clusters
  * `kubectl` - CLI tool for interacting with kubernetes
  * `kubelet` - kubernetes node agent service that interacts with the API server and the local container runtime

First, download the Google Cloud public signing key:

```
curl -fsSLo /etc/apt/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
```

Next, add the kubernetes repositories:

```
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" >> /etc/apt/sources.list.d/kubernetes.list
```

Now install the packages, and mark them as not upgradable:

```
apt update
apt install -y kubelet=${K8S_VERSION} kubeadm=${K8S_VERSION} kubectl=${K8S_VERSION}
apt-mark hold kubelet kubeadm kubectl
```

As an extra step, we can fetch the images - if not, they will be fetch automatically when we initialize the cluster. This is a good step to keep in mind if your cluster is not going to have internet access after the deployment.

```
kubeadm config images pull
```
