# Practice Test - Service Networking

## Exercises

**What network range are the nodes in the cluster part of?**

> Run the command ip addr and look at the IP address assigned to the ens3 interfaces. Derive network range from that.

```bash
$ ip add show ens3
2: ens3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 02:42:ac:11:00:0d brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.13/16 brd 172.17.255.255 scope global ens3
       valid_lft forever preferred_lft forever
    inet6 fe80::42:acff:fe11:d/64 scope link
       valid_lft forever preferred_lft forever
```

**What is the range of IP addresses configured for PODs on this cluster?**

> The network is configured with weave. Check the weave pods logs using command kubectl logs <weave-pod-name> weave -n kube-system and look for ipalloc-range


```bash
$ kubectl get pods -n kube-system
$ kubectl logs weave-net-79pfk -c weave -n kube-system
INFO: 2020/07/21 09:49:00.317359 Command line options: map[conn-limit:200 datapath:datapath db-prefix:/weavedb/weave-net docker-api: expect-npc:true host-root:/host http-addr:127.0.0.1:6784 ipalloc-init:consensus=1 ipalloc-range:10.32.0.0/12 metrics-addr:0.0.0.0:6782 name:4e:0a:02:ba:02:64 nickname:node01 no-dns:true port:6783]
```

**What is the IP Range configured for the services within the cluster?**

> Inspect the setting on kube-api server by running on command cat /etc/kubernetes/manifests/kube-apiserver.yaml | grep cluster-ip-range

`cat /etc/kubernetes/manifests/kube-apiserver.yaml | grep cluster-ip-range`

```bash
- --service-cluster-ip-range=10.96.0.0/12
```

**How many kube-proxy pods are deployed in this cluster**

```bash
$ kubectl get pods -n kube-system --no-headers | grep kube-proxy | wc -l
```

**What type of proxy is the kube-proxy configured to use?**

```bash
$ kubectl describe pod kube-proxy-kpkm7 -n kube-system
$ kubectl logs kube-proxy-kpkm7 -n kube-system
W0721 09:48:23.991417       1 server_others.go:559] Unknown proxy mode "", assuming iptables proxy
```

**How does this Kubernetes cluster ensure that a kube-proxy pod runs on all nodes in the cluster?**

> Inspect the kube-proxy pods and try to identify how they are deployed

```yaml
Name:                 kube-proxy-kpkm7
Namespace:            kube-system
Priority:             2000001000
Priority Class Name:  system-node-critical
Node:                 node01/172.17.0.24
Start Time:           Tue, 21 Jul 2020 09:48:11 +0000
Labels:               controller-revision-hash=c8bb659c5
                      k8s-app=kube-proxy
                      pod-template-generation=1
Annotations:          <none>
Status:               Running
IP:                   172.17.0.24
IPs:
  IP:           172.17.0.24
Controlled By:  DaemonSet/kube-proxy
```