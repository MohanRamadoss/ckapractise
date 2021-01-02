# Practice Test - PODs

## Pod definition

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: nginx-container
    image: nginx
```

## Edit a POD

Remember, you CANNOT edit specifications of an existing POD other than the below.

* spec.containers[*].image
* spec.initContainers[*].image
* spec.activeDeadlineSeconds
* spec.tolerations

So how to edit a pod?

1. Extract the pod definition in YAML format to a file using the command

    ```bash
    $ kubectl get pod webapp -o yaml > my-new-pod.yaml
    ```

2. Make the changes to the exported file using an editor (vi editor). Save the changes.

3. Delete the existing pod

    ```bash
    $ kubectl delete pod webapp
    ```

4. Create a new pod with the edited file

    ```bash
    $ kubectl create -f my-new-pod.yaml
    ```

## Exercises

**How many PODs exist in the current(default) namespace.**

```bash
$ kubectl get pods
```

**Create a new pod with the NGINX image.**

```bash
kubectl run nginx --image=nginx
# Generate POD Manifest YAML file (-o yaml). Don't create it(--dry-run)
kubectl run nginx --image=nginx --dry-run -o yaml
```

**Which nodes are these pods placed on? You must look at all the pods in detail to figure this out.**

```bash
$ kubectl get pods -o wide
```

**Now fix the image on the pod to 'redis'. Update the pod-definition file and use 'kubectl apply' command or use 'kubectl edit pod redis' command.**

```bash
$ kubectl edit pod redis
```
