# Practice Test - Labels, Selectors and Annotations

## Practice files

* [replicaset-definition-1.yaml](replicaset-definition-1.yaml)

## Exercises

**We have deployed a number of PODs. They are labelled with 'tier', 'env' and 'bu'. How many PODs exist in the 'dev' environment?**

```bash
$ kubectl get pods --selector env=dev --no-headers | wc -l
```

**How many PODs are in the 'finance' business unit ('bu')?**

```bash
$ kubectl get pods --selector bu=finance --no-headers | wc -l
```

**How many objects are in the 'prod' environment including PODs, ReplicaSets and any other objects?**

```bash
$ kubectl get all --selector env=prod --no-headers | wc -l
```

**Identify the POD which is 'prod', part of 'finance' BU and is a 'frontend' tier?**

```bash
$ kubectl get pod --selector env=prod,bu=finance,tier=frontend
```

**A ReplicaSet definition file is given 'replicaset-definition-1.yaml'. Try to create the replicaset. There is an issue with the file. Try to fix it.**

```bash
# Set the labels on the pod definition template to frontend
$ kubectl apply -f replicaset-definition-1.yaml
```
