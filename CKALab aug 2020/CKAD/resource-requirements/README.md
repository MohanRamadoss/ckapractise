# Practice Test - Resource Requirements

## Exercises

**A pod named 'rabbit' is deployed. Identify the CPU requirements set on the Pod**

`$ kubectl describe pod rabbit`

**Delete the 'rabbit' Pod.**

`$ kubectl delete pod rabbit`

**The elephant runs a process that consume 15Mi of memory. Increase the limit of the elephant pod to 20Mi.**

> Pod Name: elephant, Image Name: polinux/stress, Memory Limit: 20Mi

```bash
$ kubectl get pod elephant -o yaml > elephant.yaml
# Edit the YAML file and increase memory limit
$ kubectl delete -f elephant.yaml
$ kubectl create -f elephant.yaml
```
