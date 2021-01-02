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
