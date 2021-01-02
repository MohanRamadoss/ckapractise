# CKAD Practice Questions

## Environment variables
```bash
export RES_DEF="--dry-run=client -o yaml"
```

## Question 1

In namespace `pluto` there is a deployment named `project-23-api`. It has been working okay for a while but Team Pluto needs it to be more reliable. Implement a liveness-probe which checks the container to be reachable on port `80`. Initially the probe should wait `10`, periodically `15` seconds. The original deployment yaml is available `project-23-api.yaml`. Save your changes at `project-23-api-new.yaml` and apply the changes.

```bash
kubectl -n pluto get all -o wide

# We can use byrnedo/alpine-curl and curl to check if one pod is accessible on port 80:
kubectl run tmp --restart=Never --rm --image=byrnedo/alpine-curl -i <POD_IP>
# Or busybox and wget for this
kubectl run tmp --restart=Never --rm --image=busybox -i -- wget -O- <POD_IP>
# We can check the configured liveness-probe settings on a pod or the deployment
kubectl -n pluto describe pod <POD_NAME> | grep -i liveness
kubectl -n pluto describe deploy project-23-api | grep -i liveness
```

## Question 2

Team Sun needs a new deployment named `sunny` with `4` replicas of image `nginx:1.17.3-alpine` in namespace `sun`. The deployment and its pods should use the existing ServiceAccount `sa-sun-deploy`.

Expose the deployment internally using a ClusterIP service named `sun-srv` on port `9999`. The nginx containers should run as default on port `80`. The management of Team Sun would like to execute a command to check that all pods are running on occasion. Write that command into file `sunny_status_command.sh`. The command should use kubectl.

```bash
kubectl -n sun create deployment sunny --image=nginx:1.17.3-alpine $RES_DEF > sunny.yaml
# Edit the YAML file to match requirements
kubectl create -f sunny.yaml --save-config
# Create the service
kubectl expose deployment sunny --name=sun-srv --port=9999 -target-port=80 --type=ClusterIP
# Test the service
kubectl run tmp --restart=Never --rm --image=busybox -i -- wget -O- sun-srv.sun:9999
# Because the service is in a different namespace as our temporary pod, it is reachable using the names sun-srv.sun or fully: sun-srv.sun.svc.cluster.local.
echo 'kubectl -n sun get deployment sunny' > sunny_status_command.sh
```

## Question 3

Management of EarthAG recorded that one of their services stopped working. Dirk, the administrator, left already for the long weekend. All the information they could give you is that it was located in namespace `earth` and that it stopped working after the latest rollout. All services of EarthAG should be reachable from inside the cluster.

Find the service, fix any issues and confirm its working again. Write the reason of the error into file `ticket-654.txt` so Dirk knows what the issue was.

```bash
kubectl -n earth get all
kubectl -n earth get ep
echo 'yo Dirk, wrong port for readinessProbe defined!' > ticket-654.txt
```
