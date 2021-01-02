# Mock Exam 1

## Exercise 01

Deploy a pod named `nginx-448839` using the `nginx:alpine` image.

`$ kubectl run nginx-448839 --image=nginx:alpine`

## Exercise 02

Create a namespace named `apx-z993845`

`$ kubectl create namespace apx-z993845`

## Exercise 03

Create a new Deployment named `httpd-frontend` with `3 replicas` using image `httpd:2.4-alpine`.

```bash
$ kubectl create deployment httpd-frontend --image=httpd:2.4-alpine
$ kubectl scale deployment/httpd-frontend --replicas=3
```

## Exercise 04

Deploy a `messaging` pod using the `redis:alpine` image with the labels set to `tier=msg`.

`$ kubectl run messaging --image=redis:alpine --labels="tier=msg"`

## Exercise 05

A replicaset `rs-d33393` is created. However the pods are not coming up. Identify and fix the issue.

> Once fixed, ensure the ReplicaSet has 4 Ready replicas.

```bash
$ kubectl get rs
$ kubectl describe rs rs-d33393
```

```yaml
Pod Template:
  Labels:  name=busybox-pod
  Containers:
   busybox-container:
    Image: busyboxXXXXXXX # Wrong Image
```

## Exercise 06

Create a service `messaging-service` to expose the `redis` deployment in the `marketing` namespace within the cluster on port `6379`.

> Use imperative commands

`$ kubectl -n marketing expose deployment/redis --name=messaging-service --type=ClusterIP --port=6379`


## Exercise 07

Update the environment variable on the pod `webapp-color` to use a `green` background.

> Pod Name: webapp-color, Label Name: webapp-color, Env: APP_COLOR=green

```bash
$ kubectl get pod webapp-color -o yaml > webapp-color.yaml
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2020-09-03T17:12:57Z"
  labels:
    name: webapp-color
  name: webapp-color
  namespace: default
spec:
  containers:
  - env:
    - name: APP_COLOR
      value: pink # Change the value of env environment
    image: kodekloud/webapp-color
    imagePullPolicy: Always
    name: webapp-color
```

## Exercise 08

Create a new ConfigMap named `cm-3392845`. Use the spec given.

> Data: DB_NAME=SQL3322, DB_HOST=sql322.mycompany.com, DB_PORT=3306

`$ kubectl create configmap cm-3392845 --from-literal=DB_NAME=SQL3322 --from-literal=DB_HOST=sql322.mycompany.com --from-literal=DB_PORT=3306`

## Exercise 09

Create a new Secret named `db-secret-xxdf` with the data given.

>  Secret 1: DB_Host=sql01, Secret 2: DB_User=root, Secret 3: DB_Password=password123

`$ kubectl create secret generic db-secret-xxdf --from-literal=DB_Host=sql01 --from-literal=DB_User=root --from-literal=DB_Password=password123`

## Exercise 10

Update pod `app-sec-kff3345` to run as Root user and with the `SYS_TIME` capability.

`$ kubectl get pod app-sec-kff3345 -o yaml > app-sec-kff3345.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-sec-kff3345
  namespace: default
spec:
  containers:
  - command:
    - sleep
    - "4800"
    image: ubuntu
    imagePullPolicy: Always
    name: ubuntu
    securityContext:
      capabilities:
        add: ["SYS_TIME"]
```

## Exercise 11

Export the logs of the `e-com-1123` pod to the file `/opt/outputs/e-com-1123.logs`

> It is in a different namespace. Identify the namespace first.

```bash
$ kubectl get pods --all-namespaces | grep -i e-com-1123
$ kubectl -n e-commerce describe pod e-com-1123
$ kubectl -n e-commerce logs e-com-1123 -c simple-webapp > /opt/outputs/e-com-1123.logs
```

## Exercise 12

Create a `Persistent Volume` with the given specification.

> Volume Name: pv-analytics, Storage: 100Mi, Access modes: ReadWriteMany, Host Path: /pv/data-analytics

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-analytics
spec:
  capacity:
    storage: 100Mi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteMany
  hostPath:
    path: /pv/data-analytics
```

## Exercise 13

Create a `redis` deployment using the image `redis:alpine` with 1 replica and label `app=redis`. Expose it via a `ClusterIP` service called `redis` on port `6379`. Create a new Ingress Type NetworkPolicy called `redis-access` which allows only the pods with label `access=redis` to access the deployment.

```bash
$ kubectl create deployment redis --image=redis:alpine
$ kubectl expose deployment redis --name=redis --port=6379 --type=ClusterIP
```

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: redis-access
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: redis
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          access: redis
```

## Exercise 14

Create a Pod called `sega` with two containers:

* Container 1: Name `tails` with image `busybox` and command: `sleep 3600`.

* Container 2: Name `sonic` with image `nginx` and Environment variable: `NGINX_PORT` with the value `8080`.

```bash
$ kubectl run sega --image=busybox --dry-run=client -o yaml --command sleep 3600
$ kubectl run sega --image=nginx --dry-run=client -o yaml --env="NGINX_PORT=8080"
# Combine both outputs
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: sega
  name: sega
spec:
  containers:
  - command:
    - sleep
    - "3600"
    image: busybox
    name: tails
  - env:
    - name: NGINX_PORT
      value: "8080"
    image: nginx
    name: sonic
```
