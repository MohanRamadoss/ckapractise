# Practice - JSON Path

## JSON PATH exercises with Kubernetes Data Objects

* [https://mmumshad.github.io/json-path-quiz/index.html#!/?questions=questionskub1](https://mmumshad.github.io/json-path-quiz/index.html#!/?questions=questionskub1)
* [https://mmumshad.github.io/json-path-quiz/index.html#!/?questions=questionskub2](https://mmumshad.github.io/json-path-quiz/index.html#!/?questions=questionskub2)


## Exercises

```bash
$ kubectl get pods -o yaml
$ kubectl get pods -o=jsonpath='{ .items[0].spec.containers[0].image }'
$ kubectl get nodes -o=jsonpath='{ .items[*].metadata.name }'
# { "\n" } New line
# { "\t" } New tab
# Loops
$ kubectl get nodes -o=jsonpath='{range .items[*]} { .metadata.name } { "\t" } {end}'
```

## Advanced Kubectl Commands

**Get the list of nodes in JSON format and store it in a file at /opt/outputs/nodes.json**

`$ kubectl get nodes -o json > /opt/outputs/nodes.json`

**Get the details of the node node01 in json format and store it in the file /opt/outputs/node01.json**

`$ kubectl get node node01 -o json > /opt/outputs/node01.json`

**Use JSON PATH query to fetch node names and store them in /opt/outputs/node_names.txt**

```bash
cat nodes.json | jq '.items[].metadata.name'
```
`$ kubectl get nodes -o=jsonpath='{ .items[*].metadata.name }' > /opt/outputs/node_names.txt`

**Use JSON PATH query to retrieve the osImages of all the nodes and store it in a file /opt/outputs/nodes_os.txt**

> The osImages are under the nodeInfo section under status of each node.

```bash
$ cat nodes.json | jq '.items[].status.nodeInfo.osImage'
$ kubectl get nodes -o=jsonpath='{ .items[*].status.nodeInfo.osImage }' > /opt/outputs/nodes_os.txt'
```

**A kube-config file is present at /root/my-kube-config. Get the user names from it and store it in a file /opt/outputs/users.txt**

> Use the command kubectl config view --kubeconfig=/root/my-kube-config to view the custom kube-config

```bash
$ kubectl config view --kubeconfig=/root/my-kube-config -o json | jq '.users[].name'
$ kubectl config view --kubeconfig=/root/my-kube-config -o=jsonpath='{ .users[*].name }' > /opt/outputs/users.txt
```

**A set of Persistent Volumes are available. Sort them based on their capacity and store the result in the file /opt/outputs/storage-capacity-sorted.txt**

```bash
$ kubectl get pv -o json | jq '.items[].spec.capacity.storage'
$ kubectl get pv --sort-by=.spec.capacity.storage > /opt/outputs/storage-capacity-sorted.txt
```

**That was good, but we don't need all the extra details. Retrieve just the first 2 columns of output and store it in /opt/outputs/pv-and-capacity-sorted.txt**

> The columns should be named NAME and CAPACITY. Use the custom-columns option. And remember it should still be sorted as in the previous question.

`$ kubectl get pv --sort-by=.spec.capacity.storage -o=custom-columns=NAME:.metadata.name,CAPACITY:.spec.capacity.storage > /opt/outputs/pv-and-capacity-sorted.txt`

**Use a JSON PATH query to identify the context configured for the aws-user in the my-kube-config context file and store the result in /opt/outputs/aws-context-name.**

```bash
$ kubectl config view --kubeconfig=/root/my-kube-config -o json | jq '.contexts[].context | select(.user =="aws-user")'
$ kubectl config view --kubeconfig=/root/my-kube-config -o jsonpath="{.contexts[?(@.context.user=='aws-user')].name}" > /opt/outputs/aws-context-name
```
