# Practice Test - Networking Weave

## Exercises

**What is the Networking Solution used by this cluster?**

> Check the config file located at /etc/cni/net.d/

`$ cat /etc/cni/net.d/10-weave.conflist`

**How many weave agents/peers are deployed in this cluster?**

> Run the command kubectl get pods -n kube-system and count weave pods

`$ kubectl get pods -n kube-system --no-headers | grep weave | wc -l`

**On which nodes are the weave peers present?**

`$ kubectl get pods -n kube-system --no-headers -o wide | grep weave`

**Identify the name of the bridge network/interface created by weave on each node**

> Run the command ip link

`$ ip link show weave`

**What is the POD IP address range configured by weave?**

> Run the command 'ip addr show weave'

`ip addr show weave`

```bash
9: weave: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1376 qdisc noqueue state UP group default qlen 1000
    link/ether 6a:d3:47:eb:80:2e brd ff:ff:ff:ff:ff:ff
    inet 10.46.0.0/12 brd 10.47.255.255 scope global weave
       valid_lft forever preferred_lft forever
    inet6 fe80::68d3:47ff:feeb:802e/64 scope link
       valid_lft forever preferred_lft forever
```

**What is the default gateway configured on the PODs scheduled on node03?**

> Try scheduling a pod on node03 and check ip route output

