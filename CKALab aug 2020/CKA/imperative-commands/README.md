# Practice Test - Imperative Commands

## Exercises

**Deploy a pod named nginx-pod using the nginx:alpine image.**

```bash
$ kubectl run nginx-pod --image=nginx:alpine -o yaml
```

**Deploy a redis pod using the redis:alpine image with the labels set to tier=db.**

```bash
$ kubectl run redis --image=redis:alpine --labels='tier=db'
```

**Create a service redis-service to expose the redis application within the cluster on port 6379.**

> Service: redis-service, Port: 6379, Type: ClusterIp, Selector: tier=db

```bash
$ kubectl expose pod redis --port=6379 --name redis-service
```

**Create a deployment named webapp using the image kodekloud/webapp-color with 3 replicas**

> Name: webapp, Image: kodekloud/webapp-color, Replicas: 3

```bash
$ kubectl create deployment webapp --image=kodekloud/webapp-color
# scale deployment
$ kubectl scale deployment/webapp --replicas=3
```

**Create a new pod called custom-nginx using the nginx image and expose it on container port 8080**

```bash
$ kubectl run custom-nginx --image=nginx --port=8080
```

**Create a new namespace called dev-ns.**

```bash
$ kubectl create namespace dev-ns
# or
$ kubectl create ns dev-ns
```

**Create a new deployment called redis-deploy in the dev-ns namespace with the redis image. It should have 2 replicas.**

> redis-deploy created in the dev-ns namespace, replicas: 2

```bash
$ kubectl create deployment redis-deploy --image=redis --namespace=dev-ns --dry-run=client -o yaml > redis-deploy.yaml
# Edit yaml file and change replicas to 2
$ kubectl apply -f deploy.yaml
```

**Create a pod called httpd using the image httpd:alpine in the default namespace. Next, create a service of type ClusterIP by the same name (httpd). The target port for the service should be 80.**

```bash
kubectl run httpd --image=httpd:alpine --port=80 --expose
kubectl expose pod httpd --name=httpd --port=80 --target-port=80 --type=ClusterIP
```
