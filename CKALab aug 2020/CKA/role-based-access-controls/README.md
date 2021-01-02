# Practice Test - Role Based Access Controls (RBAC)

## Exercises

**Inspect the environment and identify the authorization modes configured on the cluster.**

> Check the kube-api server settings

```bash
$ kubectl describe pod kube-apiserver-master -n kube-system
# check --authorization-mode
```

**How many roles exist in the default namespace?**

```bash
$ kubectl get roles
# in all namespaces
$ kubectl get roles --all-namespaces
```

**What are the resources the kube-proxy role in the kube-system namespce is given access to?**

```bash
$ kubectl describe role kube-proxy -n kube-system
```

**What actions can the kube-proxy role perform on configmaps**

> Check the verbs associated to the kube-proxy role

**Which account is the kube-proxy role assigned to it?**

```bash
$ kubectl describe rolebinding kube-proxy -n kube-system
```

**A user dev-user is created. User's details have been added to the kubeconfig file. Inspect the permissions granted to the user. Check if the user can list pods in the default namespace.**

> Use the --as dev-user option with kubectl to run commands as the dev-user

```bash
$ kubectl get pods --as dev-user
```
**Create the necessary roles and role bindings required for the dev-user to create, list and delete pods in the default namespace.**

> Role: developer, Role Resources: pods, Role Actions: list, Role Actions: create, RoleBinding: dev-user-binding, RoleBinding: Bound to dev-user

```bash
$ kubectl create -f developer-role.yaml
```

**The dev-user is trying to get details about the dark-blue-app pod in the blue namespace. Investigate and fix the issue.**

```bash
$ kubectl edit role developer --namespace=blue
```

**Grant the dev-user permissions to create deployments in the blue namespace.**

> Remember to add both groups "apps" and "extensions"

```bash
$ kubectl create -f dev-user-deploy.yaml
```
