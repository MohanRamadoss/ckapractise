# Practice Test - Taints And Tolerations

## Taints - Node

```bash
$ kubectl taint nodes node name key=value:taint-effect
# Taint effect can be one of: NoSchedule | PreferNoSchedule | NoExecute
$ kubectl taint nodes node1 app=myapp:NoSchedule
```

## Tolerations - PODs

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
    - name: nginx-container
      image: nginx
  tolerations:
    - key: "app"
      operator: "Equal"
      value: blue
      effect: NoSchedule
```

## Practice files

* [bee-pod.yaml](bee-pod.yaml)

## Exercises

**How many Nodes exist on the system?**

```bash
$ kubectl get nodes
```

**Do any taints exist on node01?**

```bash
$ kubectl describe node node01
```

**Create a taint on node01 with key of 'spray', value of 'mortein' and effect of 'NoSchedule'**

> Key = spray, Value = mortein, Effect = NoSchedule 

```bash
$ kubectl taint nodes node01 spray=mortein:NoSchedule
```

**Create a new pod with the NGINX image, and Pod name as 'mosquito'**

```bash
$ kubectl run mosquito --image=nginx
```

**Create another pod named 'bee' with the NGINX image, which has a toleration set to the taint Mortein**

```bash
$ kubectl run bee --image=nginx --dry-run=client -o yaml > bee-pod.yaml
# Edit yaml file accoringly
$ kubectl apply -f bee-pod.yaml
```

**Remove the taint on master, which currently has the taint effect of NoSchedule**

```bash
$ kubectl taint nodes master node-role.kubernetes.io/master:NoSchedule-
```