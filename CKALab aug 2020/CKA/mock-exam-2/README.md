# Mock Exam 2

## Exercise 01

Take a backup of the etcd cluster and save it to /tmp/etcd-backup.db

```bash
$ kubectl get pods -n kube-system
$ kubectl describe pod etcd-master -n kube-system
etcd
  --advertise-client-urls=https://172.17.0.16:2379
  --cert-file=/etc/kubernetes/pki/etcd/server.crt
  --client-cert-auth=true
  --data-dir=/var/lib/etcd
  --initial-advertise-peer-urls=https://172.17.0.16:2380
  --initial-cluster=master=https://172.17.0.16:2380
  --key-file=/etc/kubernetes/pki/etcd/server.key
  --listen-client-urls=https://127.0.0.1:2379,https://172.17.0.16:2379
  --listen-metrics-urls=http://127.0.0.1:2381
  --listen-peer-urls=https://172.17.0.16:2380
  --name=master
  --peer-cert-file=/etc/kubernetes/pki/etcd/peer.crt
  --peer-client-cert-auth=true
  --peer-key-file=/etc/kubernetes/pki/etcd/peer.key
  --peer-trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
  --snapshot-count=10000
  --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
```

```bash
export ETCDCTL_API=3
etcdctl \
--endpoints=https://[127.0.0.1]:2379 \
--cacert=/etc/kubernetes/pki/etcd/ca.crt \
--cert=/etc/kubernetes/pki/etcd/server.crt \
--key=/etc/kubernetes/pki/etcd/server.key \
snapshot save /tmp/etcd-backup.db
```

## Exercise 02

Create a Pod called redis-storage with image: redis:alpine with a Volume of type emptyDir that lasts for the life of the Pod. Specs on the right.

> Pod named 'redis-storage' created, Pod 'redis-storage' uses Volume type of emptyDir, Pod 'redis-storage' uses volumeMount with mountPath = /data/redis

```bash
$ kubectl run redis-storage --image=redis:alpine --dry-run=client -o yaml > redis-storage.yaml
```

Edit the yaml file

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: redis-storage
  name: redis-storage
spec:
  containers:
  - image: redis:alpine
    name: redis-storage
    volumeMounts:
    - mountPath: /data/redis
      name: cache-volume
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  volumes: 
    - name: cache-volume
      emptyDir: {}
```

`$ kubectl create -f redis-storage.yaml`

## Exercise 03

Create a new pod called super-user-pod with image busybox:1.28. Allow the pod to be able to set system_time

The container should sleep for 4800 seconds

> Pod: super-user-pod, Container Image: busybox:1.28, SYS_TIME capabilities for the conatiner?, sleep for 4800

```bash
$ kubectl run super-user-pod --image=busybox:1.28 --restart=Never --dry-run=client -o yaml --command -- sleep 4800
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: super-user-pod
  name: super-user-pod
spec:
  containers:
  - command:
    - sleep
    - "4800"
    image: busybox:1.28
    name: super-user-pod
    securityContext:
      capabilities:
        add: ["SYS_TIME"]
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
```

## Exercise 04

A pod definition file is created at /root/use-pv.yaml. Make use of this manifest file and mount the persistent volume called pv-1. Ensure the pod is running and the PV is bound.

mountPath: /data persistentVolumeClaim Name: my-pvc

> persistentVolume Claim configured correctly, pod using the correct mountPath, pod using the persistent volume claim?


Initial POD definition file

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: use-pv
  name: use-pv
spec:
  containers:
  - image: nginx
    name: use-pv
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
```

```bash
$ kubectl get pv -o wide
$ kubectl get pvc -o wide
No resources found in default namespace.
```

my-pvc definition file
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Mi
```

Modified POD definition file

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: use-pv
  name: use-pv
spec:
  volumes:
  - name: data-pv-storage
    persistentVolumeClaim:
      claimName: my-pvc
  containers:
  - image: nginx
    name: use-pv
    resources: {}
    volumeMounts:
      - mountPath: "/data"
        name: data-pv-storage
  dnsPolicy: ClusterFirst
  restartPolicy: Always
```

## Exercise 05

Create a new deployment called nginx-deploy, with image nginx:1.16 and 1 replica. Record the version. Next upgrade the deployment to version 1.17 using rolling update. Make sure that the version upgrade is recorded in the resource annotation.

> Deployment : nginx-deploy. Image: nginx:1.16, Task: Upgrade the version of the deployment to 1:17, Task: Record the changes for the image upgrade

```bash
$ kubectl create deployment nginx-deploy --image=nginx:1.16 
$ kubectl set image deployment/nginx-deploy nginx=nginx:1.17 --record=true
```

## Exercise 06

Create a new user called john. Grant him access to the cluster. John should have permission to create, list, get, update and delete pods in the development namespace . The private key exists in the location: /root/john.key and csr at /root/john.csr

> CSR: john-developer Status:Approved, Role Name: developer, namespace: development, Resource: Pods, Access: User 'john' has appropriate permissions

```bash
vi johh.yaml
```

```yaml
apiVersion: certificates.k8s.io/v1beta1
kind: CertificateSigningRequest
metadata:
  name: john-developer
spec:
  request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZEQ0NBVHdDQVFBd0R6RU5NQXNHQTFVRUF3d0VhbTlvYmpDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRApnZ0VQQURDQ0FRb0NnZ0VCQUxlNHc3OUsxR3Rha29yTHdOQWNWME83R05IY0FCUHVqT0xjaEd5cXc5MURFSHVsCmVyUjNvdVZId2taaWZoZkdqSk83c0ZuV2thRmlRZm1pRjFmZFEvYkFCVkFUVkxtRzVsL2RDUnZRSUFBVmczalkKVFNRSVJRV2czRjlpWXVwNU9hSUovYnVpUURiMXdtWGVFTnREaHkzM1I2emZmSDRhK3BkcENYbDhDcG1tYk84eQpBYW5zVzV6aTZtQUN2bzdkY2l5eitkQUNSQXR4MEJDTVp1ZFdBRjdodHdtKzN5Vkc4QUcyeStCdUlkQ0pnTWZ1CmpIVUVUQkp1L3BYb0FKVm5mQWo5bWFRMGtLeU5VSnc0WWFmS1d4Vjl0SHZjT0t4NzN2ZGRuRnBYVHIyMDk2QzYKdkg3bWVkTzkvNjU2UU5FQUlHR3hBMjlGY1c0S0tQSzVSbS9sRWlrQ0F3RUFBYUFBTUEwR0NTcUdTSWIzRFFFQgpDd1VBQTRJQkFRQ0J3T3hraG81bkpjVjRmbHdEcUV4OHlKVTBST2lEbXh1KzZQVW52RVZUOVdkYVpMN1lkdEpLCk52S1NjQTB2V2p0eHJDdmkrenJtYW1qMjdUbjdtemdKMnlnKzBPeEdSKzdFQnMyTHJzUnlpMVg0TE5hTmFRVTUKOUEzUmRxdWxRWEV2ek9HTmtNME94M1dkaWd1N1htclIyZFhPbVNDTWVPZzZSZkNHMkRlZU4xOG9sRUR4bjJjTgpFOFNuZzBsY0VXYWVwY251dWwxS0psMVluR25MQTJWRTNZZEh5VHhNaDlpSVJBTVlYTzF6TDF0ZitZc0Qva2gvCnJseFFsM0NlekZndzVJWThZMXp5cEpjbVZUTlhyS21sbGc4a3BtcDB0Q0FYZ2R2Um5nS3VLa3FRSkNiS3kzSWIKTDV3ejNpbUppRzBEQlNCOWNqRnF3ZkdUK2VVbHhzNlkKLS0tLS1FTkQgQ0VSVElGSUNBVEUgUkVRVUVTVC0tLS0tCg==
  usages:
  - digital signature
  - key encipherment
  - server auth
```

```bash
$ cat john.csr | base64 | tr -d '\n'
$ kubectl create -f john.yaml
$ kubectl get csr
$ kubectl certificate approve john-developer
$ kubectl create role developer --resource=pods --verb=create,list,get,update,delete --namespace=development
$ kubectl describe role developer -n development
$ kubectl create rolebinding developer-role-binding --role=developer --user=john --namespace=development
$ kubectl describe rolebinding developer-role-binding
$ kubectl auth can-i update pods --as=john --namespace=developer
```

## Exercise 07

Create an nginx pod called nginx-resolver using image nginx, expose it internally with a service called nginx-resolver-service. Test that you are able to look up the service and pod names from within the cluster. Use the image: busybox:1.28 for dns lookup. Record results in /root/nginx.svc and /root/nginx.pod

> Pod: nginx-resolver created, Service DNS Resolution recorded correctly, Pod DNS resolution recorded correctly

```bash
$ kubectl run nginx-resolver --image=nginx
$ kubectl expose pod nginx-resolver --port=80 --target-port=80 --name=nginx-resolver-service 
$ kubectl run test-nslookup --image=busybox:1.28 --restart=Never --command -- sleep 4800
$ kubectl exec test-nslookup -- nslookup nginx-resolver-service > /root/nginx.svc
$ kubectl get pods -o wide
$ kubectl exec test-nslookup -- nslookup 10-244-1-4.default.pod > /root/nginx.pod
```

## Exercise 08

Create a static pod on node01 called nginx-critical with image nginx. Create this pod on node01 and make sure that it is recreated/restarted automatically in case of a failure.

Use /etc/kubernetes/manifests as the Static Pod path for example.

> Kubelet Configured for Static Pods, Pod nginx-critical-node01 is Up and running

```bash
$ kubectl run nginx-critical --image=nginx -o yaml --dry-run=client > nginx-critical.yaml
$ ssh node01
node01 $ mkdir /etc/kubernetes/manifests
node01 $ touch /etc/kubernetes/manifests/nginx-critical.yaml
node01 $ systemctl restart kubelet
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx-critical
  name: nginx-critical
spec:
  containers:
  - image: nginx
    name: nginx-critical
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
```
