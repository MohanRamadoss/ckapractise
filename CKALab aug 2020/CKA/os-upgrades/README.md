# Practice Test - OS Upgrades

## Exercises

**How many applications do you see hosted on the cluster?**

> Check the number of deployments

```bash
$ kubectl get deployments
```

**We need to take node01 out for maintenance. Empty the node of all applications and mark it unschedulable.**

```bash
$ kubectl drain node01 --ignore-daemonsets
```

**The maintenance tasks have been completed. Configure the node to be schedulable again.**

```bash
$ kubectl uncordon node01
```

**Node03 has our critical applications. We do not want to schedule any more apps on node03. Mark node03 as unschedulable but do not remove any apps currently running on it .**

```bash
$ kubectl cordon node03
```
