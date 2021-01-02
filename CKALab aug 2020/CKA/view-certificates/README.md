# Practice Test - View Certificates

## Exercises

**Identify the certificate file used for the kube-api server**

```bash
$ ps aux | grep kube-apiserver
# --tls-cert-file=/etc/kubernetes/pki/apiserver.crt
```

**Identify the Certificate file used to authenticate kube-apiserver as a client to ETCD Server**

```bash
$ cat /etc/kubernetes/manifests/kube-apiserver.yaml
# or
$ ps aux | grep kube-apiserver
# --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt
```

**Identify the key used to authenticate kubeapi-server to the kubelet server**

```
# --kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key
```

**Identify the ETCD Server Certificate used to host ETCD server**

```bash
# cat  /etc/kubernetes/manifests/etcd.yaml
# --cert-file=/etc/kubernetes/pki/etcd/server.crt
```

**Identify the ETCD Server CA Root Certificate used to serve ETCD Server**

> ETCD can have its own CA. So this may be a different CA certificate than the one used by kube-api server.

```bash
# --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
```

**What is the Common Name (CN) configured on the Kube API Server Certificate?**

> openssl x509 -in file-path.crt -text -noout

```bash
# openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text -noout
```

**What is the Common Name (CN) configured on the ETCD Server certificate?**

```bash
# openssl x509 -in /etc/kubernetes/pki/etcd/server.crt -text -noout
```

**How long, from the issued date, is the Root CA Certificate valid for?**

```bash
$ openssl x509 -in /etc/kubernetes/pki/ca.crt -text -noout
```

**Kubectl suddenly stops responding to your commands. Check it out!**

> Someone recently modified the /etc/kubernetes/manifests/etcd.yaml file
> You are asked to investigate and fix the issue. Once you fix the issue wait for sometime for kubectl to respond. Check the logs of the ETCD container.

```bash
# inspect the --cert-file in /etc/kubernetes/manifests/etcd.yaml
```

**The kube-api server stopped again! Check it out. Inspect the kube-api server logs and identify the root cause and fix the issue.**

> Run docker ps -a command to identify the kube-api server container. Run docker logs container-id command to view the logs.

```bash
# docker ps -a
# docker inspect <container_id>
```
