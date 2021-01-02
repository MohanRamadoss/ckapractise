# Practice Test - Environment Variables

## ConfigMap definition

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_COLOR: blue
  APP_MODE: prod
```

## Exercises

**What is the environment variable name set on the container in the pod?**

```bash
$ kubectl describe pod webapp-color
```

**How many ConfigMaps exist in the environment?**

```bash
$ kubectl get configmaps
```

**Identify the database host from the config map 'db-config'**

```bash
$ kubectl describe configmap db-config
```

**Create a new ConfigMap for the 'webapp-color' POD. Use the spec given on the right.**

> ConfigName Name: webapp-config-map, Data: APP_COLOR=darkblue

```bash
$ kubectl create configmap webapp-config-map --from-literal=APP_COLOR=darkblue
```

**Update the environment variable on the POD use the newly created ConfigMap**

```bash
$ kubectl create -f webapp-color.yaml
```
