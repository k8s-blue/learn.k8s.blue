---
layout: page
title: Install helm
permalink: /k8s-helm-install/
---

`Helm` for kubernetes is similar to a package manager, except that it deploys services and containerized applications instead of installing local services and files.

Though it is not mandatory to have it, it comes in handy for managing deployments.

For Ubuntu we can use the package manager to install it. First, add the signing keys:

```
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
```

Add the helm repository:

```
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
```

Refresh the package list, and install helm:

```
apt update
apt install helm
```

As a quick-and-dirty alternative, helm can be installed directly using a shell script:

```
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```
