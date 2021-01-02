# Practice Test - DaemonSets

## DaemonSet definition

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: monitoring-agent
spec:
  selector:
    matchLabels:
      app: monitoring-agent
  template:
    metadata:
      labels:
        app: monitoring-agent
    spec:
      containers:
        - name: monitoring-agent
          image: monitoring-agent
```

## Exercises

**How many DaemonSets are created in the cluster in all namespaces?**

```bash
$ kubectl get daemonsets --all-namespaces
```

**On how many nodes are the pods scheduled by the DaemonSet kube-proxy**

```bash
$ kubectl describe daemonset kube-proxy --namespace=kube-system
```

**What is the image used by the POD deployed by the kube-flannel-ds-amd64 DaemonSet?**

```bash
$ kubectl describe daemonset kube-flannel-ds-amd64 --namespace=kube-system
```
