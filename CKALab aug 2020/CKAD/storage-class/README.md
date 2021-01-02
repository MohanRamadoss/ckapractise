# Practice Test - Storage Class

## Exercises

**How many StorageClasses exist in the cluster right now?**

`$ kubectl get storageclasses --all-namespaces`

**What is the name of the Storage Class that does not support dynamic volume provisioning?**

> Look for the storage class name that uses no-provisioner

`$ kubectl get storageclasses --all-namespaces`

**What is the Volume Binding Mode used for this storage class (the one identified in the previous question)**

`$ kubectl describe sc local-storage`

**What is the Provisioner used for the storage class called portworx-io-priority-high?**

`$ kubectl describe sc portworx-io-priority-high`

**Is there a PersistentVolumeClaim that is consuming the PersistentVolume called local-pv?**

`$ kubectl get pvc`

** Let's fix that. Create a new PersistentVolumeClaim by the name of local-pvc that should bind to the volume local-pv**

Inspect the pv local-pv for the specs.

```bash
$ kubectl get pv local-pv
NAME       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS    REASON   AGE
local-pv   500Mi      RWO            Retain           Available           local-storage            4m3s
```

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: local-pvc
spec:
  storageClassName: local-storage
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 500Mi
```

**What is the status of the new pvc**

> Pending

**Why is the PVC in a pending state despite making a valid request to claim the volume called local-pv?**

> Inspect the PVC events.

`$ kubectl describe pvc local-pvc`

> The Storage Class called local-storage makes use of VolumeBindingMode set to WaitForFirstConsumer. This will delay the binding and provisioning of a PersistentVolume until a Pod using the PersistentVolumeClaim is created.

**Create a new pod called `nginx` with the image `nginx:alpine`. The Pod should make use of the PVC `local-pvc` and mount the volume at the path `/var/www/html`.**

> The PV local-pv should in a bound state.

`$ kubectl run nginx --image=nginx:alpine --dry-run=client -o yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  volumes:
    - name: nginx-local-storage
      persistentVolumeClaim:
        claimName: local-pvc
  containers:
  - image: nginx:alpine
    name: nginx
    resources: {}
    volumeMounts:
      - mountPath: "/var/www/html"
        name: nginx-local-storage
```

**Create a new Storage Class called `delayed-volume-sc` that makes use of the below specs**

> `provisioner`: kubernetes.io/no-provisioner
> `volumeBindingMode`: WaitForFirstConsumer


```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: delayed-volume-sc
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer
```