# Practice Test - Service Account

## Exercises

**How many Service Accounts exist in the default namespace?**

`$ kubectl get serviceaccounts`

**What is the secret token used by the default service account?**

`$ kubectl describe serviceaccount default`

**The application needs a ServiceAccount with the Right permissions to be created to authenticate to Kubernetes. The 'default' ServiceAccount has limited access. Create a new ServiceAccount named 'dashboard-sa'.**

`$ kubectl create serviceaccount dashboard-sa`

**Additional permissions for the newly created 'dashboard-sa' account using RBAC were applied**

> check the files

**Retrieve the Authorization token for the newly created service account.**

**Update the deployment to use the newly created ServiceAccount**

> Deployment name: web-dashboard, Service Account: dashboard-sa

`$ kubectl edit deployment web-dasboard`

```yaml
spec:
  serviceAccountName: my-serviceaccount
```
