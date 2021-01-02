# Practice Test - Control Plane Failure

## Troubleshooting

```bash
$ kubectl get nodes -o wide
$ kubectl get pods -n kube-system
# Check Controlplane Services
$ service kube-apiserver status
$ service kube-controller-manager status
$ service kube-scheduler status
$ service kubelet status
$ service kube-proxy status
# Check Service Logs
$ kubectl logs kube-apiserver-master -n kube-system
$ sudo journalctl -u kube-apiserver
```

## Exercises

**The cluster is broken again. We tried deploying an application but it's not working. Troubleshoot and fix the issue.**

```bash
$ kubectl get nodes -o wide
$ kubectl get pods -n kube-system
NAME                                      READY   STATUS             RESTARTS   AGE   IP            NODE     NOMINATED NODE   READINESS GATES
coredns-66bff467f8-hsz6q                  1/1     Running            0          22m   10.244.0.3    master   <none>           <none>
coredns-66bff467f8-krfzg                  1/1     Running            0          22m   10.244.0.2    master   <none>           <none>
etcd-master                               1/1     Running            0          22m   172.17.0.15   master   <none>           <none>
katacoda-cloud-provider-58f89f7d9-tx5s2   1/1     Running            9          22m   10.244.1.2    node01   <none>           <none>
kube-apiserver-master                     1/1     Running            0          22m   172.17.0.15   master   <none>           <none>
kube-controller-manager-master            1/1     Running            0          22m   172.17.0.15   master   <none>           <none>
kube-flannel-ds-amd64-bhm9p               1/1     Running            1          22m   172.17.0.16   node01   <none>           <none>
kube-flannel-ds-amd64-x6fzj               1/1     Running            0          22m   172.17.0.15   master   <none>           <none>
kube-keepalived-vip-6gq8z                 1/1     Running            0          22m   172.17.0.16   node01   <none>           <none>
kube-proxy-4wck6                          1/1     Running            0          22m   172.17.0.16   node01   <none>           <none>
kube-proxy-6lxlt                          1/1     Running            0          22m   172.17.0.15   master   <none>           <none>
kube-scheduler-master                     0/1     CrashLoopBackOff   7          16m   172.17.0.15   master   <none>           <none>
```

`$ kubectl describe pod -n kube-system kube-scheduler-master`

```yaml
Containers:
  kube-scheduler:
    Container ID:  docker://a2d47e7a04d9166fbb8e4af362d2541b0df31dcaccebd9e13850abd8d6f761d6
    Image:         k8s.gcr.io/kube-scheduler:v1.18.0
    Image ID:      docker-pullable://k8s.gcr.io/kube-scheduler@sha256:33063bc856e99d12b9cb30aab1c1c755ecd458d5bd130270da7c51c70ca10cf6
    Port:          <none>
    Host Port:     <none>
    Command:
      kube-schedulerrrr # This is wrong
      --authentication-kubeconfig=/etc/kubernetes/scheduler.conf
      --authorization-kubeconfig=/etc/kubernetes/scheduler.conf
      --bind-address=127.0.0.1
      --kubeconfig=/etc/kubernetes/scheduler.conf
      --leader-elect=true
```

`vi /etc/kubernetes/manifests/kube-scheduler.yaml`

**Scale the deployment app to 2 pods.**

`$ kubectl scale deploy app --replicas=2`

**Even though the depoyment was scaled to 2, the number of PODs does not seem to increase. Investigate and fix the issue.**

> Inspect the component responsible for managing deployments and replicasets.

```bash
$ kubectl get pods -n kube-system
kube-controller-manager-master            0/1     CrashLoopBackOff   4          2m26s   172.17.0.15   master   <none>           <none>
$ kubectl describe pod -n kube-system kube-controller-manager-master
```

```yaml
Command:
  kube-controller-manager
  --allocate-node-cidrs=true
  --authentication-kubeconfig=/etc/kubernetes/controller-manager.conf
  --authorization-kubeconfig=/etc/kubernetes/controller-manager.conf
  --bind-address=127.0.0.1
  --client-ca-file=/etc/kubernetes/pki/ca.crt
  --cluster-cidr=10.244.0.0/16
  --cluster-name=kubernetes
  --cluster-signing-cert-file=/etc/kubernetes/pki/ca.crt
  --cluster-signing-key-file=/etc/kubernetes/pki/ca.key
  --controllers=*,bootstrapsigner,tokencleaner
  --kubeconfig=/etc/kubernetes/controller-manager-XXXX.conf #This is wrong
```

`vi /etc/kubernetes/manifests/kube-controller-manager.yaml`

**Something is wrong with scaling again. We just tried scaling the deployment to 3 replicas. But it's not happening.**

```bash
$ kubectl describe pod -n kube-system kube-controller-manager-master
k8s-certs:
  Type:          HostPath (bare host directory volume)
  Path:          /etc/kubernetes/WRONG-PKI-DIRECTORY
  HostPathType:  DirectoryOrCreate
```



