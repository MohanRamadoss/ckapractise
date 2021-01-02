# Practice Test - Namespaces

## Namespace definition

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: team1
```

## Exercises

**How many Namespaces exist on the system?**

```bash
$ kubectl get namespace
```

**How many pods exist in the 'research' namespace?**

```bash
kubectl get pods --namespace=research
```

**Create a POD in the 'finance' namespace.**

> Name: redis, Image Name: redis

```bash
$ kubectl run redis --image=redis --namespace=finance
```

**Which namespace has the 'blue' pod in it?**

```bash
$ kubectl get pods --all-namespaces
```

**What DNS name should the Blue application use to access the database 'db-service' in the 'dev' namespace**

```bash
$ db-service.dev.svc.cluster.local
```
