# Practice Test - Manual Scheduling

## Pod definition

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    name: nginx
spec:
  containers:
    - name: nginx
      image: nginx
      ports:
        - containerPort: 8080
  nodeName: node02
```

### Pod Binding Object

```yaml
apiVersion: v1
kind: Binding
metadata:
  name: nginx
target:
  apiVersion: v1
  kind: Node
  name: node02
```

## Practice files

* [nginx](nginx.yaml)

## Exercises

**A pod definition file nginx.yaml is given. Create a pod using the file.**

```bash
$ kubectl apply -f nginx.yaml
```
