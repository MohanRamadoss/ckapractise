# Practice Test - Persistent Volumes and Persistent Volume Claims

## Persistent Volume Definition

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-vol1
spec: 
  accessModes: 
    - ReadWriteOnce
  capacity:
    storage:  1Gi
  
  awsElasticBlockStore:
    volumeID: <volume-id>
    fsType: ext4
```

## Persistent Volume Claim Definition

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: myclaim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 500Mi
```

Once you create a PVC use it in a POD definition file by specifying the PVC Claim name under persistentVolumeClaim section in the volumes section like this:

```yaml
piVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/var/www/html"
        name: mypd
  volumes:
    - name: mypd
      persistentVolumeClaim:
        claimName: myclaim
```

## Exercises

**The application stores logs at location /log/app.log. View the logs.**

```bash
$ kubectl exec webapp -- cat /log/app.log
```

**Configure a volume to store these logs at /var/log/webapp on the host**

> Use the specs: Name: webapp, Image Name: kodekloud/event-simulator, Volume HostPath: /var/log/webapp, Volume Mount: /log

```bash
$ kubectl create -f webapp-pod-hostpath.yaml
```

**Create a 'Persistent Volume' with the given specification.**

> Use the specs: Volume Name: pv-log, Storage: 100Mi, Access modes: ReadWriteMany, Host Path: /pv/log

```bash
$ kubectl create -f pv-log.yaml
```

**Let us claim some of that storage for our application. Create a 'Persistent Volume Claim' with the given specification.**

> Use the specs: Volume Name: claim-log-1, Storage Request: 50Mi, Access modes: ReadWriteOnce

```bash
$ kubectl create -f claim-log-1.yaml
```

**What is the state of the Persistent Volume Claim?**

```bash
$ kubectl get pvc
```

**What is the state of the Persistent Volume?**

```bash
$ kubectl get pv
```

**Update the webapp pod to use the persistent volume claim as its storage.**

> Use the specs: Name: webapp, Image Name: kodekloud/event-simulator, Volume: PersistentVolumeClaim=claim-log-1, Volume Mount: /log

```bash
$ kubectl create -f webapp-pod-pvc.yaml
```

**What is the Reclaim Policy set on the Persistent Volume - 'pv-log'**

```bash
$ kubectl get p
```

**Try deleting the PVC and notice what happens.**

```bash
$ kubectl delete pvc claim-log-1
# The PVC is stuck in 'terminating' state
```

**Why is the PVC stuck in 'Terminating' state?**

> The PVC is being used by the POD

**Implement State Persistence for Kubernetes Pods**

Your company needs a small database server to support a new application. They have asked you to deploy a pod running a MySQL container, but they want the data to persist even if the pod is deleted or replaced. Therefore, the MySQL database pod requires persistent storage.

You will need to do the following:

1. Create a PersistentVolume:

* The PersistentVolume should be named mysql-pv.
* The volume needs a capacity of 1Gi.
* Use a storageClassName of localdisk.
* Use the accessMode ReadWriteOnce.
* Store the data locally on the node using a hostPath volume at the location /mnt/data.


```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  storageClassName: localdisk
  hostPath:
    path: /mnt/data
```

`$ kubectl create -f mysql-pv`

2. Create a PersistentVolumeClaim:

* The PersistentVolumeClaim should be named mysql-pv-claim.
* Set a resource request on the claim for 500Mi of storage.
* Use the same storageClassName and accessModes as the PersistentVolume so that this claim can bind to the PersistentVolume.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 500Mi
  storageClassName: localdisk
```

`$ kubectl create -f mysql-pv-claim`

3. Create a MySQL Pod configured to use the PersistentVolumeClaim:

* The Pod should be named mysql-pod.
* Use the image mysql:5.6.
* Expose the containerPort 3306.
* Set an environment variable called MYSQL_ROOT_PASSWORD with the value password.
* Add the PersistentVolumeClaim as a volume and mount it to the container at the path /var/lib/mysql.

`$ kubectl run mysql-pod --image=mysql:5.6 --dry-run=client --port=3306 -o yaml > mysql-pod.yaml`

Edit YAML accordingly

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: mysql-pod
  name: mysql-pod
spec:
  containers:
  - image: mysql:5.6
    name: mysql-pod
    env:
    - name: MYSQL_ROOT_PASSWORD
      value: "password"
    ports:
    - containerPort: 3306
    volumeMounts:
      - mountPath: "/var/lib/mysql"
        name: data
  volumes:
    - name: data
      persistentVolumeClaim:
        claimName: mysql-pv-claim
  restartPolicy: Always
```
