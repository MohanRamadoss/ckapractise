# Practice Test - Cluster Installation using kubeadm

## Exercises

**Install the kubeadm package on master and node01**

```bash
sudo apt-get update && sudo apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

**What is the version of kubelet installed?**

`$kubelet version`

**Initialize Control Plane Node (Master Node)**

> Once done, set up the default kubeconfig file and wait for node to be part of the cluster.

`$kubeadm init`

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

**Join node01 to the cluster using the join token**

`$ kubeadm join 172.17.0.47:6443 --token 3vxytd.g5eanpd3xrolz6zu --discovery-token-ca-cert-hash sha256:55e095c0d04ec761a33a5241805a72719e5c6b2008c217f0e8dd3afef19ffea2`

**Install a Network Plugin. As a default, we will go with weave**

```bash 
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
```
