# Lightning Lab - 2

## Exercise 01

We have deployed a few pods in this cluster in various namespaces. Inspect them and identify the pod which is not in a `Ready` state. Troubleshoot and fix the issue. 
Next, add a check to restart the container on the same pod if the command `ls /var/www/html/file_check` fails. This check should start after a delay of `10 seconds` and run `every 60 seconds`.

> You may delete and recreate the object. Ignore the warnings from the probe.

```bash
$ kubectl get pods --all-namespaces
$ kubectl -n dev1401 get pod nginx1401 -o yaml > nginx1401.yaml
# Change readines port and add livenessProbe
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx
  name: nginx1401
  namespace: dev1401
spec:
  containers:
  - image: kodekloud/nginx
    imagePullPolicy: IfNotPresent
    name: nginx
    ports:
    - containerPort: 9080
      protocol: TCP
    readinessProbe:
      failureThreshold: 3
      httpGet:
        path: /
        port: 9080
        scheme: HTTP
      periodSeconds: 10
      successThreshold: 1
      timeoutSeconds: 1
    livenessProbe:
      exec:
        command:
        - ls
        - /var/www/html/file_check
      initialDelaySeconds: 10
      periodSeconds: 60
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: default-token-l5qd5
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  nodeName: node01
  priority: 0
  restartPolicy: Never
  schedulerName: default-scheduler
  securityContext: {}
  serviceAccountName: default
  terminationGracePeriodSeconds: 30
  tolerations:
  - effect: NoExecute
    key: node.kubernetes.io/not-ready
    operator: Exists
    tolerationSeconds: 300
  - effect: NoExecute
    key: node.kubernetes.io/unreachable
    operator: Exists
    tolerationSeconds: 300
  volumes:
  - name: default-token-l5qd5
    secret:
      defaultMode: 420
      secretName: default-token-l5qd5
```

## Exercise 02

Create a cronjob called `dice` that runs every one minute. Use the Pod template located at `/root/throw-a-dice`. The image throw-dice randomly returns a value between 1 and 6. The result of 6 is considered `success` and all others are `failure`. 
The job should be `non-parallel` and complete the task `once`. Use a `backoffLimit` of `25`.
If the task is not completed within `20 seconds` the job should fail and pods should be terminated.

> You don't have to wait for the job completion. As long as the cronjob has been created as per the requirements.

POD Template throw-a-dice

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: throw-dice-pod
spec:
  containers:
  -  image: kodekloud/throw-dice
     name: throw-dice
  restartPolicy: Never
```

`kubectl create cronjob dice --image=kodekloud/throw-dice --schedule="*/1 * * * *" --dry-run=client -o yaml > dice.yaml`

```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: dice
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      completions: 1
      activeDeadlineSeconds: 20
      backoffLimit: 25
      template:
        spec:
          containers:
          - name: throw-dice
            image: kodekloud/throw-dice
          restartPolicy: OnFailure
```

## Exercise 03

Create a pod called `my-busybox` in the `dev2406` namespace using the `busybox` image. The container should be called `secret` and should sleep for `3600` seconds.
The container should mount a `read-only` secret volume called `secret-volume` at the path `/etc/secret-volume`. The secret being mounted has already been created for you and is called `dotfile-secret`.
Make sure that the pod is scheduled on `master/controlplane` and no other node in the cluster.

```bash
$ kubectl run my-busybox --image=busybox --dry-run=client -o yaml --command sleep 3600 > busybox.yaml
# Help yourself with API
$ kubectl explain pods --recursive | less
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: my-busybox
  name: my-busybox
  namespace: dev2406
spec:
  nodeName: master
  containers:
  - command:
    - sleep
    - "3600"
    image: busybox
    name: secret
    volumeMounts:
    - name: secret-volume
      mountPath: "/etc/secret-volume"
      readOnly: true
  volumes:
  - name: secret-volume
    secret:
      secretName: dotfile-secret
  restartPolicy: Never
```

## Exercise 04

Create a single ingress resource called `ingress-vh-routing`. The resource should route HTTP traffic to multiple hostnames as specified below:

* The service `video-service` should be accessible on `http://watch.ecom-store.com:30093/video`

* The service `apparels-service` should be accessible on `http://apparels.ecom-store.com:30093/wear`

> Here `30093` is the port used by the `Ingress Controller`

`$ kubectl get service`

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
  name: ingress-vh-routing
  namespace: default
spec:
  rules:
  - host: watch.ecom-store.com
    http:
      paths:
      - path: /video
        backend:
          serviceName: video-service
          servicePort: 8080
  - host: apparels.ecom-store.com
    http:
      paths:
      - path: /wear
        backend:
          serviceName: apparels-service
          servicePort: 8080
```

## Exercise 05

A pod called `dev-pod-dind-878516` has been deployed in the `default` namespace. Inspect the logs for the container called `log-x` and redirect the `warnings` to `/opt/dind-878516_logs.txt` on the `master` node

```bash
$ kubectl describe dev-pod-dind-878516
$ kubectl logs dev-pod-dind-878516 -c log-x | grep WARNING > /opt/dind-878516_logs.txt
```

