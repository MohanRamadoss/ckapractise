# Mock Exam 1

## Exercise 01

Deploy a pod named nginx-pod using the nginx:alpine image.

Once done, click on the Next Question button in the top right corner of this panel. You may navigate back and forth freely between all questions. Once done with all questions, click on End Exam. Your work will be validated at the end and score shown. Good Luck!

```bash
$ kubectl run nginx-pod --image=nginx-alpine
```

## Exercise 02

Deploy a messaging pod using the redis:alpine image with the labels set to tier=msg.

```bash
$ kubectl run messaging --image=redis:alpine --labels='tier=msg'
```

## Exercise 03

Create a namespace named apx-x9984574

```bash
$ kubectl create namespace apx-x9984574
```

## Exercise 04

Get the list of nodes in JSON format and store it in a file at /opt/outputs/nodes-z3444kd9.json

```bash
$ kubectl get nodes -o json > /opt/outputs/nodes-z3444kd9.json
```

## Exercise 05 

Create a service messaging-service to expose the messaging application within the cluster on port 6379.

Use imperative commands

```bash
$ kubectl expose pod messaging --name messaging-service --port=6379 --type=ClusterIP
```

## Exercise 06

Create a deployment named hr-web-app using the image kodekloud/webapp-color with 2 replicas

> Name: hr-web-app, Image: kodekloud/webapp-color, Replicas: 2

```bash
$ kubectl create deployment hr-web-app --image=kodekloud/webapp-color -o yaml --dry-run=client > hr-web-app.yaml
$ vi hr-web-app.yaml
# Edit replicas: 1 -> 2
$ kubectl apply -f hr-web-app.yaml
```

## Exercise 07

Create a static pod named static-busybox on the master node that uses the busybox image and the command sleep 1000

> Name: static-busybox, Image: busybox, Command: sleep 1000

```bash
kubectl run static-busybox --image=busybox --restart=Never --dry-run=client -o yaml --command -- sleep 1000 > /etc/kubernetes/manifests/static-busybox.yaml
```

## Exercise 08

Create a POD in the finance namespace named temp-bus with the image redis:alpine.

```bash
$ kubectl run temp-bus --image=redis:alpine --dry-run=client -o yaml > temp-bus.yaml
# vi temp-bus.yaml
# Add under metadata: namespace: finance
$ kubectl create -f temp-bus.yaml 
```

## Exercise 09 

A new application orange is deployed. There is something wrong with it. Identify and fix the issue.

```bash
$ kubectl get pods
$ kubectl describe pod orange
```

```yaml
 init-myservice:
    Container ID:  docker://d162e9848f100a72fe7601d90c723df3afb9a0501843c5403159df225997d818
    Image:         busybox
    Image ID:      docker-pullable://busybox@sha256:9ddee63a712cea977267342e8750ecbc60d3aab25f04ceacfa795e6fce341793
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      sleeeep 2; -> sleep
```

```bash
$ kubectl edit pod orange
# it will create a temp file
$ kubectl delete pod orange
$ kubectl create -f /tmp/orange-XXXX.yaml
```

## Exercise 10

Expose the hr-web-app as service hr-web-app-service application on port 30082 on the nodes on the cluster

The web application listens on port 8080

> Name: hr-web-app-service, Type: NodePort, Endpoints: 2, Port: 8080, NodePort: 30082

```bash
$ kubectl expose deployment hr-web-app --type=NodePort --port=8080 --name=hr-web-app-service --dry-run -o yaml > hr-web-app-service.yaml
# Edit yaml file nodePort and apply
```

## Exercise 11

Use JSON PATH query to retrieve the osImages of all the nodes and store it in a file /opt/outputs/nodes_os_x43kj56.txt

> The osImages are under the nodeInfo section under status of each node.

```bash
$ kubectl get nodes -o=jsonpath='{ .items[*].status.nodeInfo.osImage }' > /opt/outputs/nodes_os_x43kj56.txt
```

## Exercise 12

Create a Persistent Volume with the given specification.

> Volume Name: pv-analytics, Storage: 100Mi, Access modes: ReadWriteMany, Host Path: /pv/data-analytics

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-analytics
spec:
  accessModes:
    - ReadWriteMany
  capacity:
    storage: 100Mi
  hostPath:
    path: /pv/data-analytics
```