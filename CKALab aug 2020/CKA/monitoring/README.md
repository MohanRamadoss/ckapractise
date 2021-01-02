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
$ kubectl top node
```

**Identify the POD that consumes the most Memory.**

```bash
$ kubectl top pod
```
