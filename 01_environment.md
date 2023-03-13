---
layout: page
title: Home lab environment
permalink: /k8s-home-lab-environment/
---

Before starting, we need to know what kind of machines we are going to use, and what network configuration we have.

We are going to install `Kubernetes v1.26`, and use `CRI-O 1.26` as the Container Runtime.

The name of the cluster is `k8s.blue`.



## Kubernetes nodes

In this kubernetes cluster setup we are going to use three identical virtual machines (*physical should be fine also*) with the following characteristics:

| Operating System | Ubuntu 22.04 |
| Number of vCPU   | 4            |
| RAM              | 8GB          |
| Disk space       | 100GB        |
| Internal NICs    | 1            |
| External NICs    | 1            |

The internal NICs are going to be used for nodes and pods communication. The external NICs are going to be used for internet access and exposing the services.

## Network configuration

The following IP addresses should be configured on the nodes

| Hostname      | External IP   | Internal IP   | 
| cp01.k8s.blue | 192.168.1.201 | 192.168.2.201 |
| cp02.k8s.blue | 192.168.1.202 | 192.168.2.202 |
| cp03.k8s.blue | 192.168.1.203 | 192.168.2.203 |

All three nodes will have the roles of both `control-plane` and `worker` nodes.

> The `InternalIP` refers to the management network, which is used only for the intra-cluster communication. Ideally, this is going to be an isolated network.

To make sure that we can access the cluster even if one of the `control-plane` nodes is down, we also need a `VIP` (*handled by keepalived*) which will point to a functional node.

| Hostname     | VIP           |
| cpe.k8s.blue | 192.168.1.240 |

> The DNS records are already configured to use the private IP addresses mentioned, so you can use them for your own setup if your network is configured with the same subnet.
