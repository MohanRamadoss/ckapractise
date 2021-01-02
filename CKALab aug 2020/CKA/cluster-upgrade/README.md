# Practice Test - Cluster Upgrade

## Exercises

**What is the latest stable version available for upgrade?**

```bash
$ kubeadm upgrade plan
```

**We will be upgrading the master node first. Drain the master node of workloads and mark it UnSchedulable**

```bash
$ kubectl drain master --ignore-daemonsets
```

**Upgrade the master components to exact version v1.18.0**

> Upgrade kubeadm tool (if not already), then the master components, and finally the kubelet. Practice referring to the kubernetes documentation page. Note: While upgrading kubelet, if you hit dependency issue while running the apt-get upgrade kubelet command, use the apt install kubelet=1.18.0-00 command instead

```bash
$ apt install kubeadm=1.18.0-00
$ kubeadm upgrade apply v1.18.0
$ apt install kubelet=1.18.0-00
```

**Mark the master node as "Schedulable" again**

```bash
$ kubectl uncordon master
```

**Next is the worker node. Drain the worker node of the workloads and mark it UnSchedulable**

```bash
$ kubectl drain node01 --ignore-daemonsets
```

**Upgrade the worker node to the exact version v1.18.0**

```bash
$ ssh roo@node01
$ apt install kubeadm=1.18.0-00
$ kubeadm upgrade node
$ apt install kubelet=1.18.0-00
```

**Remove the restriction and mark the worker node as schedulable again.**

```bash
$ kubectl uncordon node01
```
