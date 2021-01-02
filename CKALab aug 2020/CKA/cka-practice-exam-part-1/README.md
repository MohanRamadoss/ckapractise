# CKA Practice Exam: Part 1

## Additional Information and Resources

You have been given access to a three-node cluster. Within that cluster, you will be responsible for creating a service for end users of your web application. You will ensure the application meets the specifications set by the developers and the proper resources are in place to ensure maximum uptime for the app. You must perform the following tasks in order to complete this hands-on lab:

* All objects should be in the web namespace.
* The deployment name should be webapp.
* The deployment should have 3 replicas.
* The deployment’s pods should have one container using the linuxacademycontent/podofminerva image with the tag latest.
* The service should be named web-service.
* The service should forward traffic to port 80 on the pods.
* The service should be exposed externally by listening on port 30080 on each node.
* The pods should be configured to check the /healthz. endpoint on port 8081, and automatically restart the container if the check fails.
* The pods should be configured to not receive traffic until the endpoint on port 80 responds successfully.

```bash
kubectl get nodes -o wide
# Check namespaces
kubectl get namespace
# Create web namespace
kubectl create namespace web
# Create deployment with image linuxacademycontent/podofminerva
kubectl create deployment webapp --image=linuxacademycontent/podofminerva:latest --dry-run -o yaml > webapp-deployment.yaml
# edit deployment file
# Another way is to use run command. This works only with lower versions of kubernetes!
```

webapp-deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: webapp
  name: webapp
  namespace: web
spec:
  replicas: 3
  selector:
    matchLabels:
      app: webapp
  strategy: {}
  template:
    metadata:
      labels:
        app: webapp
    spec:
      containers:
      - image: linuxacademycontent/podofminerva:latest
        name: podofminerva
        ports:
        - containerPort: 80
          protocol: TCP
```

```bash
# create service
kubectl expose deployment -n web webapp --port=80 --target-port=80 --type=NodePort --dry-run -o yaml --name=webapp-service > webapp-service.yaml
# Edit webapp-service and add NodePort
```

webapp-service.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: webapp
  name: webapp-service
  namespace: web
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
    nodePort: 30080
  selector:
    app: webapp
  type: NodePort
```

```bash
# Test nodePort
curl localhost:30080
# Add liveness and readines probes to deployment in pod definition
```

```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8081
readinessProbe:
  httpGet:
    path: /
    port: 80
```
