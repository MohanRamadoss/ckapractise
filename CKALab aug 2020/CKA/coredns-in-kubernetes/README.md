# Practice Test - CoreDNS in Kubernetes

## Exercises

**Identify the DNS solution implemented in this cluster.**

> Run the command kubectl get pods -n kube-system and look for the DNS pods

```bash
$ kubectl get pods -n kube-system --no-headers | grep dns
coredns-66bff467f8-kpb7l                  1/1   Running   0     8m32s
coredns-66bff467f8-w47q4                  1/1   Running   0     8m32s
```

**What is the name of the service created for accessing CoreDNS?**

> Run the command kubectl get service -n kube-system

```bash
$ kubectl get service -n kube-system
NAME       TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                  AGE
kube-dns   ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP,9153/TCP   11m
```

**What is the IP of the CoreDNS server that should be configured on PODs to resolve services?**

> Run the command kubectl get service -n kube-system and look for cluster IP value.

**Where is the configuration file located for configuring the CoreDNS service?**

> Inspect the Args of the coredns deployment and check the file used, example: kubectl -n kube-system describe deployments.apps coredns | grep -A2 Args | grep Corefile

```bash
$ kubectl -n kube-system describe deployments.apps coredns | grep -A2 Args | grep Corefile
/etc/coredns/Corefile
```

**How is the Corefile passed in to the CoreDNS POD?**

> ConfigMap

**What is the name of the ConfigMap object created for Corefile?**

> Run the command kubectl get configmap -n kube-system

```bash
$ kubectl get configmap -n kube-system
NAME                                 DATA   AGE
coredns                              1      18m
extension-apiserver-authentication   6      18m
kube-flannel-cfg                     2      18m
kube-proxy                           2      18m
kubeadm-config                       2      18m
kubelet-config-1.18                  1      18m
vip-configmap                        0      18m
```

**What is the root domain/zone configured for this kubernetes cluster?**

> Run the command kubectl describe configmap coredns -n kube-system and look for the entry after kubernetes

```json
kubernetes cluster.local in-addr.arpa ip6.arpa {
   pods insecure
   fallthrough in-addr.arpa ip6.arpa
   ttl 30
}
```

**What name can be used to access the hr web server from the test Application?**

> You can execute a curl command on the test pod to test. Alternatively, the test Application also has a UI. Access it using the tab at the top of your terminal named"test-app"

`$ kubectl exec test -- curl http://web-service`

**Which of the below name can be used to access the payroll service from the test application?**

> web-service.payroll

** Which of the below name CANNOT be used to access the payroll service from the test application?**

> web-service.payroll.svc.cluster

**We just deployed a web server - webapp - that accesses a database mysql - server. However the web server is failing to connect to the database server. Troubleshoot and fix the issue.**

> They could be in different namespaces. First locate the appliations. The web server interface can be seen by clicking the tab Web Server at the top of your terminal.

> Set the DB_Host environment variable to use mysql.payroll

**From the hr pod nslookup the mysql service and redirect the output to a file /root/nslookup.out**

> Run the command kubectl exec -it hr nslookup mysql.payroll > /root/nslookup.out

`$ kubectl exec hr -- nslookup mysql.payroll > /root/nslookup.out`
