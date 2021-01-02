# Mock Exam 2

## Exercise 01

Create a deployment called `my-webapp` with image: `nginx`, label `tier:frontend` and `2` replicas. Expose the deployment as a `NodePort` service with name `front-end-service` , port: `80` and NodePort: `30083`

```bash
$ kubectl create deployment my-webapp --image=nginx --dry-run=client -o yaml
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: my-webapp
    tier: frontend
  name: my-webapp
spec:
  replicas: 2
  selector:
    matchLabels:
      app: my-webapp
      tier: frontend
  template:
    metadata:
      labels:
        app: my-webapp
        tier: frontend
    spec:
      containers:
      - image: nginx
        name: nginx
```

```bash
$ kubectl expose deployment my-webapp --name=front-end-service --type=NodePort --port=80 --dry-run=client -o yaml
```

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: my-webapp
    tier: frontend
  name: front-end-service
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
    nodePort: 30083
  selector:
    app: my-webapp
    tier: frontend
  type: NodePort
```

## Exercise 02

Add a taint to the node `node01` of the cluster. Use the specification below:

> key: `app_type`, value: `alpha` and effect: `NoSchedule`
> Create a pod called `alpha`, image: `redis` with toleration to `node01`

`$ kubectl taint node node01 app_type=alpha:NoSchedule`

`$ kubectl run alpha --image=redis --dry-run=client -o yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: alpha
  name: alpha
spec:
  tolerations:
  - key: "app_type"
    value: "alpha"
    operator: "Equal"
    effect: "NoSchedule"
  containers:
  - image: redis
    name: alpha
  restartPolicy: Always
```

## Exercise 03

Apply a label `app_type=beta` to node `node02`. Create a new deployment called `beta-apps` with image: `nginx` and replicas: `3`. Set Node Affinity to the deployment to place the PODs on node02 only.

> NodeAffinity: requiredDuringSchedulingIgnoredDuringExecution

`$ kubectl label nodes node02 app_type=beta`

`$ kubectl create deployment beta-apps --image=nginx --dry-run=client -o yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: beta-apps
  name: beta-apps
spec:
  replicas: 3
  selector:
    matchLabels:
      app: beta-apps
  template:
    metadata:
      labels:
        app: beta-apps
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: app_type
                operator: In
                values:
                - beta
      containers:
      - image: nginx
        name: nginx
```

## Exercise 04

Create a new Ingress Resource for the service: `my-video-service` to be made available at the URL: `http://ckad-mock-exam-solution.com:30093/video`.

> Create an ingress resource with host: ckad-mock-exam-solution.com path:/video
> Once set up, curl test of the URL from the nodes should be successful / HTTP 200

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
  name: my-video-ingress
  namespace: default
spec:
  rules:
  - host: ckad-mock-exam-solution.com
    http:
      paths:
      - path: /video
        backend:
          serviceName: my-video-service
          servicePort: 8080
```

## Exercise 05

We have deployed a new pod called `pod-with-rprobe`. This Pod has an initial delay before it is Ready. Update the newly created pod `pod-with-rprobe` with a `readinessProbe` using the given spec:

> httpGet path: /ready, httpGet port: 8080

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    name: pod-with-rprobe
  name: pod-with-rprobe
  namespace: default
spec:
  containers:
  - env:
    - name: APP_START_DELAY
      value: "180"
    image: kodekloud/webapp-delayed-start
    imagePullPolicy: Always
    name: pod-with-rprobe
    ports:
    - containerPort: 8080
      protocol: TCP
    readinessProbe:
      httpGet:
        path: /ready
        port: 8080
```

## Exercise 06

Create a new pod called `nginx1401` in the `default` namespace with the image `nginx`. Add a `livenessProbe` to the container to restart it if the command `ls /var/www/html/probe` fails. This check should start after a delay of `10 seconds` and run every `60 seconds`.

> You may delete and recreate the object. Ignore the warnings from the probe.

'$ kubectl run nginx1401 --image=nginx --dry-run=client -o yaml'

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx1401
  name: nginx1401
spec:
  containers:
  - image: nginx
    name: nginx1401
    livenessProbe:
      exec:
        command:
          - ls
          - /var/www/html/probe 
      initialDelaySeconds: 10
      periodSeconds: 60
```

## Exercise 07

Create a job called `whalesay` with image `docker/whalesay` and command `"cowsay I am going to ace CKAD!"`.

> completions: 10
> backoffLimit: 6
> restartPolicy: Never

`$ kubectl create job whalesay --image=docker/whalesay --dry-run=client -o yaml`

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  creationTimestamp: null
  name: whalesay
spec:
  backoffLimit: 6
  completions: 10
  template:
    metadata:
      creationTimestamp: null
    spec:
      containers:
      - image: docker/whalesay
        name: whalesay
        command:
          - cowsay
          - I am going to ace CKAD!
        resources: {}
      restartPolicy: Never
```

## Exercise 08

Create a pod called `multi-pod` with two containers.

* Container 1: name: `jupiter`, image: `nginx`
* Container 2: name: `europa`, image: `busybox`, command: `sleep 4800`

Environment Variables: 

* Container 1: `type: planet`
* Container 2: `type: moon`

`$ kubectl run multi-pod --image=nginx --dry-run=client -o yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: multi-pod
  name: multi-pod
spec:
  containers:
  - image: nginx
    name: jupiter
    env:
    - name: type
      value: planet
  - image: busybox
    name: europa
    command:
      - sleep
      - "4800"
    env:
    - name: type
      value: moon
```

## Exercise 09

Create a PersistentVolume called `custom-volume` with size: `50MiB` reclaim policy: `Retain`, Access Modes: `ReadWriteMany` and hostPath: `/opt/data`

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: custom-volume
spec:
  capacity:
    storage: 5Mi
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: /opt/data
```
