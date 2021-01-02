# Practice Test - Explore Kubernetes Environment

## Exercises

**What is the network interface configured for cluster connectivity on the master node?**

> Run the ip link command and identify the interface

`ens3`

```bash
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: ens3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 02:42:ac:11:00:19 brd ff:ff:ff:ff:ff:ff
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN mode DEFAULT group default
    link/ether 02:42:73:de:70:0b brd ff:ff:ff:ff:ff:ff
4: flannel.1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UNKNOWN mode DEFAULT group default
    link/ether 86:81:30:ff:98:b0 brd ff:ff:ff:ff:ff:ff
5: cni0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UP mode DEFAULT group default qlen 1000
    link/ether 5e:9a:51:0f:44:80 brd ff:ff:ff:ff:ff:ff
6: vethc2db0f9f@if3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue master cni0 state UP mode DEFAULT group default
    link/ether 96:d9:d7:05:47:fd brd ff:ff:ff:ff:ff:ff link-netnsid 0
7: veth1fc65a80@if3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue master cni0 state UP mode DEFAULT group default
    link/ether 3e:91:5c:74:b8:f5 brd ff:ff:ff:ff:ff:ff link-netnsid 1
```

**What is the IP address assigned to the master node on this interface?**

> Run the command kubectl get nodes -o wide and look for IP assigned to master node

```bash
master   Ready   master   28m   v1.18.0   172.17.0.25   <none>   Ubuntu 18.04.4 LTS   4.15.0-101-generic   docker://19.3.6
node01   Ready   <none>   28m   v1.18.0   172.17.0.28   <none>   Ubuntu 18.04.4 LTS   4.15.0-101-generic   docker://19.3.6
```

**What is the MAC address of the interface on the master node?**

> Run the command ip link show ens3

`02:42:ac:11:00:19`

```bash2: ens3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 02:42:ac:11:00:19 brd ff:ff:ff:ff:ff:ff
```

**What is the IP address assigned to node01?**

`172.17.0.28`

**What is the MAC address assigned to node01?**

> Run the command arp node01 on the master node

```bash
Address                  HWtype  HWaddress           Flags Mask            Iface
node01                   ether   02:42:ac:11:00:1c   C                     ens3
```

**We use Docker as our container runtime. What is the interface/bridge created by Docker on this host?**

> Run the command ip link and look for a bridge interface created by docker

`ip link show docker0`

```bash
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN mode DEFAULT group default
    link/ether 02:42:73:de:70:0b brd ff:ff:ff:ff:ff:ff
```

**If you were to ping google from the master node, which route does it take?**

> What is the IP address of the Default Gateway?

```bash
$ ip route show default
default via 172.17.0.1 dev ens3
```

**What is the port the kube-scheduler is listening on in the master node?**

> Use the command netstat -nplt | grep kube-scheduler

```bash
tcp        0      0 127.0.0.1:10259         0.0.0.0:*               LISTEN      2730/kube-scheduler
tcp6       0      0 :::10251                :::*                    LISTEN      2730/kube-scheduler
```

**Notice that ETCD is listening on two ports. Which of these have more client connections established?**

> Run the command netstat -anp | grep etcd

`2379`

Correct! That's because 2379 is the port of ETCD to which all control plane components connect to. 2380 is only for etcd peer-to-peer connectivity. When you have multiple master nodes. In this case we don't.
