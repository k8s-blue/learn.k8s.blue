---
layout: page
title: Install CRI-O
permalink: /install-crio/
---

On each kubernetes cluster we need a **container runtime** which is responsible for running the containers. There are multiple container runtimes available, but in this case we are going to use **CRI-O**.

If you didn't do it already, set the versions to be used in the current shell session as environment variables:

```
export OS=xUbuntu_22.04
export CRIO_VERSION=1.26
export K8S_VERSION=1.26.1-00
```

Download the signing keys for the `CRI-O` packages:

```
mkdir -p /usr/share/keyrings

curl -L https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/$OS/Release.key | gpg --dearmor --yes -o /usr/share/keyrings/libcontainers-archive-keyring.gpg

curl -L https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/$VERSION/$OS/Release.key | gpg --dearmor --yes -o /usr/share/keyrings/libcontainers-crio-archive-keyring.gpg
```

Add the CRI-O apt repositories:

```
echo "deb [signed-by=/usr/share/keyrings/libcontainers-archive-keyring.gpg] https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/$OS/ /" > /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list

echo "deb [signed-by=/usr/share/keyrings/libcontainers-crio-archive-keyring.gpg] https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/$VERSION/$OS/ /" > /etc/apt/sources.list.d/devel:kubic:libcontainers:stable:cri-o:$VERSION.list
```

Now you can install the packages:

```
apt update && apt -y install cri-o cri-o-runc
```

When everything is done, enable and start CRI-O:

```
systemctl enable crio && systemctl start crio
```

Now that it's installed, we can check and see if it's functioning properly

```
root@node01:~# systemctl status crio
● crio.service - Container Runtime Interface for OCI (CRI-O)
     Loaded: loaded (/lib/systemd/system/crio.service; enabled; vendor preset: enabled)
     Active: active (running) since Mon 2023-03-13 15:03:14 UTC; 1s ago
       Docs: https://github.com/cri-o/cri-o
   Main PID: 6195 (crio)
      Tasks: 9
     Memory: 11.8M
        CPU: 64ms
     CGroup: /system.slice/crio.service
             └─6195 /usr/bin/crio

Mar 13 15:03:14 node01 crio[6195]: time="2023-03-13 15:03:14.056202625Z" level=info msg="RDT not available in the host system"
Mar 13 15:03:14 node01 crio[6195]: time="2023-03-13 15:03:14.057450692Z" level=info msg="Conmon does support the --sync option"
Mar 13 15:03:14 node01 crio[6195]: time="2023-03-13 15:03:14.057468300Z" level=info msg="Conmon does support the --log-global-size-max option"
Mar 13 15:03:14 node01 crio[6195]: time="2023-03-13 15:03:14.058668585Z" level=info msg="Found CNI network crio (type=bridge) at /etc/cni/net.d/100-crio-bridge.conflist"
Mar 13 15:03:14 node01 crio[6195]: time="2023-03-13 15:03:14.058710667Z" level=info msg="Updated default CNI network name to crio"
Mar 13 15:03:14 node01 crio[6195]: time="2023-03-13 15:03:14.061643363Z" level=info msg="Starting seccomp notifier watcher"
Mar 13 15:03:14 node01 crio[6195]: time="2023-03-13 15:03:14.061687091Z" level=info msg="Create NRI interface"
Mar 13 15:03:14 node01 crio[6195]: time="2023-03-13 15:03:14.061692361Z" level=info msg="NRI interface is disabled in the configuration."
Mar 13 15:03:14 node01 systemd[1]: Started Container Runtime Interface for OCI (CRI-O).
root@node01:~#
```
