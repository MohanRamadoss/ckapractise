# Practice Test - ReplicaSets

## ReplicaSet definition

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: myapp-replicaset
  labels:
    app: myapp
    tier: front-end
spec:
  replicas: 3
  template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
        tier: front-end
    spec:
      containers:
        - name: nginx-container
          image: nginx
  selector:
    matchLabels:
      tier: front-end
```

## Practice files

* [replicaset-definition-1.yaml](replicaset-definition-1.yaml)
* [replicaset-definition-2.yaml](replicaset-definition-2.yaml)

## Exercises

**How many ReplicaSets exist in the current(default) namespace**

```bash
$ kubectl get replicaset
```

**Create a ReplicaSet using the '[replicaset-definition-1.yaml](replicaset-definition-1.yaml)' file. There is an issue with the file, so fix it.**

```bash
# The value for 'apiVersion' is incorrect. Find the correct apiVersion for ReplicaSet.
$ kubectl explain replicaset | grep VERSION
$ kubectl create -f replicaset-definition-1.yaml
```

**Fix the issue in the '[replicaset-definition-2.yaml](replicaset-definition-2.yaml)' file and create a ReplicaSet using it.**

```bash
# The values for labels on lines 9 and 13 should match.
$ kubectl create -f replicaset-definition-2.yaml
```

**Scale the ReplicaSet to 5 PODs**

```bash
$ kubectl scale --replicas=5 rs/replicaset-1
```
