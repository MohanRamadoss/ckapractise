# Practice Test - Secrets

## A note about Secrets!

Remember that secrets encode data in base64 format. Anyone with the base64 encoded secret can easily decode it. As such the secrets can be considered as not very safe.

Secrets are not encrypted, so it is not safer in that sense. However, some best practices around using secrets make it safer. As in best practices like:

* Not checking-in secret object definition files to source code repositories.
* [Enabling Encryption at Rest](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/) for Secrets so they are stored encrypted in ETCD.  

Also the way kubernetes handles secrets. Such as:

* A secret is only sent to a node if a pod on that node requires it.
* Kubelet stores the secret into a tmpfs so that the secret is not written to disk storage.
* Once the Pod that depends on the secret is deleted, kubelet will delete its local copy of the secret data as well.

there are other better ways of handling sensitive data like passwords in Kubernetes, such as using tools like Helm Secrets, HashiCorp Vault.

## Exercises

**How many Secrets exist on the system?**

```bash
$ kubectl get secrets
```

**How many secrets are defined in the 'default-token' secret?**

```bash
$ kubectl describe secret default-token-xk2jm
```

**What is the type of the 'default-token' secret?**

> kubernetes.io/service-account-token


**Create a new Secret named 'db-secret' with the data given.**

> Secret Name: db-secret, Secret 1: DB_Host=sql01, Secret 2: DB_User=root, Secret 3: DB_Password=password123

```bash
$ kubectl create secret generic db-secret --from-literal=DB_Host=sql01 --from-literal=DB_User=root --from-literal=DB_Password=password123
```

**Configure webapp-pod to load environment variables from the newly created secret.**

> Pod name: webapp-pod,  Image name: kodekloud/simple-webapp-mysql,  Env From: Secret=db-secret

Add:

```yaml
envFrom:
  - secretRef:
      name: db-secret
```