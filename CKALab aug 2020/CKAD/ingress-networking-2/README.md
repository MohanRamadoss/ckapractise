# Practice Test - Ingress Networking - 2

## Exercises

**We have deployed two applications. Explore the setup.**

> kubectl get all --all-namespaces

**Let us now deploy an Ingress Controller. First, create a namespace called 'ingress-space'**

> kubectl create namespace ingress-space

**The NGINX Ingress Controller requires a ConfigMap object. Create a ConfigMap object in the ingress-space**

> ConfigMap name: nginx-configuration

> kubectl create configmap nginx-configuration --namespace ingress-space

**The NGINX Ingress Controller requires a ServiceAccount. Create a ServiceAccount in the ingress-space.**

> Service Account Name: ingress-serviceaccount

> kubectl create serviceaccount ingress-serviceaccount --namespace ingress-space

**We have created the Roles and RoleBindings for the ServiceAccount. Check it out!**

> kubectl get roles,rolebindings --namespace ingress-space

**Let us now deploy the Ingress Controller. Create a deployment using the file given**

> Deployed in the correct namespace., Replicas: 1, Use the right image

`$ kubectl create -f ingress-controller.yaml`

**Let us now create a service to make Ingress available to external users.**

> Name: ingress, Type: NodePort, Port: 80, TargetPort: 80, NodePort: 30080, Use the right selector

> kubectl expose deployment -n ingress-space ingress-controller --type=NodePort=30080 --port=80 --name=ingress --dry-run -o yaml > ingress-service.yaml

**Create the ingress resource to make the applications available at /wear and /watch on the Ingress service.**

> Create the ingress in the app-space 