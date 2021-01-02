# CKA Practice Exam: Part 3

## Additional Information and Resources

ou have been given access to a three-node cluster. You will be responsible for creating a deployment and a service to serve as a front end for a web application. In addition to the web application, you must deploy a Redis database and make sure the web application can only access this database using the default port of 6379. You will first create a default-deny network policy, so all pods within your Kubernetes are not able to communicate with each other by default. Then you will create a second network policy that specifies the communication on port 6379 between the web application and the database using their label selectors. You must apply these specifications to your resources in order to complete this hands-on lab:

* Create a deployment named webfront-deploy.
* The deployment should use the image nginx with the tag 1.7.8.
* The deployment should expose container port 80 on each pod and contain 2 replicas.
* Create a service named webfront-service and expose port 80, target port 80.
* The service should be exposed externally by listening on port 30080 on each node.
* Create one pod named db-redis using the image redis and the tag latest.
* Verify that you can communicate to pods by default.
* Create a network policy named default-deny that will deny pod communication by default.
* Verify that you can no longer communicate between pods.
* Apply the label role=frontend to the web application pods and the label role=db to the database pod.
* Create a network policy that will apply an ingress rule for the pods labeled with role=db to allow traffic on port 6379 from the pods labeled role=frontend.
* Verify that you have applied the correct labels and created the correct network policies.

```bash
$ kubectl create deployment webfront-deploy --image=nginx:1.7.8 -o yaml --dry-run=client > webfront-deploy.yaml
```

webfront-deploy.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: webfront-deploy
  name: webfront-deploy
spec:
  replicas: 2
  selector:
    matchLabels:
      app: webfront-deploy
  strategy: {}
  template:
    metadata:
      labels:
        app: webfront-deploy
    spec:
      containers:
      - image: nginx:1.7.8
        name: nginx
        ports:
          - containerPort: 80
        resources: {}
```

```bash
$ kubectl create -f webfront-deploy.yaml
$ kubectl expose deployment webfront-deploy --name=webfront-service --port=80 --target-port=80 --type=NodePort --dry-run -o yaml > webfront-service.yaml
```

webfront-service.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: webfront-deploy
  name: webfront-service
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
    nodePort: 30080
  selector:
    app: webfront-deploy
  type: NodePort
```

```bash
$ kubectl create -f webfront-service.yaml
$ kubectl get endpoints
# Verify that you can communicate with your pod directly
kubectl run busybox --rm -it --image=busybox /bin/sh
wget -O- <pod_ip_address>:80
wget --spider --timeout=1 webfront-service
# Create a database server to serve as backend database
$ kubectl run db-redis --image=redis:latest --restart=Never
$ kubectl create -f db-redis.yaml
# Verify that you can communicate to pods by default.
# Create a network policy that will deny communication by default
# Search kubernetes.io for "network policies"
```

default-deny.yaml 

```yaml
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny
spec:
  podSelector: {}
  policyTypes:
  - Ingress
```

```bash
$ kubectl create -f default-deny.yaml
# Verify that communication has been disabled by default
# Label the pods
$ kubectl label pods webfront-deploy-<> role=frontend
$ kubectl label pods db-redis role-db
$ kubectl get pods --show-labels
# Create a network policy for the communication between the two labeled pods 
```

redis-netpolicy.yaml

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: redis-netpolicy
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          role: frontend
    ports:
    - protocol: TCP
      port: 6379
```

```bash
$ kubectl create -f redis-netpolicy.yaml
$ kubectl get networkpolicies
$ kubectl describe networkpolicies redis-netpolicy
$ kubect get pods --show-labels
```
