# Practice Test - Static PODs

Kubelet periodically checks the content of `/etc/kubernetes/manifests` folder and creates pods if find any pods definitions.

## Exercises

**How many static pods exist in this cluster in all namespaces?**

```bash
kubectl get pods --all-namespaces
# look for those with -master appended in the name 
```

**What is the path of the directory holding the static pod definition files?**

```bash
ps -aux | grep kubelet
# dentify the config file - --config=/var/lib/kubelet/config.yaml
# Then checkin the config file for staticPdPath.
```

**Create a static pod named static-busybox that uses the busybox image and the command sleep 1000**

```bash
kubectl run --restart=Never --image=busybox static-busybox --dry-run -o yaml --command -- sleep 1000 > /etc/kubernetes/manifests/static-busybox.yaml
```

**We just created a new static pod named static-greenbox. Find it and delete it.**

Identify which node the static pod is created on, ssh to the node and delete the pod definition file. If you don't know theIP of the node, run the kubectl get nodes -o wide command and identify the IP. Then SSH to the node using that IP. For static pod manifest path look at the file /var/lib/kubelet/config.yaml on node01
