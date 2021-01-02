# Practice Test - KubeConfig

## Exercises

**Where is the default kubeconfig file located in the current environment?**

```bash
$ echo $HOME
$ stat $HOME/.kube/config
```

**How many clusters are defined in the default kubeconfig file?**

```bash
$ cat /root/.kube/
```

**I would like to use the dev-user to access test-cluster-1. Set the current context to the right one so I can do that.**

> Once the right context is identified, use the 'kubectl config use-context' command.

```bash
$ kubectl config --kubeconfig=/root/my-kube-config use-context research
```

**We don't want to have to specify the kubeconfig file option on each command. Make the my-kube-config file the default kubeconfig.**

```bash
$ cp /root/my-kube-config $HOME/.kube/config
```

**With the current-context set to research, we are trying to access the cluster. However something seems to be wrong. Identify and fix the issue.**

> Try running the kubectl get pods command and look for the error. All users certificates are stored at /etc/kubernetes/pki/users
