# Mock Exam 3

## Exercise 01

Create a new service account with the name pvviewer. Grant this Service account access to list all PersistentVolumes in the cluster by creating an appropriate cluster role called pvviewer-role and ClusterRoleBinding called pvviewer-role-binding.
Next, create a pod called pvviewer with the image: redis and serviceAccount: pvviewer in the default namespace

```bash
# Create a new service account pvviewer
kubectl create serviceaccount pvviewer
# Create a cluster role with accesst to PV pvviewer-role
kubectl create clusterrole pvviewer-role --verb=list --resource=persistentvolumes
# Create a cluster role binding pvviewer-role-binding
kubectl create clusterrolebinding pvviewer-role-binding --clusterrole=pvviewer-role --serviceaccount=default:pvviewer
# Create a pod called pvviewer with image redis and service account
kubectl run pvviewer --image=redis --dry-run=client -o yaml > pvviewer-pod.yaml
```

pvviewer-pod.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: pvviewer
  name: pvviewer
spec:
  serviceAccountName: pvviewer
  containers:
  - image: redis
    name: pvviewer
  restartPolicy: Always
```

## Exercise 02

List the InternalIP of all nodes of the cluster. Save the result to a file /root/node_ips

> Answer should be in the format: InternalIP of master<space>InternalIP of node1<space>InternalIP of node2<space>InternalIP of node3 (in a single line)

```bash
kubectl get nodes -o wide 
# JSONPath
kubectl get nodes -o jsonpath='{.items[*].status.addresses[?(@.type=="InternalIP")].address}' > /root/node_ips
```

## Exercise 03

Create a pod called multi-pod with two containers.
Container 1, name: alpha, image: nginx
Container 2: beta, image: busybox, command sleep 4800.

> Environment Variables: container 1: name: alpha, Container 2: name: beta


```bash
kubectl run multi-pod --image=nginx --dry-run=client -o yaml > multi-pod.yaml
# Edit multi-pod.yaml and run
kubectl create -f multi-pod.yaml
```

multi-pod.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: multi-pod
  name: multi-pod
spec:
  containers:
  - image: nginx
    name: alpha
    env:
    - name: name
      value: alpha
  - image: busybox
    name: beta
    command:
    - sleep
    - "4800"
    env:
    - name: name
      value: beta
  restartPolicy: Always
```

## Exercise 04

Create a Pod called non-root-pod, image: redis:alpine

> runAsUser: 1000, fsGroup: 2000

```bash
kubectl run non-root-pod --image=redis:alpine --dry-run=client -o yaml > non-root-pod.yaml
# Edit non-root-pod.yaml
```

non-root-pod.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: non-root-pod
  name: non-root-pod
spec:
  securityContext:
    runAsUser: 1000
    fsGroup: 2000
  containers:
  - image: redis:alpine
    name: non-root-pod
    securityContext:
      allowPrivilegeEscalation: false
  restartPolicy: Always
```

Create a Pod called lion with image redis:alpine. Set the CPU Limit to 2 CPU and Memory Limit to 500MiB

```bash
kubectl run lion --image=redis:alpine --dry-run=client -o yaml > lion-pod.yaml
# Edit lion-pod.yaml
```

lion-pod.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: lion-pod
  name: lion-pod
spec:
  containers:
  - image: redis:alpine
    name: lion-pod
    resources:
      limits:
        memory: "500MiB"
        cpu: "2"
  restartPolicy: Always
```

### Exercise 05

We have deployed a new pod called np-test-1 and a service called np-test-service. Incoming connections to this service are not working. Troubleshoot and fix it.
Create NetworkPolicy, by the name ingress-to-nptest that allows incoming connections to the service over port 80

> Important: Don't delete any current objects deployed.


```bash
kubectl get pods -o wide
kubectl describe pod np-test-1
kubectl describe service np-test-service
# Test connectivity via busybox
kubectl run test --image=busybox:1.28 --rm -it -- sh
# Run commands inside the container
nc -z -v -w 2 np-test-service 80
# Connection timeout out
# Check default network policy
kubectl get netpol
kubeclt describe netpol default-deny
# No traffic is allowed
# Create a new network policy
kubectl api-versions | grep network
networking.k8s.io/v1
networking.k8s.io/v1beta1
```

ingress-to-nptest.yaml

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: ingress-to-nptest
  namespace: default
spec:
  podSelector:
    matchLabels:
      run: np-test-1
  policyTypes:
  - Ingress
  ingress:
  - ports:
    - protocol: TCP
      port: 80
```

## Exercise 06

Taint the worker node node01 to be Unschedulable. Once done, create a pod called dev-redis, image redis:alpine to ensure workloads are not scheduled to this worker node. Finally, create a new pod called prod-redis and image redis:alpine with toleration to be scheduled on node01.

> key:env_type, value:production, operator: Equal and effect:NoSchedule

```bash
$ Taint the worker node node01 to be Unschedulable
kubectl taint node node01 env_type=production:NoSchedule
kubectl describe node node01 | grep -i taint
kubectl run dev-redis --image=redis:alpine
kubectl run prod-redis --image=redis:alpine --dry-run=client -o yaml > prod-redis.yaml
# Edit prod-redis.yaml  
```

prod-redis.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: prod-redis
  name: prod-redis
spec:
  containers:
  - image: redis:alpine
    name: prod-redis
  restartPolicy: Always
  tolerations:
  - key: env_type
    value: production
    operator: Equal
    effect: NoSchedule
```

## Exercise 07

Create a pod called hr-pod in hr namespace belonging to the production environment and frontend tier image: redis:alpine

> Use appropriate labels and create all the required objects if it does not exist in the system already.

```bash
kubectl get namespace
kubectl create ns hr
kubectl run hr-pod --image=redis:alpine --dry-run=client --labels='environment=production,tier=frontend' -o yaml > hr-pod.yaml
# Edit and add namespace to yaml file
```

hr-pod.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    environment: production
    tier: frontend
  name: hr-pod
  namespace: hr
spec:
  containers:
  - image: redis:alpine
    name: hr-pod
  restartPolicy: Always
```

## Exercise 08

A kubeconfig file called super.kubeconfig has been created in /root. There is something wrong with the configuration. Troubleshoot and fix it.

```bash
kubectl cluster-info
Kubernetes master is running at https://172.17.0.30:6443
KubeDNS is running at https://172.17.0.30:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
kubectl cluster-info --kubeconfig=/root/super.kubeconfig
# API is exposed on wrong port in config
kubectl config --help
kubectl config --kubeconfig=/root/super.kubeconfig use-context research
```

## Exercise 09

We have created a new deployment called nginx-deploy. scale the deployment to 3 replicas. Has the replica's increased? Troubleshoot the issue and fix it.

```bash
kubectl get deployments
kubectl describe deployment nginx-deploy
kubectl scale --replicas=3 deployment/nginx-deploy
kubectl -n kube-system get pods
cd /etc/kubernetes/manifests
vi kube-controller-manager.yaml and correct what's wrong
run again the scale command
```