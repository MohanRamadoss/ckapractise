# Practice Test - Multi Container PODs

## Pod definition

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: simple-webapp
  labels:
    name: simple-webapp
spec:
  containers:
  - name: simple-webapp
    image: simple-webapp
    ports:
      - containerPort : 8080
  
  - name: log-agent
    image: log-agent
```

## Exercises

**Create a multi-container pod with 2 containers.**

> Name: yellow, Container 1 Name: lemon, Container 1 Image: busybox, Container 2 Name: gold, Container 2 Image: redis

```bash
$ kubectl create -f yellow-pod.yaml
```

**The 'app'lication outputs logs to the file /log/app.log. View the logs and try to identify the user having issues with Login.**

```bash
$ kubectl -n elastic-stack exec -it app cat /log/app.log
```
**Edit the pod to add a sidecar container to send logs to ElasticSearch. Mount the log volume to the sidecar container..**

> Name: app, Container Name: sidecar,Container Image: kodekloud/filebeat-configured, Volume Mount: log-volume, Mount Path: /var/log/event-simulator/, Existing Container Name: app, Existing Container Image: kodekloud/event-simulator

```bash
$ kubectl create -f yellow-pod.yaml
```
