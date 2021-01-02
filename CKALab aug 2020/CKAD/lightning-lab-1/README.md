# Lightning Lab - 1

## Exercise 01

Create a Persistent Volume called `log-volume`. It should make use of a `storage class` name `manual`. It should use `RWX` as the access mode and have a size of `1Gi`. The volume should use the hostPath `/opt/volume/nginx`

Next, create a PVC called `log-claim` requesting a minimum of `200Mi` of storage. This PVC should bind to `log-volume`.

Mount this in a pod called `logger` at the location `/var/www/nginx`. This pod should use the image `nginx:alpine`.

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: log-volume
spec:
  capacity:
    storage: 1Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteMany
  storageClassName: manual
  hostPath:
    path: /opt/volume/nginx
```

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: log-claim
spec:
  accessModes:
    - ReadWriteMany
  volumeMode: Filesystem
  resources:
    requests:
      storage: 200Mi
  storageClassName: manual
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: logger
spec:
  containers:
    - name: logger
      image: nginx:alpine
      volumeMounts:
      - mountPath: "/var/www/nginx"
        name: nginx-volume
  volumes:
    - name: nginx-volume
      persistentVolumeClaim:
        claimName: log-claim
```

## Exercise 02

We have deployed a new pod called `secure-pod` and a service called `secure-service`. Incoming or Outgoing connections to this pod are not working.
Troubleshoot why this is happening.

Make sure that incoming connection from the pod `webapp-color` are successful.

Important: Don't delete any current objects deployed.

```bash
$ kubectl get pods
$ kubectl exec -it webapp-color -- sh
/ nc -z -v -w 1 secure-service 80
$ kubectl get networkpolicies
$ kubectl get netpol default-deny -o yaml > netpol.yaml
$ kubectl get pods --show-labels
```

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-webapp
  namespace: default
spec:
  podSelector:
    matchLabels:
      run: secure-pod
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          name: webapp-color
    ports:
    - protocol: TCP
      port: 80
```

## Exercise 03

Create a pod called `time-check` in the `dvl1987` namespace. This pod should run a container called `time-check` that uses the `busybox` image.

* Create a config map called `time-config` with the data `TIME_FREQ=10` in the same namespace.

* The time-check container should run the command: `while true; do date; sleep $TIME_FREQ;done` and write the result to the location `/opt/time/time-check.log`.

* The path `/opt/time` on the pod should mount a volume that lasts the lifetime of this pod.

```bash
$ kubectl create ns dvl1987
$ kubectl -n dvl1987 create configmap time-config --from-literal=TIME_FREQ=10
$ kubectl -n dvl1987 run time-check --image=busybox --dry-run=client -o yaml --command=while true; do date; sleep $TIME_FREQ;done
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: time-check
  name: time-check
  namespace: dvl1987
spec:
  containers:
  - command: ["/bin/sh", "-c", "while true; do date; $TIME_FREQ;done > /opt/time/time-check.log"]
    image: busybox
    name: time-check
    env:
    - name: TIME_FREQ
      valueFrom:
        configMapKeyRef:
          name: time-config
          key: TIME_FREQ
    volumeMounts:
    - name: time-storage
      mountPath: /opt/time
  volumes:
  - name: time-storage
    emptyDir: {}
```

## Exercise 04

Create a new deployment called `nginx-deploy`, with one single container called `nginx`, image `nginx:1.16` and `4 replicas`. The deployment should use `RollingUpdate` strategy with `maxSurge=1`, and `maxUnavailable=2`.

Next upgrade the deployment to version `1.17` using rolling update.

Finally, once all pods are updated, undo the update and go back to the previous version.

```bash
$ kubectl create deployment nginx-deploy --image=nginx:1.16 --dry-run=client -o yaml > nginx-deploy.yaml
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx-deploy
  name: nginx-deploy
spec:
  replicas: 4
  selector:
    matchLabels:
      app: nginx-deploy
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 2
  template:
    metadata:
      labels:
        app: nginx-deploy
    spec:
      containers:
      - image: nginx:1.16
        name: nginx
```

```bash
$ kubectl set image deployment nginx-deploy nginx=nginx:1.17 --record=true
$ kubectl rollout undo deployment nginx-deploy
```

## Exercise 05

Create a redis deployment with the following parameters:

Name of the deployment should be `redis` using the `redis:alpine` image. It should have exactly `1 replica`.

* The container should request for `.2` CPU. It should use the label `app=redis`.
* Make sure that the pod is scheduled on `master/controlplane` node.
*  The container should expose the port 6379.
* It should mount exactly 2 volumes:
    * An Empty directory volume called data at path /redis-master-data.
    * A configmap volume called redis-config at path /redis-master.

The configmap has already been created.

```bash
$ kubectl create deployment redis --image=redis:alpine --dry-run=client -o yaml > redis.yaml
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: redis
  name: redis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  strategy: {}
  template:
    metadata:
      labels:
        app: redis
    spec:
      nodeName: master
      containers:
      - image: redis:alpine
        name: redis
        ports:
          - containerPort: 6379
        resources:
          requests:
            cpu: "0.2"
        volumeMounts:
        - name: data
          mountPath: /redis-master-data
        - name: redis-config
          mountPath: /redis-master
      volumes:
      - name: data
        emptyDir: {}
      - name: redis-config
        configMap:
          name: redis-config
```
