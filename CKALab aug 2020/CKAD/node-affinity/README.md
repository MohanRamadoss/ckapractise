# Practice Test - Node Affinity

## Node Selectors

```yaml
apiVersion: v1
kind : Pod
metadata:
  name: myapp-pod
spec:
  containers:
    - name: data-processor
      image: data-processor
  nodeSelector:
    size: Large
```

## Practice files

* [blue-deployment.yaml](blue-deployment.yaml)
* [red-deployment.yaml](red-deployment.yaml)

## Exercises

**How many Labels exist on node node01?**

```bash
$ kubectl describe node node01
```

**Apply a label color=blue to node node01**

```bash
$ kubectl label node node01 color=blue
```

**Create a new deployment named 'blue' with the NGINX image and 6 replicas**

```bash
$ kubectl create deployment blue --image=nginx --dry-run=client -o yaml > blue-deployment.yaml
# Edit replicas -> 6
$ kubectl apply -f blue-deployment.yaml
```

**Set Node Affinity to the deployment to place the PODs on node01 only**

```bash
# See file blue-deployment.yaml
$ kubectl apply -f blue-deployment.yaml
```

**Create a new deployment named 'red' with the NGINX image and 3 replicas, and ensure it gets placed on the master node only.**

```bash
# See file red-deployment.yaml
$ kubectl apply -f red-deployment.yaml
```
