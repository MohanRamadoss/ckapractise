# Practicing Skills

## Core Concepts

1. Using  only  the  allowed  browser,  URLs,  and  subdomains  search  for  and  bookmark  a  YAML  example  to  create  and
configure a basic pod. Ensure it works for the version of the exam you are taking. URLs may change, plan on checking
each book mark prior to taking the exam.
2. Using a timer and bookmarked YAML files see how long it takes you to create and verify. Try it again and see how much
faster you can complete and test each step:

    * A new pod with the nginx image. Showing all containers running and a Ready status.
    * A new service exposing the pod as a nodePort, which presents a working webserver configured in the previous step.
    * Update the pod to run the nginx:1.11-alpine image and re-verify you can view the webserver via a nodePort.

3. Use the `architecture-review1.yaml` file included. Determine if the pod is running. Fix any errors you may encounter.

## Observability & Multi-Container PODs

1. Using the three URL locations allowed by the exam, find and bookmark working YAML examples for LivenessProbes, ReadinessProbes, and multi-container pods.
2. Deploy a new nginx webserver. Add a LivenessProbe and a ReadinessProbe on port 80. Test that both probes and the webserver work.
3. Use the `build-review1.yaml` file to create a non-working deployment.  Fix the deployment such that both containers are running and in a `READY` state. The web server listens on port 80, and the proxy listens on port 8080.
4. View the default page of the web server. When successful verify the `GET` activity logs in the container log.

## Pod Design

1. Find and use the `design-review1.yaml` file to create a pod.
2. Determine the CPU and memory resource requirements of `design-pod1`.
3. Edit the pod resource requirements such that the CPU limit is exactly twice the amount requested by the container.
4. Increase the memory resource limit of the pod until the pod shows a `Running` status.  This may require multiple edits and attempts. Determine the minimum amount necessary for the `Running` status to persist at least a minute.
5. Use the `design-review2.yaml` file to create several pods with various labels.
6. Using only the –selector value `tux` to delete only those pods. This should be half of the pods. Hint, you will need to view pod settings to determine the key value as well.
7. Create a new cronjob which runs `busybox` and the `sleep 30` command.  Have the cronjob run every three minutes. View the job status to check your work.  Change the settings so the pod runs 10 minutes from the current time, every week. For example, if the current time was 2:14PM, I would configure the job to run at 2:24PM, every Monday.
8. Deploy the `stressmeout` container on your master node. This should cause it to run out of resources and the `kube-apiserver` fail to respond to kubectl commands.  Kill the container, delete the deployment before it can overwhelm the master and ensure the cluster recovers properly.

## Configuration & State Persistence & Deployments

1. Create a new secret called `specialofday` using the key `entree` and the value `meatloaf`.
2. Create a new deployment called `foodie` running the `nginx` image.
3. Add the `specialofday` secret to pod mounted as a volume under the `/food/` directory. 
4. Execute a bash shell inside a `foodie` pod and verify the secret has been properly mounted.
5. Update the deployment to use the `nginx:1.12.1-alpine` image and verify the new image is in use.
6. Roll back the deployment and verify the typical, current stable version of nginx is in use again.
7. Create a new 200M NFS volume called `reviewvol` using the NFS server configured earlier in the lab.
8. Create a new PVC called `reviewpvc` which will uses the `reviewvol` volume.
9. Edit the deployment to use the PVC and mount the volume under `/newvol`
10. Execute a bash shell into the nginx container and verify the volume has been mounted.

## Configuration

1. Create a new deployment which uses the `nginx` image.
2. Create a new `LoadBalancer` service to expose the newly created deployment. Test that it works.
3. Create a new NetworkPolicy called `netblock` which blocks all traffic to pods in this deployment only. Test that all traffic is blocked to deployment.
4. Update the `netblock` policy to allow traffic to the pod on port 80 only.  Test that you can access the default `nginx` web page.
5. Use the `security-review1.yaml` file to create a pod.
6. View the status of the pod.
7. After finding the errors, log into the container and find the proper id of the nginx user.
8. Edit the pod such that the securityContext is in place and allows the web server to read the proper configuration files.
9. Create a new `serviceAccount` called `securityaccount`.
10. Create a ClusterRole named secrole which only allows create, delete, and list of pods in all apiGroups.
11. Bind the clusterRole to the serviceAccount.
12. Locate the token of the securityaccount.  Create a file called `/tmp/securitytoken`.  Put only the value of token: `is equal` to, a long string that may start with eyJh and be several lines long. Careful that only that string exists in the file.

## Services & Networking

1. Create a new pod called `webone`, running the nginx service. Expose port 80.
2. Create a new service named `webone-svc`. The service should be accessible from outside the cluster.
3. Update both the pod and the service with selectors so that traffic for to the service IP shows the web server content.
4. Change the type of the service such that it is only accessible from within the cluster. Test that exterior access no longer works, but access from within the node works.
5. Deploy  another  pod,  called `webtwo`,  this  time  running  the `wlniao/website`image. Create another service, called `webtwo-svc` such that only requests from within the cluster work. Note the default page for each server is distinct.
6. Install and configure an ingress controller such that requests for webone.com see the `nginx` default page, and requests for webtwo.org see the `wlniao/website` default page.

## Troubleshooting

1. Use the `troubleshoot-review1.yaml` file to create a deployment. The `create` command will fail. Edit the file to fix issues such that a single pod runs for at least a minute without issue. There are several things to fix.


