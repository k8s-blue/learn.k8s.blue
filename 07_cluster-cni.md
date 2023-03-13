---
layout: page
title: Cluster CNI
permalink: /k8s-cni-flannel-weave/
---

For the services and pods to communicate with each other we need to use a Container Network Interface (CNI). This will define an intra-cluster network on top of the physical network.

There are multiple CNIs available, two of them being `flannel` and `weave`.

## Install flannel

Probably the lightest CNI available is `flannel`. It is one of the first CNIs developed, so it's code is mature and stable - though not very rich in features.

```
kubectl apply -f https://github.com/coreos/flannel/raw/master/Documentation/kube-flannel.yml
```

## Install Weave Net

Weave is a CNI developed by Weaveworks. A big plus is that it brings advanced features, like `NetworkPolicy`:

```
kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml
```

You can chose any CNI that makes sense for your environment, but usually for a home lab there's not much difference between them.
