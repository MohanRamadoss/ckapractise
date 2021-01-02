# Practice Test - Readiness and Liveness Probes

## Exercises

**Update the newly created pod 'simple-webapp-2' with a readinessProbe using the given spec**

>  Pod Name: simple-webapp-2, Image Name: kodekloud/webapp-delayed-start, Readiness Probe: httpGet, Http Probe: /ready, Http Port: 8080

```bash
$ kubectl get pod simple-webapp-2 -o yaml > simple-webapp-2.yaml
# Edit YAML file
```

```yaml
spec:
  containers:
  - env:
    - name: APP_START_DELAY
      value: "80"
    image: kodekloud/webapp-delayed-start
    imagePullPolicy: Always
    name: simple-webapp
    ports:
    - containerPort: 8080
      protocol: TCP
    readinessProbe:
      httpGet:
        path: /ready
        port: 8080
```

```bash
# Delete and recreate the pod
$ kubectl delete -f simple-webapp-2.yaml
$ kubectl create -f simple-webapp-2.yaml
```

**Update both the pods with a livenessProbe using the given spec**

> Pod Name: simple-webapp-1, Pod Name: simple-webapp-2, Liveness Probe: httpGet, Http Probe: /live, Http Port: 8080,  Initial Delay: 80, Period Seconds: 1

```bash
$ kubectl get pod simple-webapp-1 -o yaml > simple-webapp-1.yaml
# Edit YAML file
```

```yaml
livenessProbe:
  httpGet:
    path: /live
    port: 8080
  initialDelaySeconds: 80
  periodSeconds: 1
```

```bash
# Delete and recreate the PODs
$ kubectl delete -f simple-webapp-1.yaml
$ kubectl create -f simple-webapp-1.yaml
$ kubectl delete -f simple-webapp-2.yaml
$ kubectl create -f simple-webapp-2.yaml
```
