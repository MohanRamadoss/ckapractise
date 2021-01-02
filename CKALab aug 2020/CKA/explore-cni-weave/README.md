# Practice Test - Explore CNI Weave

## Exercises

**Inspect the kubelet service and identify the network plugin configured for Kubernetes.**

> Run ps -aux | grep kubelet command

```bash
root      2879  4.1 16.9 497856 345172 ?       Ssl  20:48   2:31 kube-apiserver --advertise-address=172.17.0.9 --allow-privileged=true --authorization-mode=Node,RBAC --client-ca-file=/etc/kubernetes/pki/ca.crt --enable-admission-plugins=NodeRestriction --enable-bootstrap-token-auth=true --etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt --etcd-keyfile=/etc/kubernetes/pki/apiserver-etcd-client.key --etcd-servers=https://127.0.0.1:2379 --insecure-port=0 --kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt --kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt --proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client.key --requestheader-allowed-names=front-proxy-client --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=6443 --service-account-key-file=/etc/kubernetes/pki/sa.pub --service-cluster-ip-range=10.96.0.0/12 --tls-cert-file=/etc/kubernetes/pki/apiserver.crt --tls-private-key-file=/etc/kubernetes/pki/apiserver.key
root     15169  2.6  4.2 1345816 85952 ?       Ssl  21:14   0:54 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --config=/var/lib/kubelet/config.yaml --cgroup-driver=systemd --network-plugin=cni --pod-infra-container-image=k8s.gcr.io/pause:3.1 --cni-bin-dir=/opt/cni/bin
```

**What is the path configured with all binaries of CNI supported plugins?**

> /opt/cni/bin

**Identify which of the below plugins is not available in the list of available CNI plugins on this host**

> Run the command 'ls /opt/cni/bin' and identify the one not present

```bash
cisco is not present
```

**What is the CNI plugin configured to be used on this kubernetes cluster?**

> Run the command ls /etc/cni/net.d/ and identify the name of the plugin

```bash
10-weave.conflist
```

**What binary executable file will be run by kubelet after a container and its associated namespace are created.**

> Look at the type field in file /etc/cni/net.d/10-weave.conflist

```json
{
    "cniVersion": "0.3.0",
    "name": "weave",    
    "plugins": [        
        {            
            "name": "weave",
            "type": "weave-net",
            "hairpinMode": true
        },
        {
            "type": "portmap",
            "capabilities": {"portMappings": true},
            "snat": true
        }
    ]
}
```
