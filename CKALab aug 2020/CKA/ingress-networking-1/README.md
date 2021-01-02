# Practice Test - Ingress Networking - 1

## Exercises

**We have deployed Ingress Controller, resources and applications. Explore the setup.**

> kubectl get all --all-namespaces

**Which namespace is the Ingress Controller deployed in?**

> ingress-space

**What is the name of the Ingress Controller Deployment?**

```bash
master $ kubectl get deployment -n ingress-space
NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
nginx-ingress-controller   1/1     1            1           4m43s
```

**Which namespace are the applications deployed in?**

> app-space

**How many applications are deployed in the app-space namespace?**

```bash
$ kubectl get deployment -n app-space --no-headers | wc -l
3
```

**Which namespace is the Ingress Resource deployed in?**

> kubectl get ingress --all-namespaces

**What is the Host configured on the ingress-resource?**

> kubectl describe ingress --namespace app-space

**You are requested to change the URLs at which the applications are made available.**

> Ingress: ingress-wear-watch, Path: /stream, Backend Service: video-service, Backend Service Port: 8080

`$ kubectl edit ingress --namespace app-space`

**You are requested to add a new path to your ingress to make the food delivery application available to your customers.**

> Ingress: ingress-wear-watch, Path: /eat, Backend Service: food-service, Backend Service Port: 8080

`$ kubectl edit ingress --namespace app-space`

**You are requested to make the new application available at /pay.**

`$ kubectl create -f ingres-pay.yaml`
