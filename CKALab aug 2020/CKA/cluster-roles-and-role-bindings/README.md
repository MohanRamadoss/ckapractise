# Practice Test - Cluster Roles and Role Bindings

## Exercises

**How many ClusterRoles do you see defined in the cluster?**

```bash
$ kubectl get clusterroles --no-headers | wc -l
# or
$ kubectl get clusterroles --no-headers -o json | jq '.items | length'
```

**How many ClusterRoleBindings exist on the cluster?**

```bash
$ kubectl get clusterrolebindings --no-headers | wc -l
# or
$ kubectl get clusterrolebindings --no-headers -o json | jq '.items | length'
```

**What namespace is the cluster-admin clusterrole part of?**

```bash
$ kubectl describe clusterrole cluster-admin
```

**What user/groups are the cluster-admin role bound to?**

```bash
$ kubectl describe clusterrolebinding cluster-admin
```

**What level of permission does the cluster-admin role grant?**

> Inspect the cluster-admin role's privileges

```bash
$ kubectl describe clusterrole cluster-admin
```

**A new user michelle joined the team. She will be focusing on the nodes in the cluster. Create the required ClusterRoles and ClusterRoleBindings so she gets access to the nodes.**

```bash
$ kubectl create -f michelle-node-admin.yaml
```

**michelle's responsibilities are growing and now she will be responsible for storage as well. Create the required ClusterRoles and ClusterRoleBindings to allow her access to Storage.**

> Get the API groups and resource names from command kubectl api-resources. Use the given spec.

> ClusterRole: storage-admin, Resource: persistentvolumes, Resource: storageclasses, ClusterRoleBinding: michelle-storage-admin, ClusterRoleBinding Subject: michelle, ClusterRoleBinding Role: storage-admin

```bash
$ kubectl create -f michelle-storage-admin.yaml
```
