# Practice Test - Rolling Updates and Rollbacks

## Rollout Commands

```bash
$ kubectl rollout status deployment/myapp-deployment
$ kubectl rollout history deployment/myapp-deployment
$ kubectl rollout undo deployment/myapp-deployment
$ kubectl apply f deployment-definition.yml
$ kubectl set image deployment/myapp-deployment nginx=nginx:1.9.1
```

## Recreate vs RollingUpdate Strategy

### Recreate 

is the simplest deployment strategy. A recreate deployment would go something like this. Version 1 of the app is deployed. Deployment starts, all pods running version 1 of the app are deleted. Immediately followed by version 2 of the application being deployed. The biggest draw back with recreate deployments is the small window of downtime.

### RollingUpdate

A ramped deployment updates pods in a rolling update fashion, a secondary ReplicaSet is created with the new version of the application, then the number of replicas of the old version is decreased and the new version is increased until the correct number of replicas is reached.

## Exercises


**Run the script named curl-test.sh to send multiple requests to test the web application. Take a note of the output.**

```shell
for i in {1..35}; do
   kubectl exec --namespace=kube-public curl -- sh -c 'test=`wget -qO- -T 2  http://webapp-service.default.svc.cluster.local:8080/info 2>&1` && echo "$test OK" || echo "Failed"'; echo ""
do
```

**Inspect the deployment and identify the number of PODs deployed by it**

```bash
$ kubectl describe deployment
# look at 'Desired Replicas'
```

**Upgrade the application by setting the image on the deployment to 'kodekloud/webapp-color:v2'**

```bash
$ kubectl set image deployment/frontend simple-webapp=kodekloud/webapp-color:v2
# or
$ kubectl edit deployment frontend
```

**Up to how many PODs can be down for upgrade at a time**

> Look at the Max Unavailable value under RollingUpdateStrategy in deployment details

**Change the deployment strategy to 'Recreate'**

>  Deployment Name: frontend, Deployment Image: kodekloud/webapp-color:v2, Strategy: Recreate

`$ kubectl edit deployment frontend`

**Upgrade the application by setting the image on the deployment to 'kodekloud/webapp-color:v3'**

`$ kubectl set image deployment/frontend simple-webapp=kodekloud/webapp-color:v3`
