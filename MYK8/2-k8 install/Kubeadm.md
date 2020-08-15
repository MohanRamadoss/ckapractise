# Installing Kubernetes cluster using Kubeadm
- This is an alternative way of spinning up a Kubernetes cluster.
- Kubeadm is a toolkit by K8s to create a Cluster
- Works on any dep/rpm compatible Linux distro
  - This is the main advantage of Kubeadm
  - Other tools are mostly cloud or OS specific
- It's very easy to use and lets you spin up a Kubernetes cluster in a few minutes
- Supports bootstrap tokens
  - These are simple tokens that can be used to create a cluster or to add nodes later on
  - The tokens are of the format `abcdef.0123456890abcdef`
- Kubeadm supports ubpgrading and downgrading of the cluster
- Doesn't install a Networking solution
  - You have to install a CNI compatible network solution yourself using `kubectl apply`.

# Prerequisites
- dep/rpm compatible system
- 2 GB of memory
- 2 CPUs for the master node
- Network connectivity between the nodes
  - Can be private network
  - or public routable IP addresses (in this case it is best to use a firewall to only allow access withing the cluster and to the users).
- Typically you need 2 nodes
  - One master node
  - and one worker node
  
# Demo
In this demo, I'll use Digital Ocean to spin up droplets which are VMs.
Digital Ocean is not as expensive as other providers.
I use Digital Ocean just to demonstrate that Kubeadm is not cloud provider dependent.

- A 2 GB memory droplets currently cost $10/month. But the systems are billed hourly.
- Use the link : https://m.do.co/c/007f99ffb902 for a $10 free credit.
- Once you're in the above link, sign-up to get the credit.
- Once you login, select Droplets (create cloud servers) and select a Kubeadm supported VM, for example Ubuntu.
- Select a VM with 2 GB of RAM
- Choose a datacenter closest to you.
- Add a new SSH key
- Create 2 droplets and give them the following hostname
  - kubernetes-master
  - kubernetes-node-01
- Add a tag `kubernetes-cluster`.
- While the Droplets are being created, go to Networking tab and create a new Firewall:
  - name: kubernetes-cluster
  - Inbound rules:
    - All TCP - TCP - All ports - My IP (get it from Web)
    - All TCP - TCP - All ports - kubernetes-cluster
    - All UDP - UDP - All ports - kubernetes-cluster
  - Apply to Droplets: `kubernetes-cluster`.
- Create the Firewall.
Next we will install Kubeadm and spin up the K8s cluster
- SSH into each of the Droplets using `ssh <IP> -l root -i ~/.ssh/<SSH FILE>`
- Git clone this repo and check the `install-kubernetes.sh` script
- Execute the `install-kubernetes.sh` script on the Master node.
- While the above script is running, execute the `install-node.sh` script on the worker node.
- Once the script execute successfully, we have to create a user and perform the additional steps specified in the output.
- Before we add a user, copy the `kubeadm join` command from the output to add the worker node to the cluster.
- Notice the format of the token generated.
- Copy and paste this command in the worker node to join the node to the cluster.
- Now execute the `create-user.sh` script to create the necessary users.
- Once the user is created, `logout` and login as the `ubuntu` user which was created as part of the `create-user.sh` script using `ssh -l ubuntu` command.
- Repeat the above step for the workder node as well, except for creating the `kubeconfig` file.
- Login to the worker node using the `ubuntu` user.
- Once logged-in execute `kubectl get nodes` to make sure the cluster is created correctly.
- Sanity test the new cluster by executing `kubectl run helloworld --image=k8s.gcr.io/echoserver:1.4 --port 8080` and run `kubectl get pods`.
- Expose the above POD using a NodePort service using `kubectl export deployment helloworld --type=NodePort` and run `kubectl get svc`
- Run `curl <MASTER_IP>:<NODE_PORT>` to check the helloworld deployment is accessible outside the cluster.
