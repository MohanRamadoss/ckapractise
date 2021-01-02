# Practice Test - Multiple Schedulers

Deploy Additional Scheduler

```bash
wget https://storage.googleapis.com/kubernetes-release/release/v1.12.0/bin/linux/amd64/kube-scheduler
```

my-custom-scheduler.service

```bash
ExecStart=/usr/local/bin/kube-scheduler \\
--config=/etc/kubernetes/config/kube-scheduler.yaml \\
--scheduler-name= my-custom-scheduler
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-custom-scheduler
  namespace: kube-system 
spec:
  containers:
    - command:
      - kube scheduler
      - --address=127.0.0.1
      - --kubeconfig=/etc/kubernetes/scheduler.conf
      - --leader-elect=true
      image: k8s.gcr.io/kube-scheduler-amd64:v1.11.3
      name: kube scheduler
```

## Practice files

* [kube-scheduler.yaml](kube-scheduler.yaml)
* [my-scheduler.yaml](my-scheduler.yaml)

## Exercises

**What is the name of the POD that deploys the default kubernetes scheduler in this environment?**

```bash
$ kubectl get pods --namespace=kube-system
```

**What is the image used to deploy the kubernetes scheduler?**

```bash
$ kubectl describe pod kube-scheduler-master --namespace=kube-system
```

**Deploy an additional scheduler to the cluster following the given specification.**

> Use the manifest file used by kubeadm tool. Use a different port than the one used by the current one.

**A POD definition file is given. Use it to create a POD with the new custom scheduler.**

