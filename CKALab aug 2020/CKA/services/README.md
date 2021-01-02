# Practice Test - Services

## Service definition - NodePort

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: NodePort
  ports:
      # By default and for convenience, the `targetPort` is set to the same value as the `port` field.
    - port: 80
      targetPort: 80
      # Optional field
      # By default and for convenience, the Kubernetes control plane will allocate a port from a range (default: 30000-32767)
      nodePort: 30008
  selector:
    # labels from pod-definition
    app: myapp
```

## Service definition - ClusterIP

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: ClusterIP
  ports:
    - port: 80
      targetPort: 80
  selector:
   app: myapp
```

## Exercises

**How many Services exist in the current(default) namespace**

```bash
$ kubectl get service
# That is a default service created by Kubernetes at launch.
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   5m16s
```
**Create a new service to access the web application using the service-definition-1.yaml file**

> Name: webapp-service; Type: NodePort; targetPort: 8080; port: 8080; nodePort: 30080; selector: simple-webapp

**Create a Service named redis-service of type ClusterIP to expose pod redis on port 6379**

```bash
$ kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml
# or
$ kubectl create service clusterip redis --tcp=6379:6379 --dry-run=client -o yaml
# This will not use the pods labels as selectors, instead it will assume selectors as app=redis. You cannot pass in selectors as an option. So it does not work very well if your pod has a different label set. So generate the file and modify the selectors before creating the service
```

**Create a Service named nginx of type NodePort to expose pod nginx's port 80 on port 30080 on the nodes**

```bash
$ kubectl expose pod nginx --port=80 --name nginx-service --type=NodePort --dry-run=client -o yaml
# or
$ kubectl create service nodeport nginx --tcp=80:80 --node-port=30080 --dry-run=client -o yaml
```