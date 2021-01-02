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

**Identify the number of containers running in the 'red' pod.**

`$ kubectl describe pod red`

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
$ kubectl create -f app.yaml
```

**Forwarding Port Traffic with an Ambassador Container**

This service has already been packaged into a container image, but there is one special requirement: The legacy app is hard-coded to only serve content on port 8775, but the team wants to be able to access the service using the standard port 80. Your task is to build a Kubernetes pod that runs this legacy container and uses the ambassador design pattern to expose access to the service on port 80.

This setup will need to meet the following specifications:

* The pod should have the name fruit-service.
* The fruit-service pod should have a container that runs the legacy fruit service image: linuxacademycontent/legacy-fruit-service:1.
* The fruit-service pod should have an ambassador container that runs the haproxy:1.7 image and proxies incoming traffic on port 80 to the legacy service on port 8775 (the HAProxy configuration for this is provided below).
* Port 80 should be exposed as a containerPort. Note that you do not need to expose port 8775.
* The HAProxy configuration should be stored in a ConfigMap called fruit-service-ambassador-config.
* The HAProxy config should be provided to the ambassador container using a volume mount that places the data from the ConfigMap in a file at /usr/local/etc/haproxy/haproxy.cfg.
* haproxy.cfg should contain the following configuration data:

```bash
global
    daemon
    maxconn 256

defaults
    mode http
    timeout connect 5000ms
    timeout client 50000ms
    timeout server 50000ms

listen http-in
    bind *:80
    server server1 127.0.0.1:8775 maxconn 32
```

```bash
# Create a haproxy config map
$ kubectl create configmap fruit-service-ambassador-config -o yaml -dry-run=client > fruit-service-ambassador-config.yaml
# Edit YAML file
```

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: fruit-service-ambassador-config
data:
  haproxy.cfg: |-
    global
        daemon
        maxconn 256

    defaults
        mode http
        timeout connect 5000ms
        timeout client 50000ms
        timeout server 50000ms

    listen http-in
        bind *:80
        server server1 127.0.0.1:8775 maxconn 32
```

```bash
# Create a multi-container pod which provides access to the legacy service
$ kubectl run fruit-service --image=linuxacademycontent/legacy-fruit-service:1 -o yaml --dry-run=client > fruit-service.yaml
# Edit YAML file
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: fruit-service
  name: fruit-service
spec:
  containers:
  - image: linuxacademycontent/legacy-fruit-service:1
    name: legacy-fruit-service
  - image: haproxy:1.7
    name: haproxy-ambasador
    ports:
    - containerPort: 80
    volumeMounts:
    - name: config-volume
      mountPath: "/usr/local/etc/haproxy"
  volumes:
  - name: config-volume
    configMap:
      name: fruit-service-ambassador-config
```

```bash
# Test the CURL via busybox or WGET
kubectl run -i --tty busybox --image=busybox:1.28 -- sh
/ wget -O- http://podIP:80
```

busybox.yml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: busybox
spec:
  containers:
  - name: myapp-container
    image: radial/busyboxplus:curl
    command: ['sh', '-c', 'while true; do sleep 3600; done']
```
