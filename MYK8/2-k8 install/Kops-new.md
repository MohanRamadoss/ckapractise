# Setting up Kubernetes on AWS
## Cloud Setup
- To setup Kubernetes on AWS, we will use a tool called Kops
  - KOPS stands for Kubernetes Operations
- The tool allows you to do production grade kubernetes installations, upgrades and management.
- There is also a legacy tool called kube-up.sh
  - This was a simple tool to create a cluster but it doesn't create a PRODUCTION level cluster.
- Kops only works on Mac/Linux
- If you're on windows, you'll need to create a VM first.
- You can use Vagrant to create a VM using Virtual box.
- Once downloaded, to boot up a new VM, just type this in your cmd or PowerShell
```
mkdir ubuntu
cd ubuntu
vagrant init ubuntu/xenial64
vagrant up
```


## Preparing AWS for Kops installation
- Navigate to [GitHub Kops page](https://github.com/kubernetes/kops) and look for "Download the latest release"
- Copy the link to the latest version of Kops and execute `wget <link>`
- Once downloaded, provide execute permissions to the Kops executable using `chmod +x kops-linux-amd64`
- Move the kops executable to usr/bin using `mv kops-linux-amd64 /usr/local/bin`
- Install Python PIP as this will be used to setup AWS CLI later. Use the command `sudo apt-get install python-pip`
- To install AWS CLI, execute `sudo pip install awscli`.
- If you don't have an AWS account, create one and login to the console.
- Once logged in to the Console, navigate to IAM (Identity and Access Management) to create a new user.
- In IAM, create a new user:
  - Name: kops
  - Save the credentials generated.
- In the command line, configure AWS CLI as below:
  - execute `aws configure`
  - Provide the access key
  - Provide the secret key
- Assign Admin access to the newly created user by navigating to IAM again, and select the user from the users list and attach "AdministratorAccess" policy to the user.
- Next navigate to S3 in AWS, and create a new Bucket. This will be used to store kops state.
  - Bucket name: kops-state-<SOME RANDOM STRING>
  - Region: <Your preference>
- Navigate to Route53 to create the following:
  - A domain name
  - A hosted Zone
- To create a free domain name, follow the steps below:
```
Free DNS Service
Sign up at http://freedns.afraid.org/

Choose for subdomain hosting

Enter the AWS nameservers given to you in route53 as nameservers for the subdomain

http://www.dot.tk/ provides a free .tk domain name you can use and you can point it to the amazon AWS nameservers

Namecheap.com often has promotions for tld’s like .co for just a couple of bucks (preferred)
```
- Once you have a domain name, create a hosted zone as below:
  - Navigigte to Route53 and select Hosted Zones
  - Click on "Create Hosted Zone"
  - Domain name: kubernetes.<DOMAIN NAME> (for example kubernetes-charter.com)
  - Click next.
  - Now we need to use the NS values to configure our domain.
  - If you used NameCheap follow the steps below:
    - Select "Advanced DNS"
    - Add a new NS record corresponding to each vaule from Route53.
  - Save the record set.
  - To make sure everything is setup correctly, execute `host -t NS kubernetes.<DOMAIN NAME>`. This should list the NS server that were shown on Route53
- This completes the preparation to use Kops.


## Cluster Setup on AWS using Kops
- Install kubectl using the following links:
```
Download Kubectl
Linux: https://storage.googleapis.com/kubernetes-release/release/v1.17.0/bin/linux/amd64/kubectl

MacOS: https://storage.googleapis.com/kubernetes-release/release/v1.17.0/bin/darwin/amd64/kubectl

Windows: https://storage.googleapis.com/kubernetes-release/release/v1.17.0/bin/windows/amd64/kubectl.exe

Or use a packaged version for your OS: see https://kubernetes.io/docs/tasks/tools/install-kubectl/
```

- Once downloaded, move it to /usr/local/bin and provide executable permissions.
- before creating the cluster, we will have to create new SSH keys for using in the cluster instances.
- Use ssh-keygen to create new SSH keys.
- Create a K8s cluster using the command below

```
kops create cluster --name=kubernetes-charter.com --state=s3://kops-state-csks --zones=eu-west-1a --node-count=2 --node-size=t2.micro --master-size=t2.micro --dns-zone=kubernetes-charter.com
```
- This will output the resources that will be created in AWS for the purposes of the Cluster.
- in order to continue creating the cluster, execute the following:
```
kops update cluster kubernetes-charter.com --yes --state=s3://kops-state-csks
```
- The above command creates a config file. Use CAT command to inspect the config file.
- Execute `kubectl get nodes` to determine the status of the nodes.
- Sanity test the new cluster by executing `kubectl run helloworld --image=k8s.gcr.io/echoserver:1.4 --port 8080` and run `kubectl get pods`.
- Expose the above POD using a NodePort service using `kubectl expose deployment helloworld --type=NodePort` and run `kubectl get svc`
- Edit the Inbound policy for the Security group to allow communication from your IP to the Port exposed by the service above.
  - Custom TCP - TCP - My IP address
  - Save the SG
- Get the external IP address of any of the nodes.
- Run `curl <MASTER_IP>:<NODE_PORT>` to check the helloworld deployment is accessible outside the cluster.
- Clean up by deleting the cluster as below:
```
kops delete cluster --name kubernetes-charter.com --state=s3://kops-state-csks

kops delete cluster --name kubernetes-charter.com --state=s3://kops-state-csks --yes

```

