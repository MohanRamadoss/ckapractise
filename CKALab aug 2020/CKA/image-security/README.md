# Practice Test - Image Security

## Exercise

**We have an application running on our cluster. Let us explore it first. What image is the application using?**

```bash
$ kubectl get deployments
$ kubectl get pods
$ kubectl describe pod web-757b88fd8d-66r7g
```

**We decided to use a modified version of the application from an internal private registry. Update the image of the deployment to use a new image from myprivateregistry.com:5000**

> The registry is located at myprivateregistry.com:5000. Don't worry about the credentials for now. We will configure them in the upcoming steps.

```bash
$ kubectl edit deployment web
# myprivateregistry.com:5000/nginx:alpine
```

**Create a secret object with the credentials required to access the registry**

> Name: private-reg-cred, Username: dock_user, Password: dock_password, Server: myprivateregistry.com:5000, Email: dock_user@myprivateregistry.com

```bash
$ kubectl create secret docker-registry private-reg-cred --docker-username=dock_user --docker-password=dock_password --docker-server=myprivateregistry.com:5000 --docker-email=dock_user@myprivateregistry.com
```

**Configure the deployment to use credentials from the new secret to pull images from the private registry**

```bash
$ kubectl edit deploy web
# add imagePullSecrets section. Use private-reg-cred
```
