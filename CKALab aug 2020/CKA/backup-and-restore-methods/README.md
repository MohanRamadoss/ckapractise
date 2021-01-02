# Practice Test - Backup and Restore Methods

## Working with ETCDCTL

`etcdctl` is a command line client for [etcd](https://github.com/coreos/etcd).

In all our Kubernetes Hands-on labs, the ETCD key-value database is deployed as a static pod on the master. The version used is v3.

To make use of etcdctl for tasks such as back up and restore, make sure that you set the ETCDCTL_API to 3.

You can do this by exporting the variable ETCDCTL_API prior to using the etcdctl client. This can be done as follows:

`export ETCDCTL_API=3`

```bash
$ export ETCDCTL_API=3
$ etcdctl version
```

[etcd-backup-and-restore.md](https://github.com/mmumshad/kubernetes-the-hard-way/blob/master/practice-questions-answers/cluster-maintenance/backup-etcd/etcd-backup-and-restore.md)

## Exercises

**What is the version of ETCD running on the cluster?**

> Check the ETCD Pod or Process

```bash
$  kubectl logs etcd-master -n kube-system
# or
$ kubectl describe pod etcd-master -n kube-system
```

**At what address do you reach the ETCD cluster from your master node?**

```bash
$ kubectl describe pod etcd-master -n kube-system
# look for --listen-client-urls
```

**Where is the ETCD server certificate file located?**

> Note this path down as you will need to use it later

--key-file=/etc/kubernetes/pki/etcd/server.key

**Where is the ETCD CA Certificate file located?**

> Note this path down as you will need to use it later

--trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt

**The master nodes in our cluster are planned for a regular maintenance reboot tonight. While we do not anticipate anything to go wrong, we are required to take the necessary backups. Take a snapshot of the ETCD database using the built-in snapshot functionality.**

> Store the backup file at location /tmp/snapshot-pre-boot.db

```bash
$ export ETCDCTL_API=3
$ etcdctl --endpoints=https://[127.0.0.1]:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key snapshot save /tmp/snapshot-pre-boot.db
```

**Luckily we took a backup. Restore the original state of the cluster using the backup file.**

```bash
# Restore ETCD Snapshot to a new folder
$ export ETCDCTL_API=3
$ etcdctl --endpoints=https://[127.0.0.1]:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt \
     --name=master \
     --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key \
     --data-dir /var/lib/etcd-from-backup \
     --initial-cluster=master=https://127.0.0.1:2380 \
     --initial-cluster-token=etcd-cluster-1 \
     --initial-advertise-peer-urls=https://127.0.0.1:2380 \
     snapshot restore /tmp/snapshot-pre-boot.db

# Modify /etc/kubernetes/manifests/etcd.yaml
# Update --data-dir to use new target location
# --data-dir=/var/lib/etcd-from-backup
# Update new initial-cluster-token to specify new cluster
# --initial-cluster-token=etcd-cluster-1
# Update volumes and volume mounts to point to new path
```

```yaml
  volumeMounts:
    - mountPath: /var/lib/etcd-from-backup
      name: etcd-data
    - mountPath: /etc/kubernetes/pki/etcd
      name: etcd-certs
  hostNetwork: true
  priorityClassName: system-cluster-critical
  volumes:
  - hostPath:
      path: /var/lib/etcd-from-backup
      type: DirectoryOrCreate
    name: etcd-data
  - hostPath:
      path: /etc/kubernetes/pki/etcd
      type: DirectoryOrCreate
    name: etcd-certs
```
