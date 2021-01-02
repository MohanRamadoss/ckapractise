# Manual Testing

* kubectl get nodes
* kubectl get pods
* kubectl get pods -n kube-system
* service kube-apiserver status
* service kube-controller-manager status
* service kube-scheduler status
* service kubelet status
* service kube-proxy status

## Deploy a nginx app

* kubectl run nginx
* kubectl get pods
* kubectl scale --replicas=3 deploy/nginx
* kubectl get pods
* kubectl expose deployment nginx --port=80 --type=NodePort
* kubectl get service
* curl http://node01:nodePort
