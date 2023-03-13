---
layout: page
title: Keepalived
permalink: /k8s-ubuntu-keepalived/
---

When running the `kubectl` command, the CLI will connect to the kubernetes `control-plane` node specified in the kubernetes context (*usually located in the `~/.kube/config` file*). As we have three nodes, and all of them have the `control-plane` role we want to make sure that if one node is down the request will be sent to another node that is up and running.

For this we are going to use a **VIP** (Virtual IP). The VIP will be configured in all the nodes, but it will be active only on one of them - the functional node with the highest priority. The configuration is going to be handled by `keepalived`, a tool based on `VRRP` that will constantly check the health of the nodes and when the node that has the VIP active is not answering, one of the other nodes will rise the VIP on it's interface.

## Install keepalived

In Ubuntu 22.04 the `keepalived` is present in the repositories, so we can install it directly:

```
apt install -y keepalived
```

## Configure keepalived

Once it's installed, we add the configuration to each node:

```
cat <<EOF | tee -a /etc/keepalived/keepalived.conf
vrrp_instance VI_1 {
        state MASTER
        interface enp1s0
        virtual_router_id 51
        priority 255
        advert_int 1
        authentication {
              auth_type PASS
              auth_pass 12345
        }
        virtual_ipaddress {
              192.168.1.240/24
        }
}
EOF
```

> Make sure that the `interface` is corresponding to the external interface of your machines  

> `priority` should be different between the machines, and the `keepalived` service that is up and has the highest priority will activate the VIP  

## Enable and start keepalived

Once the service is configured on all the `control-plane` nodes, we can enable and start it

```
systemctl enable keepalived && systemctl start keepalived
```

## Check the functionality

To check the functionality of `keepalived` we can use `tcpdump`. Run the following command in one of the nodes:

```
tcpdump proto 112
```

The result should be similar to this:

```
root@node01:~# tcpdump proto 112
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on enp1s0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
09:04:59.552340 IP 192.168.1.251 > vrrp.mcast.net: VRRPv2, Advertisement, vrid 51, prio 255, authtype simple, intvl 1s, length 20
09:05:00.552571 IP 192.168.1.251 > vrrp.mcast.net: VRRPv2, Advertisement, vrid 51, prio 255, authtype simple, intvl 1s, length 20
09:05:01.552793 IP 192.168.1.251 > vrrp.mcast.net: VRRPv2, Advertisement, vrid 51, prio 255, authtype simple, intvl 1s, length 20
3 packets captured
3 packets received by filter
0 packets dropped by kernel
root@node01:~#
```

Here we can see that the node that currently has the VIP active is `192.168.1.251` (cp01).

To move the test a bit further, we can reboot the `cp01` node, and the advertisement should start on the next node according to the priorities.   
