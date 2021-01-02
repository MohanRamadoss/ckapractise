# Practice Test - Deployments

## Deployment definition

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
  labels:
    app: myapp
    tier: front-end
spec:
  replicas: 3
  template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
        type: front-end
    spec:
      containers:
        - name: nginx-container
          image: nginx
  selector:
    matchLabels:
      type: front-end
```

## Practice files

* [deployment-definition-1.yaml](deployment-definition-1.yaml)

## Exercises

**How many Deployments exist in the current(default) namespace**

```bash
$ kubectl get deployments
```

**Create a new Deployment using the '[deployment-definition-1.yaml](deployment-definition-1.yaml)' file. There is an issue with the file, so try to fix it.**

```bash
# The value for 'kind' is incorrect. It should be 'Deployment' with a capital 'D'.
$ kubectl create -f deployment-definition-1.yaml
```

**Create a new Deployment with the below attributes using your own deployment definition file**

> Name: httpd-frontend; Replicas: 3; Image: httpd:2.4-alpine

```bash
$ kubectl create deployment --image=httpd:2.4-alpine httpd-frontend --dry-run=client -o yaml > httpd-frontend-deployment.yaml
# Edit httpd-frontend-deployment.yaml and add replicas: 3
$ kubectl create -f httpd-frontend-deployment.yaml
```

**Generate Deployment YAML file (-o yaml). Don't create it(--dry-run)**

```bash
$ kubectl create deployment --image=nginx nginx --dry-run -o yaml
```

** Generate Deployment YAML file (-o yaml). Don't create it(--dry-run) with 4 Replicas (--replicas=4)**

```bash
$ kubectl create deployment --image=nginx nginx --dry-run -o yaml > nginx-deployment.yaml
```
