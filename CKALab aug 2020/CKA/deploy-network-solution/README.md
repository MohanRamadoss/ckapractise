# Practice Test - Deploy Network Solution

## Exercises

**Deploy weave-net networking solution to the cluster**

> Check the [documentation](https://www.weave.works/docs/net/latest/kubernetes/kube-addon/)

`$ kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"`