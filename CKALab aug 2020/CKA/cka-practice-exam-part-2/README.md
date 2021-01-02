# CKA Practice Exam: Part 2

## Additional Information and Resources

You have been given access to a two-node cluster. Within that cluster, a PersistentVolume has already been created. You must identify the size of the volume in order to make a PersistentVolumeClaim and mount the volume to your pod. Once you have created the PVC and mounted it to your running pod, you must copy the contents of /etc/passwd to the volume. Finally, you will delete the pod and create a new pod with the volume mounted in order to demonstrate the persistence of data. You must perform the following tasks in order to complete this hands-on lab:

* All objects should be in the web namespace.
* The PersistentVolumeClaim name should be data-pvc.
* The PVC request should be 256 MiB.
* The access mode for the PVC should be ReadWriteOnce.
* The storage class name should be local-storage.
* The pod name should be data-pod.
* The pod image should be busybox and the tag should be 1.28.
* The pod should request the PersistentVolumeClaim named data-pvc, and the volume name should be temp-data.
* The pod should mount the volume named temp-data to the /tmp/data directory.
* The name of the second pod should be data-pod2.

```bash
kubectl get namespaces
kubectl get persistentvolumes -n web
# Create a pvc yaml file
```

data-pvc.yaml

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: data-pvc
  namespace: web
spec:
  storageClassName: local-storage
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 256Mi
```

```bash
kubectl run data-pod --image=busybox:1.28 --restart=Never -o yaml --dry-run -- /bin/sh -c 'sleep 3600' > data-pod.yaml
```

data-pod.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: data-pod
  name: data-pod
  namespace: web
spec:
  volumes:
  - name: temp-data
    persistentVolumeClaim:
      claimName: data-pvc
  containers:
  - image: busybox:1.28
    name: data-pod
    args:
     - /bin/sh 
     - -c 
     - sleep 3600
    volumeMounts:
      - mountPath: "/tmp/data"
        name: temp-data
  dnsPolicy: ClusterFirst
  restartPolicy: Never
```

```bash
# Shell into container
kubectl exec -it data-pod -n web -- sh
# Copy /etc/passwd to /tmp/data
cp /etc/passwd /tmp/data/passwd
# Copy data-pod.yaml to data-pod2.yaml and edit names
kubectl delete pod data-pod -n web
kubectl apply -f data-pod2.yaml
kubectl exec -it data-pod2 -n web -- sh
ls -al /tmp/data
```
