# Practice Test - Monitoring

## Exercises

**Let us deploy metrics-server to monitor the PODs and Nodes. Pull the git repository for the deployment files.**

```bash
$ git clone https://github.com/kodekloudhub/kubernetes-metrics-server.git
```

**Deploy the metrics-server by creating all the components downloaded.**

```bash
$ kubectl create -f .
```

**It takes a few minutes for the metrics server to start gathering data.**

```bash
$ kubectl top nodes
```

**Identify the POD that consumes the most Memory.**

```bash
$ kubectl top pods
```

**Debugging in Kubernetes**

They want you to locate the problem and collect some relevant debugging information that will help the team analyze the problem and correct it in the future. They also want you to go ahead and get the broken pod running again.

You will need to do the following:

* Find the broken pod and save the pod name to the file /home/cloud_user/debug/broken-pod-name.txt.
* In the same namespace as the broken pod, find out which pod is using the most CPU and output the name of that pod to the file /home/cloud_user/debug/high-cpu-pod-name.txt.
* Get the broken pod's summary data in the JSON format and output it to the file /home/cloud_user/debug/broken-pod-summary.json.
* Get the broken pod's container logs and output them to the file /home/cloud_user/debug/broken-pod-logs.log.
* Fix the problem with the broken pod so that it enters the Running state.

```bash
$ kubectl get pods --all-namespaces
$ kubectl -n <namespace> top pod
$ kubectl -n <namespace> get pod <podname> -o json
$ kubectl -n <namespace> logs <podname>
$ kubectl -n <namespace> get pod <podname> -o yaml > <podname>.yaml
# Edit YAML file

```