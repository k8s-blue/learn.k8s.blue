---
layout: page
title: OS Setup
permalink: /os-setup/
---

Let's configure the machines to be used as cluster nodes.

## Set versions as environment variables

This step is added for simplicity. The versions must be set as environment variables, so make sure you run the `export`s on all the nodes every time you open a shell instance. Optional, you can add them to the `.bashrc` file to be set automatically when you open the terminal.

```
export OS=xUbuntu_22.04
export CRIO_VERSION=1.26
export K8S_VERSION=1.26.1-00
```

## Disable swap

For now, kubernetes doesn't support `swap` partitions so we should disable it.

```
swapoff -a && sed -i '/swap/d' /etc/fstab
```

## Load and configure kernel modules

The `br_netfilter` and `overlay` modules should be present, and loaded at boot time.

Load the modules:

```
modprobe br_netfilter
modprobe overlay
```

Ensure that the modules are loaded at boot time:

```
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
overlay
EOF
```

Configure `br_netfilter` to direct bridged packets to iptables, and enable ip forwarding:

```
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

sysctl --system
```

## Install packages

Install the dependencies:

```
apt install -y software-properties-common \
               apt-transport-https        \
               ca-certificates            \
               curl                       \
               dialog                     \
               git                        \
               tcpdump                    \
               wget                       \
               apt-utils
```

Update system packages:

```
apt update && apt -y upgrade && apt -y autoremove
```
