# Lightning Labs

## Exercises

### Exercise 01

Upgrade the current version of kubernetes from 1.17 to 1.18.0 exactly using the kubeadm utility. Make sure that the upgrade is carried out one node at a time starting with the master node. To minimize downtime, the deployment gold-nginx should be rescheduled on an alternate node before upgrading each node.

Upgrade master node first. Drain node01 before upgrading it. Pods for gold-nginx should run on the master node subsequently.

```bash
$ kubectl drain master --ignore-daemonsets
$ apt install kubeadm=1.18.0-00
$ kubeadm upgrade apply v1.18.0
$ apt install kubelet=1.18.0-00
$ kubectl uncordon master
$ kubectl drain node01 --ignore-daemonsets
$ ssh node01
node01 $ apt install kubeadm=1.18.0-00
node01 $ kubeadm upgrade node
node01 $ apt install kubelet=1.18.0-00
node01 $ exit
$ kubectl uncordon node01
```

### Exercise 02

Print the names of all deployments in the admin2406 namespace in the following format:
DEPLOYMENT CONTAINER_IMAGE READY_REPLICAS NAMESPACE
<deployment name> <container image used> <ready replica count> <Namespace>
. The data should be sorted by the increasing order of the deployment name.


Example:
DEPLOYMENT CONTAINER_IMAGE READY_REPLICAS NAMESPACE
deploy0 nginx:alpine 1 admin2406
Write the result to the file /opt/admin2406_data.

Hint: Make use of -o custom-columns and --sort-by to print the data in the required format.

```bash
$ kubectl get  deployments -n admin2406 --sort-by=.metadata.name \
-o=custom-columns=DEPLOYMENT:.metadata.name,\
CONTAINER_IMAGE:.spec.template.spec.containers[0].image,\
READY_REPLICAS:.status.readyReplicas,\
NAMESPACE:.metadata.namespace > /opt/admin2406_data
```

### Exercies 03

A kubeconfig file called admin.kubeconfig has been created in /root. There is something wrong with the configuration. Troubleshoot and fix it.

/root/admin.kubeconfig

```bash
$ kubectl cluster-info
Kubernetes master is running at https://172.17.0.10:6443
KubeDNS is running at https://172.17.0.10:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
```

```yaml
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01EY3lNakUyTlRJME4xb1hEVE13TURjeU1ERTJOVEkwTjFvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTXlxCjU5RnJpMk1XWWFteDAyOFhnb0Q1ZThDOG5SS01rUk5JMXV3azdFRCtaSE95MG9sOG5ES0lKbVRPbk5wL3RnQ1YKcTFHVHdWQXhaUXZFdDlsWTNlRzJqZk4yV3RSOHY4QWZzL1J2bXpYa0FWOFlwQ2xuMHJrWVNmeHRUcHBrV2dUdwo4TUtXdE1nTjdiSSt1Zzg4NFJoNTJZaHlMa3lLVVljcVQvd1VpWUlTd3daTGZ2MUN2aHlFOTJVS3hBZExNYTlKCks1MmNyZ2VJeEtUK3pTRWVmN0V0bUJicklQakhQZm00dlVvYTFPb1ExeFkrWHRVWStlUGJMUWY2VjhCcDJQRksKbWoycTJEbEZaOStWbm9lUk8xQkdLcWdGdit2dis0b3JGWEd1VW9Fc2xxKzJNYmdBWmF0OVFkeUJvZEswR1pvTQo4ZStVN1lKY280RnpEM2NZS1I4Q0F3RUFBYU1qTUNFd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFLMUlTQnNCeHFjWHBvbkZEYWZMVlJiVGFJZTYKU1dqV3drVjlwQWtiakE0RUhvaDE4bWN0T2VqR1dOak4zNm1ZN1ZpT2JzY3YyQ2k1SzFUTzc2S2V3amJpVjFFbApuRVc1empOdlRFQTdHWlRrZGFBUE9tb2laMHlHMkxqMVE0cFYrNEJ4YTdNaTB4V3hReGhoc0RMM2kwWTM0Y0hLClZob2o1d0tBZi85bG95VExWQkNIVVdNcDB2Yis2YXpXNnZJOGR0Uk5DcloxUW16dlhVcktsQzIzeTd2NU9udlgKd3hscmZlVzl3WlVZVzhINll0WFUrOUtsM2hWY3BkbnhBNFd2bEhWdE85cnAzUXQ4V1Q2Z005Zm82Y2xxandkYgpmcmlhSStWZnkyajU5c1RvRzlsc0RaNk5XOWhLR05GWUlubWkweGxTWHBpbCtQWC9SOG8xZU5MNWJmaz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
    server: https://172.17.0.21:2379 -> port
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: kubernetes-admin
  user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUM4akNDQWRxZ0F3SUJBZ0lJU3Zjd05Ia1h5WG93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBM01qSXhOalV5TkRkYUZ3MHlNVEEzTWpJeE5qVXlOVEZhTURReApGekFWQmdOVkJBb1REbk41YzNSbGJUcHRZWE4wWlhKek1Sa3dGd1lEVlFRREV4QnJkV0psY201bGRHVnpMV0ZrCmJXbHVNSUlCSWpBTkJna3Foa2lHOXcwQkFRRUZBQU9DQVE4QU1JSUJDZ0tDQVFFQW0yWS9PQVNvbTU4eGZzUHMKNzF3UFpVcy9XRytxQlJ2LzFRRWRwbGFxRTlTSjhnSGR4OERLMXZHemprTit4clhBVVlBSURJTmZUS1FoOWJMOQpwdm05em1QWW4yUTBXTmxLaFpHZnNKS0FoMlBnQkJjMDVjKzQrUTR4RGNpMkUwVUVqRDlJSXBBeC9GZkptdkVICmNaOCtoMTlrNmZic25BeGc5MnhnN0x4MkoxUnVZSll0WWJFcTI4SS9FMkxOUk5yYUMrZnlmaE14QzV0dHhwRlgKYWtTdlRVMmpFTm03LzFtU1ZyRE5pS2FEMXkrUUU3OHJXYk9WM1RLbHcyRExPZVZ5TEtWNUtSbXBXeWpUbUxrMwpDT0YrQ2Z3QjFuZFBsR3lLMnZHY2Z2SnZpK1BnQ2Z3U2pBWVlFbmtNOUI3QnU5WHJnb3hFRE5HQVZNRy9TYkt4ClVYS2NFUUlEQVFBQm95Y3dKVEFPQmdOVkhROEJBZjhFQkFNQ0JhQXdFd1lEVlIwbEJBd3dDZ1lJS3dZQkJRVUgKQXdJd0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFHMFFmTDFLWEtLS3YwbTVTRjRrNmxoOXNsVDEremxZcTBiMAphUlQwUHl1aHZNNlNHWkM5WU52b0VPUXdvZXhDRU5nQXZic0RZdEFYcUgySk85aEZCeWhobmJiZUZiQ28yMUw0ClJSM1AzOS9mVVoxSTFiZHR3SW10bWlZOWZnMDRvTHFNeWFCNnowYWQ3ZEtxSUx1NnBWblhVYXBMVmtsTjFCK0gKUk5lTVBWQ21pTFA4d2k4K3JMRmRyV2FCbVlxeHBndzAyc0Q3WVRoOHMvazQzN2RKZlhuN1l5VXkzUTR1UkNvbApycnNjalp0Y1E1WFY1VGw4TW9wWk9ucFhhS043dVZPUGEyZFlXKzhnd2t0YjB5NW9MMVYzejlIbEN3NDJzQlZjCnRKckNVVmN5QWhuQWNxV1p0L1Z1MkRNZTlqOTVlUHBDdjMxeDF6U2RBZXJVN2NDcTB0UT0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
    client-key-data: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFcFFJQkFBS0NBUUVBbTJZL09BU29tNTh4ZnNQczcxd1BaVXMvV0crcUJSdi8xUUVkcGxhcUU5U0o4Z0hkCng4REsxdkd6amtOK3hyWEFVWUFJRElOZlRLUWg5Ykw5cHZtOXptUFluMlEwV05sS2haR2ZzSktBaDJQZ0JCYzAKNWMrNCtRNHhEY2kyRTBVRWpEOUlJcEF4L0ZmSm12RUhjWjgraDE5azZmYnNuQXhnOTJ4ZzdMeDJKMVJ1WUpZdApZYkVxMjhJL0UyTE5STnJhQytmeWZoTXhDNXR0eHBGWGFrU3ZUVTJqRU5tNy8xbVNWckROaUthRDF5K1FFNzhyCldiT1YzVEtsdzJETE9lVnlMS1Y1S1JtcFd5alRtTGszQ09GK0Nmd0IxbmRQbEd5SzJ2R2Nmdkp2aStQZ0Nmd1MKakFZWUVua005QjdCdTlYcmdveEVETkdBVk1HL1NiS3hVWEtjRVFJREFRQUJBb0lCQVFDUXhaVzUyMlUwN2Y4UAphUG9RR0VUamlsR3VVd0VqRjIya2hCQ2FTWHpUVXB0YzBXNUxUUW5yYTJ0cTZ1aHR1Ujg5RGpqeFoyWmVJVDdlCkJtMldwRzIybklCUUplczZwdHh0U1RocktHRURRUUdPN2d2QzJWMHE4WlN6a2J4R2dYTDJNWEpEZVBDSWRVU0YKeTJ5eTVyYzdLSno3OXFXQnoycDZ0d1lwSm40d3JsMERnYytVMTJLMHJ6bFQ0NG5Sd1puY0dxd3BXNlQyZytGMwpIdTIxRWdtNS9PUWRoY0p2SGZmQVRsSnVxemhWTURTb3U0ZWpERFhldllhWUh4Vkw3U1ZoUm15VTN3YWlUZE1RCkt5WnA3enBDU0lJOGpwSXBGN2VVM042L09WUU1EQXIvYVNVMDQxNldGNks2aUtMdEl5L0dPMCtNVWQ1Sjk4ZmgKNWxGVGFUc2RBb0dCQU0yaCsxekc3U3JZaHNrbGVFelJ2M2xFemtDVDBtL1cvQWw4WU1PUTRLQmxYbnlVOExnKwozNmljbWtmRSttdzhWTVAvN2hFUlhWYmVocTBkM3dFb3FQRzl6eXNpb3NHNVNSbDhJVkxWaFp0cjVRZHJJYnRGCmdtSmp5cksybmtMaXNjM3l3SHVRWkFUdnc5d0hjYzkvRkU5Z0ZESWR5QTdiUWNKb05YQWwrQ2duQW9HQkFNRjIKYlo1b2V0WExLaUNDb3REM0owbWdKRHNRNW16Q1Vjd2FVazFWRWpOR2hCVkplM1FMSkhZZlI3VHhmNU5rTVhoWQpPbUNpTE96TDdtdHF5MnBsbnlHR0kxdnJYdy9IUVpzQUJxTVp4VnFJazZZZnhpeHQ5OVk1QUptSm9ybFB3VzVxCjJ5N2ZRTHZQdTVUM3BaK1pmOTF0OTMyQlU2d3JCMTJ0R3lTYjBVVUhBb0dCQUxFdU1oelYxc01GUU5ZT2NDS1kKMUFmVXJuYmNLQVZ2MDBrRkg0ZEN6Um5IWG11d0ZkNWJabkd0Umc0eS9xZkdNQytWMVBabDVPd1YzeXRKUXI2SAozMUkwZjA0bWtPQ2xHd1JYZllzWEtnRXFMWFIvN2RlajQ5Mk5LTGxtT3VWYzJXR2dHaFFkN09DSUMxWUtJQmx1CjRQd255QjhLcERlUTJvOTlKUlpWMFFZZkFvR0FWSWJEMmtSWDl2VXZVTWtERGo0T1FsTGZ3WmJldG9Jb2J2N0oKR3RTaWdSaThPUGpFSWEyRW5yK0grdnpRK3JyNkNsU3lIT3ArcW05bm1qTXM4UzI1VzFKYVBKMFlDYkxMYlVlVApuc3R4dU42WGRDdkowRHV6M3p0RWtkRGE4aEJ3dVNlaTRGWFcyTGdvZ3l1NGg0VHZvdldhL3BEMDEvSnM0M2J3CnR3d0ZZUjBDZ1lFQW5aUk1QMElBVHBMUnBaS2w1S3REaVN5b29vT1A0T0V4NlNpOWRoTy9HQkJMRkZ4VUpwTHIKZytMVndnN1lmaWtmQWc5RkpnR2hSRHdpSFVBdkFtTkYzeEVDUEZBMGpPLzJicWU2clpQdklPSkR0RXlzSGlLRgpXL3p5Um05MTZzS2RiaXMzSFNBVUNRMk9MeXIvQ1Q1UGZJdVQrL3hqZVk3bmJZUDRZVC9vU0tZPQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=
```

### Exercise 04

Create a new deployment called nginx-deploy, with image nginx:1.16 and 1 replica. Next upgrade the deployment to version 1.17 using rolling update. Make sure that the version upgrade is recorded in the resource annotation.

```bash
$ kubectl create deployment --image=nginx:1.16 nginx-deploy --dry-run=client -o yaml > nginx-deploy.yaml
$ kubectl apply -f nginx-deploy.yaml
$ kubectl set image deployment/nginx-deploy nginx=nginx:1.17 --record
```

### Exercise 05

A new deployment called alpha-mysql has been deployed in the alpha namespace. However, the pods are not running. Troubleshoot and fix the issue. The deployment should make use of the persistent volume alpha-pv to be mounted at /var/lib/mysql and should use the environment variable MYSQL_ALLOW_EMPTY_PASSWORD=1 to make use of an empty root password.

Important: Do not alter the persistent volume.

```bash
$ kubectl describe pvc -n alpha alpha-claim
$ kubectl edit pvc -n alpha alpha-claim
# Correct AccessMode and StorageClass
$ kubectl get deployments -n alpha
$ kubectl describe deployments alpha-mysql -n alpha
$ kubectl edit deployments alpha-mysql -n alpha
# Correct PVC claim name
```

### Exercise 06

Take the backup of ETCD at the location /opt/etcd-backup.db on the master node

```bash
export ETCDCTL_API=3
etcdctl --endpoints=https://[127.0.0.1]:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key snapshot save /opt/etcd-backup.db
```

### Exercise 07

Create a pod called secret-1401 in the admin1401 namespace using the busybox image. The container within the pod should be called secret-admin and should sleep for 4800 seconds.

The container should mount a read-only secret volume called secret-volume at the path /etc/secret-volume. The secret being mounted has already been created for you and is called dotfile-secret.

```bash
$ kubectl run secret-admin --image=busybox --dry-run -o yaml > secret-admin.yaml
$ kubectl describe secret dotfile-secret -n admin1401
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: secret-1401
  name: secret-1401
  namespace: admin1401
spec:
  containers:
  - image: busybox
    name: secret-admin
    command:
    - sh
    - "-c"
    - sleep 4800
    volumeMounts:
    - name: secret-volume
      mountPath: "/etc/secret-volume"
      readOnly: true
  volumes:
  - name: secret-volume
    secret:
      secretName: dotfile-secret
```